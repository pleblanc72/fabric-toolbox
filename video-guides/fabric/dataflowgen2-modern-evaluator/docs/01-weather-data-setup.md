# Step 1: Weather Data Setup — Marketing_Weather_Enrichment Query

This guide walks through connecting to the **Open-Meteo Weather API** in Dataflow Gen2 (`ECommerce_Sales_Weather_Analytics`) to create the `Marketing_Weather_Enrichment` query.

---

## Overview

You'll create a **Marketing_Weather_Enrichment** query that:
- Calls the Open-Meteo API for hourly weather forecasts (NYC, 7 days)
- Parses and transforms all data in M code (maintains query folding performance)
- Outputs a clean table with weather categories, temperature segments, and impact flags
- Joins later with Customers and Orders data

**Natural output:** 168 rows (7 days × 24 hours), 9 columns, fully transformed and ready for analysis.

---

## Part 1: API Details & Data Structure

### Open-Meteo API Endpoint
```
https://api.open-meteo.com/v1/forecast
```

### Query Parameters for Demo
```
?latitude=40.7128
&longitude=-74.0060
&hourly=temperature_2m,precipitation,weather_code,wind_speed_10m
&timezone=America/New_York
&forecast_days=7
```

**Parameters explained:**
- `latitude=40.7128` — NYC latitude
- `longitude=-74.0060` — NYC longitude (adjust for your test location)
- `hourly=temperature_2m,precipitation,weather_code,wind_speed_10m` — hourly metrics
- `timezone=America/New_York` — timezone for timestamps
- `forecast_days=7` — 7-day forecast

### Full URL for Demo
```
https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&hourly=temperature_2m,precipitation,weather_code,wind_speed_10m&timezone=America/New_York&forecast_days=7
```

### Expected JSON Response
```json
{
  "latitude": 40.7128,
  "longitude": -74.0060,
  "timezone": "America/New_York",
  "hourly": {
    "time": ["2026-02-12T00:00", "2026-02-12T01:00", ...],
    "temperature_2m": [35.2, 34.8, 34.1, ...],
    "precipitation": [0.0, 0.0, 0.1, ...],
    "weather_code": [1, 1, 80, ...],
    "wind_speed_10m": [12.5, 13.2, 11.8, ...]
  }
}
```

---

## Part 2: Create the Query in Dataflow Gen2

### Step 1: Create a Blank Query with M Code

1. In Microsoft Fabric, open your **`ECommerce_Sales_Weather_Analytics` dataflow**
2. In Power Query Editor, click **Get Data** → **Blank Query**
3. In the formula bar, paste this complete M code:

```powerquery
let
    url = "https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&hourly=temperature_2m,precipitation,weather_code,wind_speed_10m&timezone=America/New_York&forecast_days=7",
    Source = Json.Document(Web.Contents(url, [Headers=[#"Accept-Encoding"="identity"]])),
    HourlyData = Source[hourly],
    TimeList = HourlyData[time],
    TempList = HourlyData[temperature_2m],
    PrecipList = HourlyData[precipitation],
    WeatherList = HourlyData[weather_code],
    WindList = HourlyData[wind_speed_10m],
    
    // Transform lists upfront - parse dates, extract hours, categorize weather, etc.
    DateList = List.Transform(TimeList, each Date.FromText(Text.Start(_, 10))),
    HourList = List.Transform(TimeList, each Number.From(Text.Middle(_, 11, 2))),
    WeatherCategoryList = List.Transform(WeatherList, each 
        if _ = 0 then "Clear"
        else if _ = 1 then "Mainly Clear"
        else if _ = 2 then "Partly Cloudy"
        else if _ = 3 then "Overcast"
        else if _ >= 50 and _ < 70 then "Drizzle/Rain"
        else if _ >= 70 and _ < 90 then "Snow"
        else if _ >= 90 and _ < 100 then "Thunderstorm"
        else "Unknown"),
    TempSegmentList = List.Transform(TempList, each
        if _ < 32 then "Freezing (<32°F)"
        else if _ < 45 then "Cold (32-45°F)"
        else if _ < 60 then "Cool (45-60°F)"
        else if _ < 75 then "Mild (60-75°F)"
        else "Warm (>75°F)"),
    HighImpactList = List.Transform(List.Zip({PrecipList, WindList, WeatherList}), each
        _{0} > 0.1 or _{1} > 20 or (_{2} >= 70 and _{2} < 90)),
    LocationList = List.Repeat({"New York City"}, List.Count(DateList)),
    
    CreateTable = Table.FromColumns(
        {DateList, HourList, TempList, PrecipList, WindList, WeatherCategoryList, TempSegmentList, HighImpactList, LocationList},
        {"date", "hour", "temperature_2m", "precipitation", "wind_speed_10m", "weather_category", "temperature_segment", "high_impact_weather", "location"}
    )
in
    CreateTable
```

4. Click **Done**

### Step 2: Rename Query

1. Right-click the query name in the left panel
2. Select **Rename**
3. Type: `Marketing_Weather_Enrichment`
4. Press Enter

### Step 3: Verify Results

Click **Refresh** to execute the query. You should see:
- **~168 rows** (7 days × 24 hours)
- **9 columns** with proper data types
- Sample data preview

| date | hour | temperature_2m | precipitation | wind_speed_10m | weather_category | temperature_segment | high_impact_weather | location |
|------|------|---|---|---|---|---|---|---|
| 2026-02-12 | 0 | 35.2 | 0.0 | 12.5 | "Clear" | "Freezing" | false | "New York City" |
| 2026-02-12 | 1 | 34.8 | 0.0 | 13.2 | "Mainly Clear" | "Freezing" | false | "New York City" |

---

## What the M Code Does

✅ **Calls the API** with compression disabled (avoids format errors)  
✅ **Parses JSON** automatically  
✅ **Transforms at M layer:**
  - Converts ISO timestamps to dates (YYYY-MM-DD) and extracts hours (0-23)
  - Categorizes weather codes (0-99) into readable conditions ("Clear", "Snow", etc.)
  - Bins temperatures into retail segments ("Freezing", "Cold", "Mild", etc.)
  - Flags adverse weather (high_impact_weather = true if rain/snow/high wind)
  - Adds location identifier
  
✅ **Maintains query folding** — all operations happen before table creation = maximum performance

---

## Output Columns

| Column | Type | Purpose | Example |
|--------|------|---------|----------|
| `date` | Date | Day reference (join key with Orders) | 2026-02-12 |
| `hour` | Integer | Hour (0-23) | 0 |
| `temperature_2m` | Double | Temperature in °F | 35.2 |
| `precipitation` | Double | Hourly rainfall (mm) | 0.0 |
| `wind_speed_10m` | Double | Wind speed (mph) | 12.5 |
| `weather_category` | Text | Human-readable weather | "Clear" |
| `temperature_segment` | Text | Temperature bin for retail analysis | "Freezing" |
| `high_impact_weather` | Boolean | Flag for adverse conditions | false |
| `location` | Text | Location identifier (for multi-location support) | "New York City" |

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "DataFormat.Error: compression method unsupported" | Already fixed in M code with `Accept-Encoding: identity` header |
| Query returns 0 rows | Check internet connectivity; Open-Meteo is publicly available |
| Dates parse to null or error | M code handles ISO format (YYYY-MM-DDTHH:MM); shouldn't occur |
| Weather categories all show "Unknown" | Verify weather_code values are in range 0-100 |

---

## Notes

- **Performance:** API call typically <500ms; refresh is very fast (demonstrates Modern Evaluator efficiency)
- **Data freshness:** 7-day forecast; dates are from when the query runs
- **Customization:** To use different location, change latitude/longitude in URL and LocationList formula
- **Scale:** This data (168 rows × 9 columns) is small and fast; perfect for demo purposes

---

## Next Step

→ Move to [02-customers-data-setup.md](02-customers-data-setup.md) to load the Customers CSV data.
