# Adressen für Serienbrief mit Tabulator und Zeilenumbruch vorbereiten 

Ziel ist es Adressdaten für einen Serienbrief so aufzubereiten, dass mehrere Spalten mit einem Tabulator zusammengefasst werden und danach mehrer Datensätze eines Kunden zusammengefasst werden, wobei die Teildatensätze durch einen Zeilenumbruch getrennt werden sollen.

Zuerst werden die Daten in Power Query importiert.

Dann werden die Spalten, die zusammengefasst werden sollen, ausgewählt und mit einem Rechtsklick auf den Spaltenkpf zusammengefasst.

![Screenshot Rechtsklick Spalten zusammenführen](/sources/Adr_Spaltenzusammen.png)

Dann wird als Trennzeichen der Tabulator gewählt und ein Spaltenname vergeben.

![Screenshot Dialog Spalten zusammenführen](/sources/Adr_SpaltenTab.png)

Nun werden die Daten mit dem Befehl `Start - Transformieren - Gruppieren nach` ausgewählt.

![Screenshot Dialog Gruppieren nach](/sources/Adr_Gruppieren.png)

Dabei wird der Vorgang `Alle Zeilen` ausgewählt.

Für den nächsten Schritt muss die Anpassung in der Bearbeitungsleiste von Power Query erfolgen.

![Screenshot Berabeitungsleiste](/sources/Adr_Code1.png)

Im bestehenden Code :

```
= Table.Group(#"Zusammengeführte Spalten", {"Kundennummer", "Kundenname", "Straße", "Ort"}, {{"LG final", each _, type table [Kundennummer=nullable text, Kundenname=nullable text, Straße=nullable text, Ort=nullable text, LG=text]}})
```
wird der Teil `each _, type table [Kundennummer=nullable text, Kundenname=nullable text, Straße=nullable text, Ort=nullable text, LG=text]` 
durch `each Text.Combine([LG], "#(lf)"), type text` ersetzt,
```
= Table.Group(#"Zusammengeführte Spalten", {"Kundennummer", "Kundenname", "Straße", "Ort"}, {{"LG final", each Text.Combine([LG], "#(lf)"), type text}})
```
Das wars.

Der finale Power Query Quellcode
```
let
    Quelle = Excel.CurrentWorkbook(){[Name="Tabelle2"]}[Content],
    ÜberschriftAnpassen = Table.TransformColumnTypes(Quelle,{{"Kundennummer", type text}, {"Kundenname", type text}, {"Straße", type text}, {"Ort", type text}, {"Liegenschaftsnummer", Int64.Type}, {"LAdresse", type text}, {"LOrt", type text}}),
    SpaltenZusammenfassenTab = Table.CombineColumns(Table.TransformColumnTypes(ÜberschriftAnpassen, {{"Liegenschaftsnummer", type text}}, "de-DE"),{"Liegenschaftsnummer", "LAdresse", "LOrt"},Combiner.CombineTextByDelimiter("#(tab)", QuoteStyle.None),"LG"),
    GruppierenLF = Table.Group(SpaltenZusammenfassenTab, {"Kundennummer", "Kundenname", "Straße", "Ort"}, {{"LG final", each Text.Combine([LG], "#(lf)"), type text}})
in
    GruppierenLF
```

Ein Beispiel findet sich in der Datei [AdresseSerienbrief.xlsx](/samples/AdresseSerienbrief.xlsx).
