# PowerQuery als Ersatz für SVerweis in Excel

Die Excel Funktionen SVerweis, WVerweis und XVerweis sind bei einer großen Anzahl von Daten sehr rechenintensiv und bremsen Excel aus. Ein Ersatz hierfür ist die Möglichkeit in PowerQuery Daten zusammen zu führen.

In der Beispieldatei werden aus der Tabelle Regionen die Information zu den Regionen in der Tabelle Nordwind2024 eingefügt werden. Dazu werden beide Tabellen in PowerQuery hinzugefügt.

![Screenshot Tabellenbereich zu PowerQuery hinzufügen](/sources/SVerweisAuswahl.png)

Anschließend wird werden die beiden Abfragen über Start - Kombinieren - Abfrage zusammmenführen - Abfragen als neue Abfrage zusammenführen verknüpft.

![Screenshot Abfragen kombinieren](/sources/SVerweisKombination.png)

Im Auswahlfenster werden die beiden Abfragen ausgewählt und in beiden Abfragen jeweils die Spalte ausgewählt, die für die Verknüpfung genutzt werden soll.

![Screenshot Zusammenführen](/sources/SVerweisZusammenfuehren.png)

Im Beispiel werden die beiden Spalten Land ausgewählt.

Es können immer nur Spalten vom gleichen Datentyp ausgewählt werden. Hierfür stehen nur diese Datentypen zur Verfügung:

- Text
- Ganzzahl
- Datum, nicht Datum/Uhrzeit

Des Weiteren muss noch definiert werden, wie die beiden Tabellen in Beziehung (JoinArt) stehen sollen.

![Screenshot Join-Arten](/sources/SVerweisJoin.png)

Hier gibt es diese Möglichkeiten die ggf. aus dem Datenbankdesign bekannt sein können:

* Linker äußerer Join - Alle Zeilen aus der oberen Tabelle  werden verwendet und wenn ein passender Match in der unteren Tabelle gefunden wird, wird dieser genutzt ansonsten bleibt der Wert leer.

* Rechter äußerer Join - Alle Zeilen aus der unteren Tabelle  werden verwendet und wenn ein passender Match in der oberen Tabelle gefunden wird, wird dieser genutzt ansonsten bleibt der Wert leer.

* Vollständiger äußerer Join - Es werden alle die Zeilen aus beiden Tabellen genutzt, ggf. werden leere Zeile aus der einen oder anderen Tabelle erzeugt.

* Inner Join - Es werden nur die Zeilen genutzt für die es in beiden Tabellen einen Match gibt.

* Linker Anti Join - Es werden die Zeilen oberen Tabelle angezeigt, für die es keinen Mtach in der unteren Tabelle gibt.

* Rechter Anti Join - Es werden die Zeilen unteren Tabelle angezeigt, für die es keinen Mtach in der oberen Tabelle gibt.

Die Begriffe Links und Rechts beziehen sich darauf, wenn man sich die Tabellen nebeneinander vorstellt.

Nach dem die Definition mit OK abgeschloßen wird wird einen neue Spalte am Ende der ersten Tabelle angezeigt.

![Screenshot Neue Spalte](/sources/SVerweisRegionTable.png)

Diese Spalte zeigt noch keine Daten an. Um diese Daten zu erhalten wird die Schaltfläche mit den beiden Pfeilen im Spaltenkopf angeklickt.

![Screenshot Abfragen kombinieren](/sources/SVerweisRegionTableAuswahl.png)

Nun werden die Saplten ausgewählt, die genutzt werden sollen. Es kann auch ein Präfix vor die neuen Spalten gesetzt werden.

Das fertige Ergebnis sieht dann so aus:

![Screenshot Abfragen kombinieren](/sources/SVerweisRegionSpalten.png)

Mit Start - Schließen & Laden wird PowerQuery geschlossen.

![Screenshot Schließen & Laden](/sources/Schliessen_Laden.png)

## Beispiel

Das fertige Ergebnis ist in der [SVerweisErsatz.xlsx](/samples/SVerweisErsatz.xlsx) nachvollziehbar. 
