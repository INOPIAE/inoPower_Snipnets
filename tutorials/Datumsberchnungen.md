# Datumsberechnungen


## Umbau I

```
let
    Quelle = Excel.CurrentWorkbook(){[Name="AusgangI"]}[Content],
    DatentypenAnpassen = Table.TransformColumnTypes(Quelle,{{"Fiskalmonat", type text}, {"Datum", type date}, {"Projekt", type text}, {"Wert", Int64.Type}}),
    FiskalmonatDuplizieren = Table.DuplicateColumn(DatentypenAnpassen, "Fiskalmonat", "MonatUmbau"),
    DuplikatAufspalten = Table.SplitColumn(FiskalmonatDuplizieren, "MonatUmbau", Splitter.SplitTextByDelimiter("/", QuoteStyle.Csv), {"MonatUmbau.1", "MonatUmbau.2"}),
    DatentypentAnpassenI = Table.TransformColumnTypes(DuplikatAufspalten,{{"MonatUmbau.1", Int64.Type}, {"MonatUmbau.2", Int64.Type}}),
    MonatUmrechnen = Table.AddColumn(DatentypentAnpassenI, "MonatUmgewandelt", each if [MonatUmbau.1] < 10 then [MonatUmbau.1] + 3 else [MonatUmbau.1] -9),
    JahrUmrechnen = Table.AddColumn(MonatUmrechnen, "JahrUmgewandelt", each if [MonatUmbau.1] < 10 then [MonatUmbau.2] - 1 else [MonatUmbau.2]),
    MonatsdatumErstellen = Table.AddColumn(JahrUmrechnen, "Monatsdatum", each #date([JahrUmgewandelt],[MonatUmgewandelt],1)),
    MonatFormatieren = Table.TransformColumnTypes(MonatsdatumErstellen,{{"Monatsdatum", type date}}),
    Aufräumen = Table.RemoveColumns(MonatFormatieren,{"MonatUmbau.1", "MonatUmbau.2", "MonatUmgewandelt", "JahrUmgewandelt"})
in
    Aufräumen
```

https://www.thebiccountant.com/2018/02/15/generic-switch-function-query-editor-in-power-bi-and-power-query/

```
let func =  
 (Expression as any, Values as list, Results as list, optional Else as any) =>

try Results{List.PositionOf(Values, Expression)} 
otherwise if Else = null then "Value not found" 
                         else Else
, documentation = [
Documentation.Name =  " M.Switch
", Documentation.Description = " Evaluates an  <code>Expression</code>  against a list of  <code>Values</code>  and returns one or multiple possible  <code>Results</code>  expressions.
" , Documentation.LongDescription = " Evaluates an  <code>Expression</code>  against a list of  <code>Values</code>  and returns one or multiple possible  <code>Results</code>  expressions.
", Documentation.Category = " Other
", Documentation.Source = " https://wp.me/p6lgsG-Oa .
", Documentation.Author = " Imke Feldmann: www.TheBIccountant.com .
", Documentation.Examples = {[Description =  " see: https://wp.me/p6lgsG-Oa . 
" , Code = " M.Switch(2, {1..3}, {""January"", ""February"", ""March""}, ""Unknown month number"")
 ", Result = " ""February""
"]}] 
 in 
  Value.ReplaceType(func, Value.ReplaceMetadata(Value.Type(func), documentation)) 
```

## Umbau II

```
let
    Quelle = Excel.CurrentWorkbook(){[Name="AusgangII"]}[Content],
    DatentypenAnpassen = Table.TransformColumnTypes(Quelle,{{"Monat", type text}, {"Datum", type datetime}, {"Projekt", type text}, {"Wert", Int64.Type}}),
    MonatDuplizieren = Table.DuplicateColumn(DatentypenAnpassen, "Monat", "MonatUmbau"),
    DuplikatAufspalten = Table.SplitColumn(MonatDuplizieren, "MonatUmbau", Splitter.SplitTextByDelimiter(" ", QuoteStyle.Csv), {"MonatUmbau.1", "MonatUmbau.2"}),
    DatentypentAnpassenI = Table.TransformColumnTypes(DuplikatAufspalten,{{"MonatUmbau.1", type text}, {"MonatUmbau.2", Int64.Type}}),
    MonatUmrechnen = Table.AddColumn(DatentypentAnpassenI, "MonatUmgewandelt", each Switch([MonatUmbau.1], {"Januar", "Februar", "März", "April", "Mai", "Juni", "Juli", "August", "September", "Oktober", "November", "Dezember"}, {1..12}, "unbekannt")),
    MonatsdatumErstellen = Table.AddColumn(MonatUmrechnen, "Monatsdatum", each #date([MonatUmbau.2],[MonatUmgewandelt],1)),
    MonatFormatieren = Table.TransformColumnTypes(MonatsdatumErstellen,{{"Monatsdatum", type date}}),
    Aufräumen = Table.RemoveColumns(MonatFormatieren,{"MonatUmbau.1", "MonatUmbau.2", "MonatUmgewandelt"})
in
    Aufräumen
```
