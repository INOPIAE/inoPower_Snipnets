# Power BI: fehlende Monate darstellen

Standardmäßig werden Power BI nur die Datenpunkte angezeigt, zu denen Werte vorhanden sind. Datenpunkte mit Null-Werte werden nicht angezeigt.

Das folgende Tutorial zeigt, wie diese Werte trotzdem dargestellt werden können.

Im folgenden Screenshot wird der Weg mit den einzelnen Schritten aufgezeigt.

![Screenshot Diagramme](/sources/bi_null_diagramme.png)

## Oberstes Diagramm

Das oberste Diagramm zeigt nur die Monate mit Werten. Es fehlt der Monat Mai 2024.


## Zweites Diagramm

Im zweiten Diagramm werden die Elemente ohne Daten mit angezeigt. Dies wird erreicht, in dem für die X-Achse die Elemente ohne Daten angezeigt werden. 

Dazu wird mit der rechten Maustatste auf das Datenfeld geklickt.

![Screenshot Elemente ohne Daten anzeigen](/sources/bi_null_ohne_daten.png)

Nun werden alle Monate angezeigt. Allerdings wird im Mai 2024 eine Leerstelle angezeigt.

## Drittes Diagramm

Um dies zu umgehen, wird ein neues Measure gebaut, dass Null-Werte als 0 angezeigt werden.

```
Umsatz mit 0 = [Umsatz] + 0
```

Das dritte Diagramm zeigt das Ergebnis mit dem neuen Measure.

Hier werden allerdings auch die zukünftigen Monate angzeigt. (Es liegen nur Daten bis Juni 2024 vor.)

## Viertes Diagramm

Um dies zu bereinigen wird ein neues Measure erstellt.

```
Umsatz aktuell mit 0 = 
    var MaxDateAllFacts = CALCULATE(MAX(Nordwind[Bestelldatum]),REMOVEFILTERS())
    var MinDateDataPoint = MIN(Kalendertabelle[Datum])
    var MyMeasure = [Umsatz] + 0
    var Result = IF(MinDateDataPoint<=MaxDateAllFacts, MyMeasure, BLANK())

RETURN
    Result
```

Mit `MaxDateAllFacts` wird der größte Datumswert aus der Faktentabelle Nordwind berechnet.

Mit `MinDateDataPoint` wird der kleinste Datumswert aus der Kalendertabelle Nordwind bezogen auf den Filter aus dem Visual (hier Monat Jahr) berechnet. Es ist immer der 1. eines jeden Monats

Mit `Result` wird geprüft ob es `MinDateDataPoint` kleiner als `MaxDateAllFacts` ist. In desem Fall wird das Measure genutzt. Ansonsten wird null (Blank()) zurückgegeben. 

Das letzte Diagramm zeigt das finale Ergebnis.



## Beispiel

Die einzelnen Schritte sowie das fertige Ergebnis sind in der Datei [Nordwind.pbix](/samples/PowerBI/Dashboards/Nordwind.pbix) auf der Seite `Leere Monate füllen` nachvollziehbar.

Die Idee zu diesem Tutorial stammt aus diesem Video https://youtu.be/ctKZIM1Ci3s.
