# Berechnung eines Meausres zur laufenden Summe von aktuellen und Forecast Werten in einem Measure

In diesem Tutorial wird das Measure in Power Pivot mit den drei Tabellen 
* Actuals mit Datum und den aktuellen Werten
* Forecast mit Datum und den Forecastwerten
* Kalendertabelle als Datumstabelle

entwickelt.

Zunächst wird ein Measure `Actuals` erstellt `Actuals:=SUM(Actuals[Wert])`.

Für das eigentliche Measure `YTD Gemischt` wird zunächst einen Variable `Total_actual` erstellt

```
var Total_actual =TOTALYTD([Actuals];Kalendertabelle[Datum])
```
In der Variable wird die Fortlaufende Summe von `Actuals` berechnet.

Als nächstes werden einige Varibalen für die zeitliche Zuordnung des Wechsels von Actual zu Forecast berechnet.

```
var LetztesDatum = MAXX(ALL(Actuals);Actuals[Datum])
var LetztesDatumMonat =MAX(Actuals[Datum])
var MonatsEnde = EOMONTH(LetztesDatum;0)
var AktuellesJahr=YEAR(Max(Kalendertabelle[Datum])) 
```
Die Variable `LetztesDatum` enthält den letzten Datumswert der Tabelle Actuals.

Die Variable `LetztesDatumMonat` enthält den letzten Datumswert für die aus dem Filter hervorgehenden Datums aus Actuals.

Die Variable `MonatsEnde` berechnet den letzten Tag des Monats aus `LetztesDatum`.

Die Variable `AktuellesJahr` berechnet Jahr aus `LetztesDatum`.

Im nächsten Schritt wird die Berechnung der fortlaufenden Summe des Forecasts definiert.

```
var Total_Forecast = SWITCH(MONTH(MonatsEnde);
        1; TOTALYTD([Forecast];Kalendertabelle[Datum]; "1/31");
        2; if(DAY(MonatsEnde)=28;
                TOTALYTD([Forecast];Kalendertabelle[Datum]; "2/28");
                TOTALYTD([Forecast];Kalendertabelle[Datum]; "2/29")
            );
        3; TOTALYTD([Forecast];Kalendertabelle[Datum]; "3/31");
        4; TOTALYTD([Forecast];Kalendertabelle[Datum]; "4/30");
        5; TOTALYTD([Forecast];Kalendertabelle[Datum]; "5/31");
        6; TOTALYTD([Forecast];Kalendertabelle[Datum]; "6/30");
        7; TOTALYTD([Forecast];Kalendertabelle[Datum]; "7/31");
        8; TOTALYTD([Forecast];Kalendertabelle[Datum]; "8/31");
        9; TOTALYTD([Forecast];Kalendertabelle[Datum]; "9/30");
        10; TOTALYTD([Forecast];Kalendertabelle[Datum]; "10/31");
        11; TOTALYTD([Forecast];Kalendertabelle[Datum]; "11/30");
        TOTALYTD([Forecast];Kalendertabelle[Datum])
    )
```

Da die Berechnung mit verschoben Enddaten für den Zeitraum der berechnung der Versatz nicht mit einer Varibaklen gesetzt werden kann, wird hier für die verschiedenen Monate definiert.

Im letzten Schritt wird die Auswertung für den aus den Filter resultierenden Bereichen erstellt.

```
var Total = IF(ISBLANK(LetztesDatumMonat);
	IF(Year(LetztesDatum)=AktuellesJahr;
		Total_actual+Total_Forecast; 
		TOTALYTD([Forecast];Kalendertabelle[Datum]));
	Total_actual)
```

Der komplette Code des Measures (im Syntax für Power Pivot, für Power BI muss das Semikolon durch ein Komma ersetzt werden.)

```
TYD Gemischt:=var Total_actual =TOTALYTD([Actuals];Kalendertabelle[Datum])

var LetztesDatum = MAXX(ALL(Actuals);Actuals[Datum])
var LetztesDatumMonat =MAX(Actuals[Datum])
var MonatsEnde = EOMONTH(LetztesDatum;0)
var AktuellesJahr=YEAR(Max(Kalendertabelle[Datum])) 

var Total_Forecast = SWITCH(MONTH(MonatsEnde);
        1; TOTALYTD([Forecast];Kalendertabelle[Datum]; "1/31");
        2; if(DAY(MonatsEnde)=28;
                TOTALYTD([Forecast];Kalendertabelle[Datum]; "2/28");
                TOTALYTD([Forecast];Kalendertabelle[Datum]; "2/29")
            );
        3; TOTALYTD([Forecast];Kalendertabelle[Datum]; "3/31");
        4; TOTALYTD([Forecast];Kalendertabelle[Datum]; "4/30");
        5; TOTALYTD([Forecast];Kalendertabelle[Datum]; "5/31");
        6; TOTALYTD([Forecast];Kalendertabelle[Datum]; "6/30");
        7; TOTALYTD([Forecast];Kalendertabelle[Datum]; "7/31");
        8; TOTALYTD([Forecast];Kalendertabelle[Datum]; "8/31");
        9; TOTALYTD([Forecast];Kalendertabelle[Datum]; "9/30");
        10; TOTALYTD([Forecast];Kalendertabelle[Datum]; "10/31");
        11; TOTALYTD([Forecast];Kalendertabelle[Datum]; "11/30");
        TOTALYTD([Forecast];Kalendertabelle[Datum])
    )

var Total = IF(ISBLANK(LetztesDatumMonat);
	IF(Year(LetztesDatum)=AktuellesJahr;
		Total_actual+Total_Forecast; 
		TOTALYTD([Forecast];Kalendertabelle[Datum]));
	Total_actual)

Return Total
```

## Beispiel

Das fertige Ergebnis ist in der [Beispieldatei YTD.xlsx](/samples/YTD.xlsx) nachvollziehbar. Es muss das Com-Addin PowerPivot für Excel aktiviert sein, um den Code unter Power Pivot - Verwalten auf der Tabelle Measures ansehen zu können.
