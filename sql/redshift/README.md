# Redshift

SQL commands specific to Amazon Redshift.

## Date functions

### dateadd

The [`dateadd`](https://docs.aws.amazon.com/redshift/latest/dg/r_DATEADD_function.html) function adds (or subtracts) a time interval from a date or timestamp column. The syntax is `dateadd(datepart, interval, time_field)`:

``` sql
select
    dateadd(day, 30, date_field), # adds 30 days to date_field
    dateadd(month, -1, date_field), # subtracts a month from date_field
    dateadd(year, 1, date_field) # adds 1 year to date_field
from
    table_name;
```

Common `datepart`s include `minute`, `hour`, `day`, `week`, `month`, `quarter`, and `year`. A complete list can be found [here](https://docs.aws.amazon.com/redshift/latest/dg/r_Dateparts_for_datetime_functions.html).

### datediff

The [`datediff`](https://docs.aws.amazon.com/redshift/latest/dg/r_DATEDIFF_function.html) function determines the difference (according to the specified datepart) between two dates. The syntax is `datediff(datepart, first_timestamp, second_timestamp)`. The datepart boundary is very important here. If you want to know the differenct in years between 2020-12-31 and 2021-01-01, it will respond with `1` since 2021 - 2020 = 1. Thus, if you are considering a problem like this it would be better to select `day` as the `datepart`.

``` sql
select
    datediff(day, timestamp_1, timestamp_2), # difference in days between timestamp 1 and 2
    datediff(day, '2020-11-30' :: date, '2021-01-01' :: date), # returns 32
    datediff(month, '2020-11-30' :: date, '2021-01-01' :: date), # returns 2
    datediff(year, '2020-11-30' :: date, '2021-01-01' :: date) # returns 1
from
    table_name;
```

## Window functions

### Row number

This function assigns a number to each row based on partition and sort criteria. For example, suppose I have a table that stores transaction details for customers:

* customer_id
* transaction_id
* transaction_date
* revenue

If I wanted to know the transaction with the most revenue for each customer I could simply:

``` sql
select
    customer_id, 
    max(revenue) as max_revenue
from
    transactions
group by
    customer_id
order by
    max_revenue desc;
```

But suppose I want to know the transaction date where the maximum revenue was received for each customer. This can get complicated, especially if that customer has had transactions that share the maximum value. One way to accomplish this is using the `row_number` window function, which will allow me to sort the transactions for each customer by revenue and assign each a number that I can filter on:

``` sql
with
    t
as (
    select
        customer_id, 
        transaction_id, 
        transaction_date, 
        revenue, 
        row_number() over (
            partition by 
                customer_id
            order by
                revenue desc,
                transaction_date desc
        ) as revenue_rank
    from
        transactions
)

select
    customer_id, 
    transaction_id, 
    transaction_date, 
    revenue
from
    t
where
    revenue_rank = 1
order by
    revenue desc
;
```

This will grab the highest grossing transaction for each customer, orderbed revenue and then the most recent transaction date. Note the `where` clause limits the query results to the highest grossing. If I didn't have that where clause it would just return everything with the `revenue_rank` included.
