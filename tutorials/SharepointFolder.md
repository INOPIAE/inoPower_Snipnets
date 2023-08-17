# Daten aus einen Sharepoint-Ordner abrufen

Das einbinden eines Sharepoint-Ordners ist mit dem Assistenten recht hackelig.

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
