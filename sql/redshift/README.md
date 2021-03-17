# Redshift

SQL commands specific to Amazon Redshift.

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
where
    revenue_rank = 1
order by
    revenue desc;
```

This will grab the highest grossing transaction for each customer, orderbed revenue and then the most recent transaction date. Note the `where` clause limits the query results to the highest grossing. If I didn't have that where clause it would just return everything with the `revenue_rank` included.
