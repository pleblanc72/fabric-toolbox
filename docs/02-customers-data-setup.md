# Step 2: Customers Data Setup

This guide loads the **Customers** data from a CSV file via GitHub into your `ECommerce_Sales_Weather_Analytics` dataflow.

---

## Overview

You'll create a **Customers** query that:
- Points to the `sample-data/Customers.csv` file in the GitHub repo (or your uploaded location)
- Loads 50,000 customer records with segment and region information
- Outputs a table ready to join with Orders and Weather data

**Natural output:** 50,000 rows, 5 columns (CustomerID, Name, Segment, Region, Value)

---

## Prerequisites

Before you begin, make sure you have:
- ✅ **Marketing_Weather_Enrichment** query created (from Step 1)
- ✅ Access to the Customers.csv file (options below)

---

## Option A: Load from GitHub (Recommended)

Use this method to load directly from the sample data in the GitHub repository.

### Step 1: Create a Blank Query for Customers CSV

1. In your `ECommerce_Sales_Weather_Analytics` dataflow, click **Get Data** → **Blank Query**
2. In the formula bar, paste this M code:

```powerquery
let
    url = "https://raw.githubusercontent.com/YOUR_USERNAME/ECommerce_Sales_Weather_Analytics/main/sample-data/Customers.csv",
    Source = Csv.Document(Web.Contents(url)),
    Headers = Table.PromoteHeaders(Source),
    TypeConverted = Table.TransformColumnTypes(Headers, {
        {"CustomerID", type text},
        {"Name", type text},
        {"Segment", type text},
        {"Region", type text},
        {"Value", type number}
    })
in
    TypeConverted
```

**Note:** Replace `YOUR_USERNAME` with the GitHub username/org that owns the repo.

3. Click **Done**

### Step 2: Rename Query

1. Right-click the query name
2. Select **Rename**
3. Type: `Customers`
4. Press Enter

### Step 3: Verify Results

Click **Refresh**. You should see:
- **~50,000 rows**
- **5 columns:** CustomerID, Name, Segment, Region, Value
- Data properly typed (text for IDs/names, number for Value)

| CustomerID | Name | Segment | Region | Value |
|---|---|---|---|---|
| C001 | John Doe | Premium | Northeast | 5000 |
| C002 | Jane Smith | Standard | Southeast | 2500 |
| C003 | Bob Johnson | Basic | Midwest | 1200 |

---

## Option B: Local CSV Upload to Azure Blob Storage

If you prefer not to use GitHub:

1. Download `Customers.csv` from the repo's `sample-data/` folder
2. Upload it to your Azure Blob Storage account
3. Replace the URL in the M code with your Blob Storage path
4. Follow steps 1-3 above

---

## Customers.csv Schema

| Column | Type | Description | Example |
|--------|------|---|---|
| CustomerID | Text | Unique customer identifier | C001 |
| Name | Text | Customer name | John Doe |
| Segment | Text | Customer segment (Premium/Standard/Basic) | Premium |
| Region | Text | Geographic region | Northeast |
| Value | Number | Customer lifetime value ($) | 5000 |

**Row count:** 50,000 customers  
**Segments:** Premium, Standard, Basic  
**Regions:** Northeast, Southeast, Midwest, Southwest, West  

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Web.Contents: Unauthorized" | GitHub URL may be private; use raw URL and check GitHub token access |
| "CSV parsing error" | Verify CSV format; should have headers in row 1 |
| "Row count is 0" | Check URL is accessible; try opening in browser first |
| "Column type mismatch" | Value column must be numeric; check source CSV for non-numeric entries |

---

## Next Steps

→ Move to [03-orders-data-setup.md](03-orders-data-setup.md) to load the Orders CSV data.
