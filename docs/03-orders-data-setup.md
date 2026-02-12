# Step 3: Orders Data Setup

This guide loads the **Orders** data from a CSV file via GitHub into your `ECommerce_Sales_Weather_Analytics` dataflow.

---

## Overview

You'll create an **Orders** query that:
- Points to the `sample-data/Orders.csv` file in the GitHub repo
- Loads 100,000 order transaction records
- Outputs a table ready to join with Customers and Weather data

**Natural output:** 100,000 rows, 5 columns (OrderID, CustomerID, OrderDate, Amount, Status)

---

## Prerequisites

- ✅ **Marketing_Weather_Enrichment** query created (Step 1)
- ✅ **Customers** query created (Step 2)
- ✅ Access to Orders.csv file

---

## Load from GitHub

### Step 1: Create a Blank Query for Orders CSV

1. In your `ECommerce_Sales_Weather_Analytics` dataflow, click **Get Data** → **Blank Query**
2. In the formula bar, paste this M code:

```powerquery
let
    url = "https://raw.githubusercontent.com/YOUR_USERNAME/ECommerce_Sales_Weather_Analytics/main/sample-data/Orders.csv",
    Source = Csv.Document(Web.Contents(url)),
    Headers = Table.PromoteHeaders(Source),
    TypeConverted = Table.TransformColumnTypes(Headers, {
        {"OrderID", type text},
        {"CustomerID", type text},
        {"OrderDate", type date},
        {"Amount", type number},
        {"Status", type text}
    })
in
    TypeConverted
```

**Note:** Replace `YOUR_USERNAME` with your GitHub username/org.

3. Click **Done**

### Step 2: Rename Query

1. Right-click the query name
2. Select **Rename**
3. Type: `Orders`
4. Press Enter

### Step 3: Verify Results

Click **Refresh**. You should see:
- **~100,000 rows**
- **5 columns:** OrderID, CustomerID, OrderDate, Amount, Status
- OrderDate properly typed as date (YYYY-MM-DD format)
- Amount as numeric

| OrderID | CustomerID | OrderDate | Amount | Status |
|---|---|---|---|---|
| O001 | C001 | 2026-02-12 | 150.00 | Completed |
| O002 | C001 | 2026-02-11 | 75.50 | Completed |
| O003 | C002 | 2026-02-10 | 200.00 | Pending |

---

## Orders.csv Schema

| Column | Type | Description | Example |
|--------|------|---|---|
| OrderID | Text | Unique order identifier | O001 |
| CustomerID | Text | Foreign key to Customers | C001 |
| OrderDate | Date | Order date (YYYY-MM-DD) | 2026-02-12 |
| Amount | Number | Order amount ($) | 150.00 |
| Status | Text | Order status (Completed/Pending/Cancelled) | Completed |

**Row count:** 100,000 orders  
**Date range:** Last 7 days (aligns with weather forecast)  
**Statuses:** Completed, Pending, Cancelled  
**Amounts:** $50 - $500 per order  

---

## Important: Date Alignment

⚠️ **Critical:** OrderDate values must fall within the 7-day weather forecast period. The sample CSV is pre-generated with dates that match the current week when you run the demo. This ensures proper joins with the weather data.

If you regenerate or modify Orders.csv, **ensure OrderDate values span 7 days** to match your weather query.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "OrderDate is null or parse as text" | Ensure date format in CSV is YYYY-MM-DD |
| Join fails (no matching records in Step 4) | Verify OrderDate range overlaps with weather dates (last 7 days) |
| Amount values are text,not numeric | Check CSV for non-numeric characters in Amount column |
| Row count is 0 | Check CSV URL is accessible and properly formatted |

---

## Next Steps

→ Move to [04-join-transformations.md](04-join-transformations.md) to join all three data sources.
