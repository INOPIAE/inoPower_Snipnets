# Datum Umwandeln

Manchmal klappt der automatische Umbau von einer Text- oder Zahlspalte nicht direkt in PowerQuery.

Mit der folgenden Funktion lässt sich häufig das Datum umwandeln.

```
(EingabeFlexiblesDatum as any) as nullable date =>
let
    alsText = Text.PadStart(Text.From(EingabeFlexiblesDatum), 8, "0"),
    d = Text.Start(alsText, 2),
    m = Text.Middle(alsText, 2, 2),
    y = Text.End(alsText, 4),
    Datum = try #date(Number.FromText(y), Number.FromText(m), Number.FromText(d)) otherwise null
in
    Datum
```

## Erstellen einer eigene Funktion

Zunächst wird in PowerQuery eine leere Abfrage angelegt.

![Screenshot Leere Abfrage](/sources/LeereAbfrage.png)

Danach wird im Ribbon Start der erweiterte Editor geöffnet.

![Screenshot Erweiterter Editor öffnen](/sources/KalenderErweiterterEditor.png)

![Screenshot Neuer Erweiterter Editor](/sources/LeererEditor.png)

Nun wird der Code einfach rein kopiert.

Nun wird die Abfrage auf DatumAusText umbenannt.

![Screenshot Abfrage umbennenen](/sources/AbfrageUmbennen.png)

## Nutzen der Funktion

Um die Funktion zu nutzen wird im Ribbon Spalte hinzufügen Benutzerdefinierte Funktion aufrufen angewendet.

![Screenshot Benutzerdefinerte Funktion](/sources/BenutzerdefinierteFunktion.png)

Ein Beispiel findet sich in der Datei [DatumUmwandeln.xlsx](/samples/DatumUmwandeln.xlsx).
