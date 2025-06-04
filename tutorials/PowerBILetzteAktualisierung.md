# Zeitpunkt der letzten Aktualisierung in Power BI

Power BI hat leider keine direkte Funktion um den aktuellen Zeitstempel der letzten Aktualisierung auszugeben.

Die kann aber über PowerQuery umgesetzt werden.

Dazu wird in PowerQuery eine neue Abfrage für eine neue Tabelle erstellt, die bei der Aktualisierung der Daten aufgerufen wird um den letzten Zeitpunkt auszugeben.

Es wird in PowerQuery eine [neue leere Abfrage](/tutorials/PowerQueryNeueAbfrage.md) erstellt.

Über den [Erweiterten Editor](/tutorials/PowerQueryErweiterterEditor.md) wird dieser Code eingefügt:

```
let
    Quelle = #table(type table[Date Last Refreshed=datetime], {{DateTime.LocalNow()}})
in
    Quelle
```

Die erstelle Tabelle enthält eine Spalte `Date last refreshed` mit dem aktuellen Zeitstempel.

Leider wird beim Veröffentlichen des Dashboard auf dem PowerBI Server dort nicht die lokale Zeit sondern immer UTC-Zeit zurückgegeben. 

Auch kennt PowerQuery nicht den Wechsel von Sommer- und Winterzeit.

Um diese Umstände zu beheben, wird in PowerQuery eine neue Funktion erstellt.

Es wird ein PowerQuery eine [neue leere Abfrage](/tutorials/PowerQueryNeueAbfrage.md) erstellt.

Über den [Erweiterten Editor](/tutorials/PowerQueryErweiterterEditor.md) wird dieser Code eingefügt:

```
() =>
let
    UtcNow = DateTimeZone.UtcNow(),

    Jahr = Date.Year(Date.From(UtcNow)),

    // Letzter Sonntag im März, Sommerzeit beginnt um 2:00 Uhr
    März31 = #date(Jahr, 3, 31),
    StartDST = Date.AddDays(März31, -Date.DayOfWeek(März31, Day.Sunday)) & #time(2,0,0),

    // Letzter Sonntag im Oktober, Sommerzeit endet um 3:00 Uhr
    Okt31 = #date(Jahr, 10, 31),
    EndDST = Date.AddDays(Okt31, -Date.DayOfWeek(Okt31, Day.Sunday)) & #time(3,0,0),

    IstDST = UtcNow >= DateTimeZone.From(StartDST) and UtcNow < DateTimeZone.From(EndDST),

    Offset = if IstDST then 2 else 1,

    LokaleZeit = DateTimeZone.SwitchZone(UtcNow, Offset),

    // Ausgabe ohne Zeitzoneninformationen (reiner DateTime-Wert)
    Ergebnis = DateTimeZone.RemoveZone(LokaleZeit)
in
    Ergebnis
```

Die Abfrege erhält noch den Namen fktAktuellerZeitstempel.

Nun kann die Funktion in erste Abfrage eingebaut werden.

Dazu wird der Code im [Erweiterten Editor](/tutorials/PowerQueryErweiterterEditor.md) entsprechend angepasst.

```
let
    Quelle = #table(type table[Date Last Refreshed=datetime], {{fktAktuellerZeitstempel()}})
in
    Quelle
```

oder alles in einer Abfrage

```
let
    UtcNow = DateTimeZone.UtcNow(),

    Jahr = Date.Year(Date.From(UtcNow)),

    // Letzter Sonntag im März, Sommerzeit beginnt um 2:00 Uhr
    März31 = #date(Jahr, 3, 31),
    StartDST = Date.AddDays(März31, -Date.DayOfWeek(März31, Day.Sunday)) & #time(2,0,0),

    // Letzter Sonntag im Oktober, Sommerzeit endet um 3:00 Uhr
    Okt31 = #date(Jahr, 10, 31),
    EndDST = Date.AddDays(Okt31, -Date.DayOfWeek(Okt31, Day.Sunday)) & #time(3,0,0),

    IstDST = UtcNow >= DateTimeZone.From(StartDST) and UtcNow < DateTimeZone.From(EndDST),

    Offset = if IstDST then 2 else 1,

    LokaleZeit = DateTimeZone.SwitchZone(UtcNow, Offset),

    // Ausgabe ohne Zeitzoneninformationen (reiner DateTime-Wert)
    Ergebnis = DateTimeZone.RemoveZone(LokaleZeit).

    Quelle = #table(type table[Date Last Refreshed=datetime], {{Ergebnis}})
in
    Quelle
```

