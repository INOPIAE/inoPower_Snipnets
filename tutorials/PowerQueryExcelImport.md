# PowerQuery Import aus Excel steuern

## Arbeiten in Excel

In der Excel-Datei wird eine Tabelle „Import“ angelegt und mit den Spalten „Feld“ und „Wert“ beschriftet.

Tabelle anlegen. Hier wird das Feld Dateiname definiert. Bei Wert kommt später der gesuchte Dateiname hin.

Daten wie unten gezeigt in Excel eingeben und markieren.

![Screenshot Basistabelle](/sources/ExcelImportPQTabelle.png)

Dann über Start – Formatvorlagen – Als Tabelle formatieren – Design wählen.

![Screenshot Tabelle formatieren Dialog](/sources/ExcelImportPQTabelleDef.png)
 
Im nächsten Dialogfenster das Häkchen bei Tabelle hat Überschriften setzen.
 
Anschließend den Namen für die Tabelle auf Import setzen.
Dazu über Tabellenentwurf – Eigenschaften – Tabellenname den Namen auf Import ändern.

![Screenshot Tabelle umbennen](/sources/ExcelImportPQExcel.png)
 
## Arbeiten in PowerQuery

Über Daten – Daten abrufen und transformieren – Daten abrufen – Aus anderen Datenquellen – Leere Abfrage eine neue leere Abfrage anlegen.
 
![Screenshot Leere Abfrage](/sources/ExcelPQImport.png)

Im sich öffnenden PowerQuery-Fenster die Abfrage1 auswählen und über Start – Abfrage – Erweiterter Editor wird der unten aufgeführte Text reinkopiert.

![Screenshot Leere Abfrage bearbeiten](/sources/ExcelPQImportEditor.png)
 
```
(ParameterName as text) =>
let
    ParamSource = Excel.CurrentWorkbook(){[Name="Import"]}[Content],
    ParamRow = Table.SelectRows(ParamSource, each ([Feld] = ParameterName)),
    Value=
         if Table.IsEmpty(ParamRow)=true
         then null
         else Record.Field(ParamRow{0},"Wert")
in
     Value
```

![Screenshot Fertige Abfrage](/sources/ExcelImportPQAbfrage1.png)

Mit Fertig die Eingabe abschließen.

Im rechten Bereich der Abfrageeinstellungen den Namen der Abfrage auf fktImport ändern.

![Screenshot Abfrage1 umbenennen](/sources/ExcelImportPQUmbenennen.png)
 
Nun kann an anderen Stellen in PowerQuery auf die fktImport zugegriffen werden.

Als Beispiel in einer anderen Abfrage sehen die ersten 3 Zeilen dann so aus:

```
let
    Pfad=fktImport("Dateiname"),
    Quelle = Excel.Workbook(File.Contents(Pfad), null, true),
…
```

Der Begriff in der Klammer bei fktImport muss einem Eintrag der der Exceltabelle entsprechen.
