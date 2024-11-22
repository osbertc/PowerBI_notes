# PowerBI_notes
syntax for powerBI and power query

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
