# SQL

This page is for SQL commands and code that is general across data warehousing and relational database systems (e.g., Redshift, BigQuery, MySQL, etc.). Commands that are system-specific are included in their respective folders (see [Redshift](/sql/redshift/) and [BigQuery](/sql/bigquery/)).

## Creating tables

What happens most commonly in my role is I am building a more structured table downstream of a table that houses incoming raw data. So typically I create a table using a select statement:

``` sql
create table
    table_name
as (
    select
        column1, 
        column2, 
        ..., 
        columnN
    from
        upstream_table
);
```
