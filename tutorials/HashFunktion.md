# Hash-Funktionen um Daten zu anonymisieren

Es gibt immer mal die Notwendigkeit Daten vor der Weiterverarbeitung in Excel oder Power BI zu anonymisieren, damit die Nutzer der Daten nicht auf die originalen Daten rückschlißen können. Dazu können Hash-Funktionen genutzt werden. 

Zur Zeit steht in Powerquery keine Hash-Funktion zur Verfügung aber es kann eine eigene Hash-Funktion erstellt werden.

## Erstellen der Hash-Funktion

In PowerQuery wird zunächst eine Leere Abfrage im Ribbon Start angelegt.

![Screenshot Leere Abfrage](/sources/LeereAbfrage.png)

Danach wird im Ribbon Start der erweiterte Editor geöffnet.

![Screenshot Erweiterter Editor öffnen](/sources/KalenderErweiterterEditor.png)

![Screenshot Neuer Erweiterter Editor](/sources/LeererEditor.png)

Nun wird der folgende Code in den erweiterten Editor eingefügt. (Hinweis: nach dem Kopieren des Codes zunächst den Code in einen beliebigen Text-Editor (Word, Editor, Notepad ...) einfügen und von dort nochmals kopieren, um störende Formatierungen aus der Webseite zu entfernen)

```
(x as any) as text => 

let 
    input = 
        //Datentypen auf Text konvertieren
        if Type.Is(Value.Type(x), Null.Type) then " "
            else if Type.Is(Value.Type(x), Number.Type) then Number.ToText(x)
            else if Type.Is(Value.Type(x), DateTime.Type) then DateTime.ToText(x)
            else x,
    hash = Binary.ToText(
        Binary.FromList(
            List.FirstN(
                List.LastN(
                    Binary.ToList(
                        Binary.Compress(Text.ToBinary(input, BinaryEncoding.Base64), Compression.GZip)
                    ),
                8), 
            4)
        ),
    BinaryEncoding.Hex)
    
in hash
```

Nun wird die Abfrage auf HashFunktion umbenannt.

![Screenshot Abfrage umbennenen](/sources/AbfrageUmbennen.png)

## Nutzen der Hash-Funktion

Um die Hash-Funktion zu nutzen wird im Ribbon Spalte hinzufügen Benutzerdefinierte Funktion aufrufen angewendet.

![Screenshot Benutzerdefinerte Funktion](/sources/BenutzerdefinierteFunktion.png)

Hier wird nun bei Funktionsabfrgae die HashFunktion ausgewählt bei x die Spalte ausgewählt, deren Daten gehashed werden sollen.

![Screenshot Nutzen der Hash-Funktion](/sources/HashFunktion.png)

Die einzelnen Schritte sowie das fertige Ergebnis sind in der [Beispieldatei HashFunktion.xlsx](/samples/HashFunktion.xlsx) nachvollziehbar.

Ausgangspunkt für diese Funktion war dieser Blogbeitrag 
[https://community.powerbi.com/t5/Desktop/Privacy-Hashing-of-keys/m-p/110574](https://community.powerbi.com/t5/Desktop/Privacy-Hashing-of-keys/m-p/110574)


