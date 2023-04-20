# Sales Insight 1

# Sales Insight 2:

- Total Profit Margin
``` sql
SELECT SUM(profit_margin) AS total_profit_margin FROM sales.transactions;
```

- Revenue (Total sales_amount)
``` sql
SELECT SUM(sales_amount) AS revenue FROM transactions;
```

### Issues that went unnoticed:
  - In table sales.markets, `Bhopal` has two codes `Mark007` & `Mark013`. This should be cleaned or handled properly or it can create serious gaps in the analysis process.

    #### Example: Get `Revenue` by `market_name` instead of `market_code`. Here is how:
    ``` sql
    WITH trans_market AS(
    SELECT t.*, m.markets_name FROM transactions t 
    JOIN markets m ON t.market_code=m.markets_code)
    SELECT markets_name, SUM(sales_amount) as total_sales_amount FROM trans_market
    GROUP BY markets_name
    ORDER BY total_sales_amount DESC;
    ```

- Profit Margin % by Market
-- This is calculted by the formula, `(total_profit_margin(of the market)/total_sales_amount(of the market)) * 100`
``` sql
WITH trans_market AS(
SELECT m.markets_name, SUM(t.profit_margin) AS total_profit_margin, SUM(t.sales_amount) as total_sales_amount FROM transactions t 
JOIN markets m ON t.market_code=m.markets_code
GROUP BY m.markets_name)
SELECT markets_name, (total_profit_margin/total_sales_amount)*100 AS profit_margin_perc
FROM trans_market
ORDER BY profit_margin_perc DESC
```

- Profit Margin Contribution % by Market
-- This is calculated by the formula, `(total_profit_margin(of a market)/total_profit_margin(SUM of all the markets))*100`
``` sql
WITH trans_market AS(
SELECT m.markets_name, SUM(t.profit_margin) AS total_profit_margin, SUM(t.sales_amount) as total_sales_amount FROM transactions t 
JOIN markets m ON t.market_code=m.markets_code
GROUP BY m.markets_name)
SELECT markets_name, (total_profit_margin/total_sales_amount)*100 AS profit_margin_perc,
((total_profit_margin/(SELECT SUM(total_profit_margin) FROM trans_market))*100) AS profit_margin_contri_market
FROM trans_market
ORDER BY profit_margin_contri_market DESC;
```

