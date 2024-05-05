# Daten aus einen Sharepoint-Ordner abrufen

Das Einbinden eines Sharepoint-Ordners ist mit dem Assistenten recht hackelig.

Am einfachsten geht es über diesen Weg.

In PowerQuery eine Leere Abfrage erstellen.

Danach im Erweiterten Editor diese Zeile austauschen. Vorher muss noch die URL angepasst werden. **Wichtig**: die URL ist nur der Pfad zur Sharepoint Site nicht der Pfad bis zum Ordner.

``` 
let
    Quelle = SharePoint.Files("https://my.domain.tld/sites/MySite/", [ApiVersion = 15])
in
    Quelle
```

![Screenshot Erweiterter Editor](/sources/SPEditor1.png)

Es werden nun alle Dateien, die innerhalb der Sharepoint-Site angelegt sind, angezeigt.

![Screenshot Ansicht Dateien](/sources/SPGefiltert.png)

Diese werden nun entsprechend gefiltert. 
Es bieten sich die Spalten (Datei-)Name, Extension und Folder Path an.

Als nächstest wird auf das Sybol im Spaltenkopf der Spalte Content geklickt.

Abhängig vom Dateityp der ersten angezeigten Datei erscheint ggf. ein Auswahlfenster.

![Screenshot optionales Auswahlfenster ](/sources/SPWizzard.png)

Power Query generiert einige neue Elemente.

![Screenshot Erweiterter Editor](/sources/SPHilfsprogramme.png)

Nun muss die Abfrage Beispieldatei Transformieren so bearbeitet werden, dass alle notwendigen Aufbereitungsschritte umgesetzt werden.

Meistens muss zum Schluss noch der letzte Schritte aus der primären Abfrage gelöscht werden, da es hier zu Namenskolisionen kommt.

## Anpassen der Abfrage Beispieldatei

Standardmäßig wird die erste Datei der Dateiliste als Beispieldatei genommen.

Code aus dem Erweiteren Editor

``` 
let
    Quelle = SharePoint.Files("https://my.domain.tld/sites/MySite/", [ApiVersion = 15]),
    Filter = Table.SelectRows(Quelle, each ([Extension] = ".xlsx")),
    Navigation = Filter{0}[Content]
in
    Navigation
```

Wenn eine spezielle Datei genommen werden soll, muss der Pfad zur Datei angegeben werden:

Code aus dem Erweiteren Editor

``` 
let
    Quelle = SharePoint.Files("https://my.domain.tld/sites/MySite/", [ApiVersion = 15]),
    Filter = Table.SelectRows(Quelle, each ([Extension] = ".xlsx")),
    Navigation = Filter{[Name="00_PQ_Transformationen_052018_00.xlsx",#"Folder Path"="https://my.domain.tld/sites/MySite/Shared Documents/General/"]}[Content]
in
    Navigation
```

# Hinzufügen von Dateiinformationen zur Abfrage

Einige Dateiinformationen können aus dem Sharepoint gezogen werden, z.B das Änderungsdatum der Datei.

Dazu kann in einem beliebigen späteren Schritt auf den Schritt `Filter` zugeriffen werden.

Hier der Code das Änderungsdatum.

``` 
    Änderungsdatum = Table.AddColumn(#"Geänderter Typ", "Änderungsdatum", each List.Max(Filter[Date modified])),
    #"Geänderter Typ1" = Table.TransformColumnTypes(Änderungsdatum,{{"Änderungsdatum", type datetime}})
```
