# Erstellen einer Kalender mit ISO-Kalenderwochen

Für Auswertungen mit DAX-Funktionen in PowerPivot und Power BI wird eine Kalendertabelle benötigt, die für jeden Tag des auszuwertenden Zeitraumes einen Eintrag enthält.

Dazu wird in PowerQuery eine Kalenderwoche erstellt.

## Erstellen einer Datumsliste

Dazu wird eine leere Abfrage erstellt.

Aus Excel:

![Screenshot Erstellen aus Excel](/sources/KalenderExcelLeereAbfrage.png)

In PowerQuery:

![Screenshot Erstellen in PowerQuery](/sources/KalenderPowerQueryLeereAbfrage.png)

Zunächst wird dieser Code in die Bearbeitungszeile von PowerQuery eingeben:

```
 = List.Dates(#date(2018,1,1), 2000, #duration(1, 0, 0, 0))
```
![Screenshot Eingabe Code](/sources/KalenderListeErstellen.png)

Der gesamte Code für das Tabellenblatt **ListeErstellen**:

```
let
    Quelle = List.Dates(#date(2018,1,1), 2000, #duration(1, 0, 0, 0))
in
    Quelle
```

# Anpassen der Liste mit Startdatum und automatischem Ende

Um die Liste mit einem Startdatum zu versehen, wird ein Parameter erstellt.

![Screenshot Parameter erstellen](/sources/KalenderParameterAuswahl.png)

![Screenshot Parameter definieren](/sources/KalenderParameter.png)


Nachdem der Parameter Startdatum erstellt wurde, wird dieser in den Code eingefügt indem `#date(2018,1,1)` durch `Startdatum` ersetzt wird.

```
 = List.Dates(Startdatum, 2000, #duration(1, 0, 0, 0))
```

Als nächstes soll die Liste immer zum Ende des aktuellen Jahres enden. Dazu wird im Code `2000` durch `Duration.Days(#date(Date.Year(DateTime.FixedLocalNow()),12,31)-Startdatum)+1` ausgestauscht.

```
 = List.Dates(Startdatum, Duration.Days(#date(Date.Year(DateTime.FixedLocalNow()),12,31)-Startdatum)+1, #duration(1, 0, 0, 0))
```

Der gesamte Code für das Tabellenblatt **ListeErstellenDynamisch**:

```
let
    Quelle = List.Dates(Startdatum, Duration.Days(#date(Date.Year(DateTime.FixedLocalNow()),12,31)-Startdatum)+1, #duration(1, 0, 0, 0))
in
    Quelle
```

## Umbau zu Tabelle und Einfügen von Standardspalten

Nun wird die Liste zur Tabelle umbegaut.

![Screenshot Tabelle konvertieren](/sources/KalenderZuTabelleKonvertieren.png)

Im folgenden Dialog werden die Einstellungen einfach übernommen.

![Screenshot Tabelle konvertieren Dialog](/sources/KalenderZuTabelleKonvertieren2.png)

Dann wird die Spalte als Datum formatiert und in Datum umbenannt.

Jetzt werden alle nötigen Datumsspalten erstellt. Dazu wird die Datumsspalte ausgewählt und die entsprechende Spalte hinzugefügt.

![Screenshot Spalte Jahr hinzufügen](/sources/KalenderSpalteJahrHinzufuegen.png)

Es wird noch eine Spalte PeriondenID hinzugfügt, um später die Spalte Monat Jahr sortieren zu können.

![Screenshot Spalte PeriodenID hinzufügen](/sources/KalendarPeriodenID.png)


Der gesamte Code für das Tabellenblatt **TabelleErweitert**:

```
let 
    Quelle = List.Dates(Startdatum, Duration.Days(#date(Date.Year(DateTime.FixedLocalNow()),12,31)-Startdatum)+1, #duration(1, 0, 0, 0)),
    InTabelleKonvertiert = Table.FromList(Quelle, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    Datumsformat = Table.TransformColumnTypes(InTabelleKonvertiert,{{"Column1", type date}}),
    Datumsspalte = Table.RenameColumns(Datumsformat,{{"Column1", "Datum"}}),
    JahrEingefügt = Table.AddColumn(Datumsspalte, "Jahr", each Date.Year([Datum]), Int64.Type),
    MonatEingefügt = Table.AddColumn(JahrEingefügt, "Monat", each Date.Month([Datum]), Int64.Type),
    Monatsname = Table.AddColumn(MonatEingefügt, "Monatsname", each Date.MonthName([Datum]), type text),
    QuartalEingefügt = Table.AddColumn(Monatsname, "Quartal", each Date.QuarterOfYear([Datum]), Int64.Type),
    QuartalText = Table.AddColumn(QuartalEingefügt, "QuartalT", each "Q" & Number.ToText([Quartal])),
    QuartalTextÜberschrift = Table.TransformColumnTypes(QuartalText,{{"QuartalT", type text}}),
    MonatJahr = Table.AddColumn(QuartalTextÜberschrift, "Monat Jahr", each [Monatsname]& " " &Number.ToText([Jahr])),
    PeriodenID = Table.AddColumn(MonatJahr, "PeriodenID", each [Monat]+12*([Jahr]-2000)-12),
    PeriodenIDTyp = Table.TransformColumnTypes(PeriodenID,{{"PeriodenID", Int64.Type}}),
    KW = Table.AddColumn(PeriodenIDTyp, "Woche des Jahres", each Date.WeekOfYear([Datum]), Int64.Type),
    Wochentag_Zahl = Table.AddColumn(KW, "Tag der Woche", each Date.DayOfWeek([Datum]), Int64.Type),
    Wochentag = Table.AddColumn(Wochentag_Zahl, "Name des Tags", each Date.DayOfWeekName([Datum]), type text)
in
    Wochentag
```

## Einfügen der Berechnung der ISO-Kalenderwoche

Nun werden diese Codezeilen im Erweiterete Editor hinzugefügt.

```
    ISO_Jahr =Table.AddColumn(Wochentag,"ISO_Jahr", each Date.Year(Date.AddDays([Datum],3-Date.DayOfWeek([Datum],1))), type number),Hilfsdatum = Table.AddColumn(ISO_Jahr , "Hilfsdatum", each #date([ISO_Jahr],1,3)),
    ISO_KW = Table.AddColumn(Hilfsdatum ,"ISO_KW", each Number.IntegerDivide(Duration.Days( [Datum]-[Hilfsdatum])+Date.DayOfWeek([Hilfsdatum],0)+6,7), type number ),
    ISO_Jahr_KW = Table.AddColumn(ISO_KW, "ISO_Jahr_KW", each Text.Combine({Text.From([ISO_Jahr], "de-DE"), Text.From(Number.ToText([ISO_KW], "00"), "de-DE")}, "/"), type text),
    HilfsdatumEntfernen = Table.RemoveColumns(ISO_Jahr_KW,{"Hilfsdatum"}),
    ISO_Quartal = Table.AddColumn(HilfsdatumEntfernen, "ISO_Quartal", each if [ISO_KW]>39 then 4 else Number.RoundUp([ISO_KW]/13,0))
```

Der finale Code auf dem Tabellenblatt **Kalender**:

```
let 
    Quelle = List.Dates(Startdatum, Duration.Days(#date(Date.Year(DateTime.FixedLocalNow()),12,31)-Startdatum)+1, #duration(1, 0, 0, 0)),
    InTabelleKonvertiert = Table.FromList(Quelle, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    Datumsformat = Table.TransformColumnTypes(InTabelleKonvertiert,{{"Column1", type date}}),
    Datumsspalte = Table.RenameColumns(Datumsformat,{{"Column1", "Datum"}}),
    JahrEingefügt = Table.AddColumn(Datumsspalte, "Jahr", each Date.Year([Datum]), Int64.Type),
    MonatEingefügt = Table.AddColumn(JahrEingefügt, "Monat", each Date.Month([Datum]), Int64.Type),
    Monatsname = Table.AddColumn(MonatEingefügt, "Monatsname", each Date.MonthName([Datum]), type text),
    QuartalEingefügt = Table.AddColumn(Monatsname, "Quartal", each Date.QuarterOfYear([Datum]), Int64.Type),
    QuartalText = Table.AddColumn(QuartalEingefügt, "QuartalT", each "Q" & Number.ToText([Quartal])),
    QuartalTextÜberschrift = Table.TransformColumnTypes(QuartalText,{{"QuartalT", type text}}),
    MonatJahr = Table.AddColumn(QuartalTextÜberschrift, "Monat Jahr", each [Monatsname]& " " &Number.ToText([Jahr])),
    PeriodenID = Table.AddColumn(MonatJahr, "PeriodenID", each [Monat]+12*([Jahr]-2000)-12),
    PeriodenIDTyp = Table.TransformColumnTypes(PeriodenID,{{"PeriodenID", Int64.Type}}),
    KW = Table.AddColumn(PeriodenIDTyp, "Woche des Jahres", each Date.WeekOfYear([Datum]), Int64.Type),
    Wochentag_Zahl = Table.AddColumn(KW, "Tag der Woche", each Date.DayOfWeek([Datum]), Int64.Type),
    Wochentag = Table.AddColumn(Wochentag_Zahl, "Name des Tags", each Date.DayOfWeekName([Datum]), type text),
    ISO_Jahr =Table.AddColumn(Wochentag,"ISO_Jahr", each Date.Year(Date.AddDays([Datum],3-Date.DayOfWeek([Datum],1))), type number),Hilfsdatum = Table.AddColumn(ISO_Jahr , "Hilfsdatum", each #date([ISO_Jahr],1,3)),
    ISO_KW = Table.AddColumn(Hilfsdatum ,"ISO_KW", each Number.IntegerDivide(Duration.Days( [Datum]-[Hilfsdatum])+Date.DayOfWeek([Hilfsdatum],0)+6,7), type number ),
    ISO_Jahr_KW = Table.AddColumn(ISO_KW, "ISO_Jahr_KW", each Text.Combine({Text.From([ISO_Jahr], "de-DE"), Text.From(Number.ToText([ISO_KW], "00"), "de-DE")}, "/"), type text),
    HilfsdatumEntfernen = Table.RemoveColumns(ISO_Jahr_KW,{"Hilfsdatum"}),
    ISO_Quartal = Table.AddColumn(HilfsdatumEntfernen, "ISO_Quartal", each if [ISO_KW]>39 then 4 else Number.RoundUp([ISO_KW]/13,0))
in
    ISO_Quartal
```

## Beispiel

Die einzelnen Schritte sowie das fertige Ergebnis sind in der [Beispieldatei Kalender.xlsx](/samples/Kalender.xlsx) nachvollziehbar.