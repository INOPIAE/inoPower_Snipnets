# Power BI Visuals dynamisch einblenden oder ausblenden

Zur Zeit können Visuals in Power BI nicht dynamisch ein- oder ausgeblendet werden. Allerdings kein man über das betreffende Visual eine Form-Visual legen, bei dem die Füllfarbe dynamisch angepasst werden kann. Dazu wird die Füllfarbe von Transparent 0 zu Transparent 100 geändert, was bedeutet, dass die Fläche sichtbar oder durchsichtig ist.

Dazu werden zwei Measures benötigt. Das Erste definiert die Bedingung definiert, die erfüllt sein muss, um die das Transparenz zu steuern. Das Zweite stellt die Transparenz ein.


## Measure zum Steuern der Anzeige
```
Hinweis = IF(COUNTROWS(ALLSELECTED(Nordwind[Kategoriename]))>3,"Es dürfen max. 3 Kategorien ausgewählt werden.",BLANK())
```

In dem Beispiel wird festgelegt, das wenn mehr als 3 Katgegorien ausgewählt wurden, ein anderes Visual ausgeblendet wird.

## Measure zum Steuern der Transparenz

```
Hintergrund dynamisch = "#FFFFFF" & IF ( [Hinweis] == BLANK(), "00" )
```
Im ersten 

## Umsetzung der Dynamik im Form-Visual

Das Form-Visual wird über dem auszublendenden Bereich postitioniert.

Im Bereich der Formatierung wird der Punkt Form - Formenart - Ausfüllen gewählt

![Screenshot Form Formatierung](/sources/EinblendenFormFormatieren.png)

Dann wird die Schaltfläche fx genutzt, um die bedingte Formatierung anzulegen.

![Screenshot Form bedingte Formatierung](/sources/EinblendenFormFuellfarbeFormatieren.png)

Hier wird der Formatstil Feldwert und das Measure als Feld ausgewählt.

## Beispiel

Die einzelnen Schritte sowie das fertige Ergebnis sind in der Datei [Nordwind.pbix](/samples/PowerBI/Dashboards/Nordwind.pbix) auf der Seite dynamischesEin- und Ausblenden nachvollziehbar.

