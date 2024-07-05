# Bilder in Power BI nutzen

Um in Power BI Bilder nutzen zu können, müssen diese entweder auf einem öffentlich zugänglichen Webserver liegen oder als base64 kodierter Text vorliegen.

Dazu wid die Bild-Datei in base64 Code umgewandelt und vor den Code wird dieses Präfix gesetzt. 

`data:image/jpg;base64,`

Wobei der Bild-Datentyp entsprechend angepasst werden muss.

Bei der Darstellung in Power BI wird die Spalte, die den Bild-Code enthält, mit der Datenkategorie Bild-URL formatiert.

![Screenshot Bild-URL](/sources/powerbi_bild_url.png)

Ist die Bildinformation länger als 32.768 Zeichen, so wird das Bild nicht komplett dargesellt.

![Screenshot Bild mit Fehler](/sources/powerbi_bild_fehler.png)

In diesem Fall muss die Datenspalte in Power Query aufgeteilt werden und als später in Power BI als Measure wieder zusammengesetzt werden.

Dazu wird die Spalte in Power Query mit dem Befehl `Transformieren - Textspalte - Spalte Teilen - nach Anzahl von Zeichen` geteilt.

![Screenshot Spalte teilenL](/sources/powerbi_spalten_teilen.png)

Hier wird die Anzahl der Zeichen auf 30.000 eingegeben und in den Erwiterten Option Aufteilen in Spalten ausgewählt.

Anschließen wird noch einen Idexspalte beginnend von 0 hinzugefügt.

In Power BI wird dann ein Measure zum Verbinden der Bildzeilen erstellt.

```
Bild = CONCATENATEX('bilder erw','bilder erw'[Base64],,'bilder erw'[Index],ASC)
```

Diese Measure wird auch die Datenkategorie Bild-URL zugewiesen.

![Screenshot Bild ohne Fehler](/sources/powerbi_bild.png)



## Beispiel

Die einzelnen Schritte sowie das fertige Ergebnis sind in der [Bilder.pbix](/samples/Bilder.pbix) nachvollziehbar.

Ggf. müssen noch die Datei [bilder.json](/samples/Bilder/bilder.json) geladen werden und in Power Query der Power BI -Datei der Datenpfad angepasst werden.

## Bilder


Foto Hund von [Baptist Standaert](https://unsplash.com/de/@baptiststandaert?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)auf [Unsplash](https://unsplash.com/de/fotos/langhaariger-schwarz-weisser-hund-tagsuber-mx0DEnfYxic?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

Foto Elefant von [Will Shirley](https://unsplash.com/de/@willshirley?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) auf [Unsplash](https://unsplash.com/de/fotos/schwarzer-elefant-lauft-auf-braunem-sand-xRKcHoCOA4Y?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)
