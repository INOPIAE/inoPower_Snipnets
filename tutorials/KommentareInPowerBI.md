# Dynamische formatierte Kommentar in Power BI

Wenn Kommentare formatiert in Power BI dynamisch angezeigt werden sollen, müssen diese als Text zur Verfügung gestellt werden, der schon die Formatierung enthält.

Hier bieten sich unter anderem drei Varianten an:
- RTF (Rich Text Format), hier für benötigt man einen Programm das die Eingabe der Formatierung zulässt. (Excel, Sharepointlisten)
- HTML, hier sollte der HTML Syntax bekannt sein und die Eingabe muss die Regeln von HTML befolgen. Es gibt für die Ausgabe mehrer Visiuals, die die Ausgabe von HTML unterstützen.
- Markdown, ist eine relative einfache Auszeichnungsprache, die mit relativ wenig Formatierungsanweisungen auskommt. [Markdown bei wikipedia](https://de.wikipedia.org/wiki/Markdown), [Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)

## Aufbau der Daten
Die Daten der Kommentare werden in einer kleinen Tabelle angelegt. 

Diese sollte mindestens 2 Spalten enthalten:
- eine Spalte, die Verknüfungen zu den andern Daten der Auswertung ermöglicht. Dies kann einen ID-Spalte oder auch eine Datumspalte sein.
- eine Spalte für den Kommentar

Die Tabelle wird am besten in Excel oder als Sharepointliste angelegt.
In Sharepoint sollte das Kommentarfeld als mehrzeiliges Textfeld angelegt werden.

## Einbinden der Daten in Power BI
Die Daten werden in Power BI eingebunden und im Modell entsprechend verknüpft.

Es wird ein Visual importiert, das die ausgewählte Formatierungsart unterstützt.

Vorschläge:
- HTML: das HTML Content Visual aus dem Microsoft AppStore
- Markdown: [https://github.com/emil-eklund/markdown-visual](https://github.com/emil-eklund/markdown-visual)

## Beispielumsetzung

In der Beispielumsetzung [Kommentare.pbix](/samples/PowerBI/Dashboards/Kommentare.pbix) wird exmplarisch die Umsetzung mit Markdown aufgezeigt. Es werden monatliche Kommentar aus Excel [Kommentare.xlsx](/samples/PowerBI/Daten/Kommentare.xlsx) genommen und in Power BI dargestellt.

Die Daten in Excel:

![Screenshot Daten in Excel](/sources/KommentareDaten.png)

Die Darstellung in Power BI:

![Screenshot Bild ohne Fehler](/sources/KommentarePowerBI.png)

Die Beispieldateien sind oben verlinkt.

## Ausblick für weitere Möglichkeiten

Falls die Daten über eine Sharepointliste eingebunden werden, kann eine PowerApp, die auf die Sharepointliste zugreift, in Power BI eingebunden werden. Damit können die Kommentare dirkt inm Power BI Service einggeben werden.
