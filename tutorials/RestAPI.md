# REST-API Zugriff

Es soll hier exemplarisch der Zugriff auf eine Datenquelle über eine REST-API Schnittstelle am Beispiel der Starwars Datenbank [swapi.dev](https://swapi.dev/) gezeigt werden.

Es sollen alle hinterlegten Figuren abgerufen werden. Dazu wird unter Daten abrufen aus dem Web gewählt und die URL `https://swapi.dev/api/people/` eingegeben.

![Screenshot Webabfrage](/sources/RestUrl.png)

Nach OK wird der Zugriff auf den Website festgelegt.

Da die swapi ohne Zugriffsschutz erreichbar ist wird Anonym gewählt.

![Screenshot Zugriff Anonym](/sources/RestZugriff.png)

In den meisten Fällen muss ein Schlüssel, der in der RestAPI - Anwendung hinterlegt ist, eingegeben werden.
![Screenshot Zugriff mit Key](/sources/RestZugriffKey.png)


Mit Verbinden wird der Import angestoßen.

Da die API-Schnittstelle die Daten über mehrere Seiten verteilt zurück gibt, muss der Import angepasst werden.

Dazu wird der erweiterete Editor geöffnet und der bestehende Code

```
let
    Quelle = Json.Document(Web.Contents("https://swapi.dev/api/people/"))
in
    Quelle
```

durch diesen ersetzt.

```
let
    // Funktion zum Abrufen einer einzelnen Seite
    GetPage = (url as text) =>
        let
            Source = Json.Document(Web.Contents(url)),
            Results = Source[results],
            NextPage = Source[next]
        in
            [Results = Results, Next = NextPage],

    // Erste Seite
    FirstPage = GetPage("https://swapi.dev/api/people/"),

    // Alle Seiten abrufen
    AllPages = List.Generate(
        () => FirstPage,
        each [Results] <> null,
        each if [Next] <> null then GetPage([Next]) else [Results = null, Next = null],
        each [Results]
    ),

    // Listen zusammenführen
    CombinedResults = List.Combine(AllPages),

    // In Tabelle umwandeln
    TableFromList = Table.FromList(CombinedResults, Record.FieldValues, Record.FieldNames(CombinedResults{0}))
in
    TableFromList
```


Ein Beispiel findet sich in der Datei [RestSWAPI.xlsx](/samples/RestSWAPI.xlsx).
