# PowerBI_notes
DAX for powerBI and "Mashup" Language for power query

### to map IDs to Names
```
= Table.AddColumn
  (Source, "LeaseStationName",
  each if [leasedByStation] = "BQongXZQwpYeRMoCr5lR" then "Bristol Sparks"
  else if [leasedByStation] = "mrj5ldavL6k8MBCpRY9U" then "Sherman Theatre"
  else if [leasedByStation] = "LfAnuhW4wOdspLcaCTkg" then "KIN+ILK The Maltings"
  else if [leasedByStation] = null then "NA" else "error"
)
//[columnName] = 'value' replace with 'new value'

```


### to append tables
```
= Table.Combine
  (
    {Leases, #"Pending Returns", Returns}
)
//{table names}
```

### to remove columns
```
= Table.RemoveColumns
  (Source,
    {"chargeId", "chargeTimestamp", "returnStation", "leasedByStation", "returnPendingStation", "vendor"}
)
//{column names}
```

### to re-order columns
```
= Table.ReorderColumns
  (#"Removed Columns",
    {"id", "asset", "customer",
    "LeaseStationName", "leasedTimestamp",
    "ReturnPendingStationName", "returnPendingTimestamp",
    "ReturnStationName", "returnTimestamp",
    "type", "leaseLengthDays"}
)
```

### to replace 'null' with 'NA'

```bash
 = Table.ReplaceValue(Custom1,null,"NA",Replacer.ReplaceValue,{"returnPendingTimestamp","returnTimestamp"})
```


### to create a stocklevel table
```
let
    // Step 1: Load the source table
    Source = Stock_LPR_combine,

    // Step 2: Add Stock Outflow Column (Lease)
    LeaseOutflow = Table.AddColumn(Source, "StockOut", each 1, type number),

    // Step 3: Add Stock Inflow Column (Return)
    ReturnInflow = Table.AddColumn(LeaseOutflow, "StockIn", each if [ReturnStationName] <> null then 1 else 0, type number),

    // Step 4: Transform LeasePlace and ReturnPlace into Key Columns
    Leases = Table.Group(LeaseOutflow, {"LeaseStationName"}, {{"LeasedStock", each List.Sum([StockOut]), type number}}),
    Returns = Table.Group(ReturnInflow, {"ReturnStationName"}, {{"ReturnedStock", each List.Sum([StockIn]), type number}}),

    // Step 5: Merge Lease and Return Data into Stock Levels Table
    Combined = Table.Join(Leases, "LeaseStationName", Returns, "ReturnStationName", JoinKind.FullOuter),

    // Step 6: Fill Missing Values with Zero
    FinalTable = Table.ReplaceValue(Combined, null, 0, Replacer.ReplaceValue, {"LeasedStock", "ReturnedStock"}),
    // Step 7: Calculate Net Stock
    StockLevel = Table.AddColumn(FinalTable, "NetStock", each [ReturnedStock] - [LeasedStock], type number)
in
    StockLevel
```
