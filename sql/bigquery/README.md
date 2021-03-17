# BigQuery

SQL commands specific to Google Cloud BigQuery.

## Selecting data

The `select` list follows standard SQL syntax, but BigQuery has some extra goodies.

### select * modifiers

#### select * except

Want to select all of the columns in a table save one or two? Use `select * except`:

``` sql
select
    * except(column)
from
    table;
```

#### select * replace

Want to select all of the columns in a table but replace one or a few of them?

``` sql
select
    * replace(value as column)
from
    table;
```
