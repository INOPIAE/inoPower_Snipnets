# Aus Anfangs- und Endedatum Monatseinträge erstellen

## Die Ausgangssituation

![Screenshot Ausgang](/sources/StartEndeAusgang.png)

Diese Liste soll pro Monat in dem das Projekt läuft einen Eintrag erhalten.

## Vorbereitende Arbeiten

Zunächst wird mit PowerQuery eine weitere Spalte EndeAufgefüllt erzeugt, die die fehlenden Einträge aus der Spalte Ende mit dem auktuellen Tagesdatum füllt.

```
    EndeBestimmen = Table.AddColumn(SpaltenFormateAnpassen, "EndeAufgefuellt", each if [Ende] = null then Date.From(DateTime.LocalNow()) else [Ende])
```

Anschließend wird die Spalte noch formatiert.

![Screenshot Zwischenschritt](/sources/StartEndeEndeAufgefuellt.png)

Der gesamte Code:

```
let
    Quelle = Excel.CurrentWorkbook(){[Name="Ausgangstabelle"]}[Content],
    SpaltenFormateAnpassen = Table.TransformColumnTypes(Quelle,{{"Projekt", type text}, {"Start", type date}, {"Ende", type date}}),
    EndeBestimmen = Table.AddColumn(SpaltenFormateAnpassen, "EndeAufgefuellt", each if [Ende] = null then Date.From(DateTime.LocalNow()) else [Ende]),
    EndeAufgefuelltFormatieren = Table.TransformColumnTypes(EndeBestimmen,{{"EndeAufgefuellt", type date}})
in
    EndeAufgefuelltFormatieren
```

## Die Aufteilung auf Monate

Die Aufteilung in die Monate erfolgt mit diesem Schritt. Die Inspitarion lieferte  dieses [Video](https://www.youtube.com/watch?v=tnjzIq7jinQ). Hierbei wird zunächst eine Liste erstellt.

```
  	ListeMitZerlegungInMonate = Table.AddColumn(EndeAufgefuelltFormatieren, "Monatsdatum", 
		each let
		
		StDatum = [Start],
		
		AllDates = {Number.From([Start])..Number.From([EndeAufgefuellt])},
		
		StMonatsdatum = 
			List.Distinct(
				List.Select(
					List.InsertRange(
						List.Transform(
							AllDates,
							each Date.StartOfMonth(Date.From(_))
						),
						0,
						{[Start]}
					),
					each Number.From(_) >= Number.From(StDatum)
				)
			)
		
		in
			StMonatsdatum)
```

Danach wird die Liste noch aufgelöst und formatiert.

![Screenshot Ergebnis](/sources/StartEndeFinal.png)

Der gesamte Code:

```
let
    Quelle = Excel.CurrentWorkbook(){[Name="Ausgangstabelle"]}[Content],
    SpaltenFormateAnpassen = Table.TransformColumnTypes(Quelle,{{"Projekt", type text}, {"Start", type date}, {"Ende", type date}}),
    EndeBestimmen = Table.AddColumn(SpaltenFormateAnpassen, "EndeAufgefuellt", each if [Ende] = null then Date.From(DateTime.LocalNow()) else [Ende]),
    EndeAufgefuelltFormatieren = Table.TransformColumnTypes(EndeBestimmen,{{"EndeAufgefuellt", type date}}),
  	ListeMitZerlegungInMonate = Table.AddColumn(EndeAufgefuelltFormatieren, "Monatsdatum", 
		each let
		
		StDatum = [Start],
		
		AllDates = {Number.From([Start])..Number.From([EndeAufgefuellt])},
		
		StMonatsdatum = 
			List.Distinct(
				List.Select(
					List.InsertRange(
						List.Transform(
							AllDates,
							each Date.StartOfMonth(Date.From(_))
						),
						0,
						{[Start]}
					),
					each Number.From(_) >= Number.From(StDatum)
				)
			)
		
		in
			StMonatsdatum),
    ListeAuflösen = Table.ExpandListColumn(ListeMitZerlegungInMonate, "Monatsdatum"),
    MonatsdatumFormatieren = Table.TransformColumnTypes(ListeAuflösen,{{"Monatsdatum", type date}})
in
    MonatsdatumFormatieren
```

## Beispiel

Die einzelnen Schritte sowie das fertige Ergebnis sind in der [Beispieldatei StartEnde.xlsx](/samples/StartEnde.xlsx) nachvollziehbar.