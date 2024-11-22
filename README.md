# PowerBI_notes
syntax for powerBI and power query


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
