# Kohortenanalyse

Bei der Kohortenanalyse geht es darum Daten in dynamischen Bereichen zusammenzufassen und diese auszuwerten.

Als Beispiel soll die Kundentreue und der Umsatz der Kunden in Bezug auf ihre erste bestellung ausgewertet werden.

![Screenshot Neukundengeschäft](/sources/Neukundengeschäft.png)

Dazu wird eine neue Tabelle in PowerBI mit der DAX-Funktion [SUMMERIZE](https://learn.microsoft.com/de-de/dax/summarize-function-dax) erzeugt.

Dazu wird der folgende Code genutzt.

```
Neukunden = SUMMARIZE(
    Nordwind,
    Nordwind[Firma],
    "Erster Umsatz", MIN(Nordwind[Bestelldatum]),
    "Erstes Jahr", IF(
        VALUE(FORMAT(MIN(Nordwind[Bestelldatum]),"YYYY"))<=2020,
        2020,
        Value(FORMAT(MIN(Nordwind[Bestelldatum]),"YYYY"))
        ),
    "Erstes Jahr alle", FORMAT(MIN(Nordwind[Bestelldatum]),"YYYY")
)
```

Zuerst wird die Basistabelle `Nordwind` definiert.

Danach werden Angaben gemacht wie die Daten zusamengefasst (gruppiert) werden sollen. Im Beispiel nach der Spalte `Nordwind[Firma]`. es können aber auch weitere Parameter angegeben werden.

Zum Abschluss werden die Berechnungen angebene. Diese bestehen immer aus 2 Angaben. Zuerst wird der Name der Spalte und dann die Berechnung für die Spalte angegeben. Im  Beispiel werden drei Berechnungen angegeben.

Für die Auswertung werden die beiden Tabellen `Nordwind` und `Neukunden` in der Modellansicht über die Spalten Firma miteinander verknüpft.

## Beispiel

Das fertige Ergebnis ist in der [Nordwind.pbix](/samples/PowerBI/Dashboards/Nordwind.pbix) nachvollziehbar. 

Die Beispieldatei ist mit der PowerBI Version April 2024 erstellt worden. 

Zur Anpassung der Datenquelle gibt es in der Beispieldatei einen Hinweis.
