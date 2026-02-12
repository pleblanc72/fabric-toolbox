# Step 4: Join Transformations

This guide joins the three data sources (**Marketing_Weather_Enrichment**, **Customers**, **Orders**) to create the final enriched analytics table.

---

## Overview

You'll create an **Enriched_Sales_Analysis** query that:
- Joins Orders → Customers (add customer details to transactions)
- Joins result → Marketing_Weather_Enrichment (add weather context)
- Creates a single table with full business context: customer segment + order details + weather

**Result:** 100,000 rows with customer, order, and weather information for comprehensive analysis.

---

## Prerequisites

- ✅ **Marketing_Weather_Enrichment** query (Step 1)
- ✅ **Customers** query (Step 2)
- ✅ **Orders** query (Step 3)

---

## Step 1: Create a Blank Query for Joins

1. In your `ECommerce_Sales_Weather_Analytics` dataflow, click **Get Data** → **Blank Query**
2. In the formula bar, paste this M code:

```powerquery
let
    // Reference the three source queries
    Orders = #"Orders",
    Customers = #"Customers",
    Weather = #"Marketing_Weather_Enrichment",
    
    // Join 1: Orders + Customers (on CustomerID)
    JoinOrdersCustomers = Table.NestedJoin(
        Orders,
        {"CustomerID"},
        Customers,
        {"CustomerID"},
        "CustomerDetails",
        JoinKind.LeftOuter
    ),
    ExpandCustomers = Table.ExpandTableColumn(
        JoinOrdersCustomers,
        "CustomerDetails",
        {"Name", "Segment", "Region", "Value"},
        {"CustomerName", "Segment", "Region", "CustomerValue"}
    ),
    
    // Join 2: Result + Weather (on OrderDate = date)
    JoinWithWeather = Table.NestedJoin(
        ExpandCustomers,
        {"OrderDate"},
        Weather,
        {"date"},
        "WeatherDetails",
        JoinKind.LeftOuter
    ),
    ExpandWeather = Table.ExpandTableColumn(
        JoinWithWeather,
        "WeatherDetails",
        {"hour", "temperature_2m", "precipitation", "wind_speed_10m", "weather_category", "temperature_segment", "high_impact_weather", "location"},
        {"WeatherHour", "Temperature_F", "Precipitation_mm", "WindSpeed_mph", "WeatherCategory", "TemperatureSegment", "HighImpactWeather", "Location"}
    ),
    
    // Reorder columns for readability
    FinalTable = Table.SelectColumns(
        ExpandWeather,
        {
            "OrderID",
            "CustomerID",
            "CustomerName",
            "Segment",
            "Region",
            "CustomerValue",
            "OrderDate",
            "Amount",
            "Status",
            "Location",
            "WeatherHour",
            "Temperature_F",
            "Precipitation_mm",
            "WindSpeed_mph",
            "WeatherCategory",
            "TemperatureSegment",
            "HighImpactWeather"
        }
    )
in
    FinalTable
```

3. Click **Done**

### Step 2: Rename Query

1. Right-click the query name
2. Select **Rename**
3. Type: `Enriched_Sales_Analysis`
4. Press Enter

### Step 3: Verify Results

Click **Refresh**. You should see:
- **~100,000 rows** (one per order)
- **17 columns** combining all source data
- Each order row enriched with customer details AND weather conditions

| OrderID | CustomerID | CustomerName | Segment | OrderDate | Amount | Temperature_F | WeatherCategory | Status |
|---|---|---|---|---|---|---|---|---|
| O001 | C001 | John Doe | Premium | 2026-02-12 | 150.00 | 35.2 | Clear | Completed |
| O002 | C001 | John Doe | Premium | 2026-02-11 | 75.50 | 42.1 | Partly Cloudy | Completed |

---

## Understanding the Joins

### Join 1: Orders → Customers
```
Orders.CustomerID = Customers.CustomerID
```
Adds customer context (Name, Segment, Region, Value) to each order.

**Type:** LeftOuter — keep all orders, even if customer isn't found

### Join 2: Result → Weather
```
OrderDate = Weather.date
```
Adds weather context (temperature, conditions, rain/snow flags) for the order date.

**Type:** LeftOuter — keep all orders, even if weather data is missing

---

## Output Columns

| Column | Source | Purpose |
|--------|--------|---------|
| OrderID | Orders | Order identifier |
| CustomerID | Orders | Customer identifier |
| CustomerName | Customers | Customer name |
| Segment | Customers | Customer segment (Premium/Standard/Basic) |
| Region | Customers | Customer region |
| CustomerValue | Customers | Customer lifetime value |
| OrderDate | Orders | Order date |
| Amount | Orders | Order amount |
| Status | Orders | Order status |
| Location | Weather | Weather location (NYC) |
| WeatherHour | Weather | Hour of day (0-23) |
| Temperature_F | Weather | Temperature in °F |
| Precipitation_mm | Weather | Rain/snow (mm) |
| WindSpeed_mph | Weather | Wind speed |
| WeatherCategory | Weather | Weather condition (Clear/Snow/Rain/etc.) |
| TemperatureSegment | Weather | Temperature bin (Freezing/Cold/Mild/Warm) |
| HighImpactWeather | Weather | Adverse weather flag (true/false) |

---

## Analysis Opportunities

With these columns, you can now analyze:

✅ **Weather Impact on Sales:** Do sales increase/decrease with specific weather?  
✅ **Segment Behavior:** How do Premium vs Standard customers respond to weather?  
✅ **Regional Patterns:** Do regions show different weather-sales correlation?  
✅ **Temperature Sweet Spots:** At what temperature do certain products sell better?  
✅ **High-Impact Weather Events:** Do orders spike during rain/snow when people stay indoors?

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Reference to undefined query" | Ensure you named queries exactly: `Marketing_Weather_Enrichment`, `Customers`, `Orders` |
| Join returns 0 rows | Check date formats match (OrderDate should be YYYY-MM-DD) |
| Many NULL weather values | Weather data only available for current 7-day period; older orders won't have weather |
| Column not found after expand | Verify source query has the column; may need to adjust expand column names |

---

## Performance Note

This join operation involves:
- 100,000 Orders rows
- 50,000 Customers rows (lookup)
- 168 Weather rows per day x 7 days (lookup)

**With Modern Evaluator:** Handles this efficiently  
**Expected join time:** <30 seconds for the full 100K rows

This is your opportunity to **measure before/after Modern Evaluator performance** in the next step!

---

## Next Steps

→ Move to [05-demo-validation.md](05-demo-validation.md) to validate and prepare for the Modern Evaluator performance test.
