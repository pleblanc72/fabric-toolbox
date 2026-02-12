# ECommerce Sales Weather Analytics — Modern Evaluator Demo

This is a **step-by-step, reproducible guide** to building a multi-source Dataflow Gen2 in Microsoft Fabric that demonstrates the performance improvements of the Modern Query Evaluation Engine.

**Video:** [Modern Evaluator for Dataflow Gen2 — Fabric](https://www.youtube.com/watch?v=YOUR_VIDEO_URL)

---

## What You'll Build

A **Dataflow Gen2** that combines three data sources:
1. **Weather Data** — Real-time hourly forecasts via Open-Meteo API
2. **Customer Data** — Simulated CRM records (50K rows)
3. **Order Data** — Simulated transaction logs (100K rows)

The dataflow joins these sources and applies transformations to create an **enriched analytics table** that correlates weather patterns with e-commerce sales and customer segments.

**Result:** See how the Modern Evaluator cuts refresh time in **half** compared to the legacy engine—on the exact same dataflow!

---

## Prerequisites

- ✅ Microsoft Fabric workspace access (Trial OK)
- ✅ Dataflow Gen2 (CI/CD) capability enabled
- ✅ Basic Power Query knowledge
- ✅ ~45 minutes of time
- ✅ No external tools or scripts needed (data is pre-generated in `/sample-data/`)

---

## Quick Start

### Step 1: Follow the Docs (in order)

| Step | Doc | Purpose | Time |
|------|-----|---------|------|
| 1 | [01-weather-data-setup.md](docs/01-weather-data-setup.md) | Build Marketing_Weather_Enrichment query (Open-Meteo API) | 10 min |
| 2 | [02-customers-data-setup.md](docs/02-customers-data-setup.md) | Build Customers query (CSV from GitHub) | 5 min |
| 3 | [03-orders-data-setup.md](docs/03-orders-data-setup.md) | Build Orders query (CSV from GitHub) | 5 min |
| 4 | [04-join-transformations.md](docs/04-join-transformations.md) | Join all 3 sources + create final enriched table | 15 min |
| 5 | [05-demo-validation.md](docs/05-demo-validation.md) | Validate data before performance test | 5 min |

### Step 2: Test & Measure

Once you complete Step 5:

1. **Run the dataflow with legacy engine** — Note the refresh time
2. **Enable Modern Evaluator** — Options > Scale > Modern query evaluation engine: ON
3. **Run again** — See the speed improvement (typically 40-50% faster)

---

## Dataflow & Query Names

For consistency with the demo video, use these exact names:

- **Dataflow:** `ECommerce_Sales_Weather_Analytics`
- **Query 1:** `Marketing_Weather_Enrichment` (weather data)
- **Query 2:** `Customers` (customer data)
- **Query 3:** `Orders` (order data)
- **Query 4:** `Enriched_Sales_Analysis` (final joined table)

---

## Sample Data

Pre-generated CSV files are in `/sample-data/`:

- **Customers.csv** — 50,000 customer records
- **Orders.csv** — 100,000 order transactions

See [sample-data/README.md](sample-data/README.md) for full schema documentation.

---

## Key Features Demonstrated

✅ **Modern Evaluator (GA)** — .NET Core 8 query engine  
✅ **80+ Connector Support** — Web API + CSV connectors  
✅ **Query Folding** — M code techniques to keep queries performant  
✅ **Complex Joins** — Multi-source data correlation  
✅ **Real Transformations** — Weather categorization, temperature binning, etc.  

---

## Questions?

Open an issue on GitHub or check the docs for troubleshooting.

Happy exploring! 🚀
