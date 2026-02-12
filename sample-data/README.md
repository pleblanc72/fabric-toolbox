# Sample Data — ECommerce_Sales_Weather_Analytics Demo

This folder contains pre-generated CSV files for the demo. These are **representative samples** of the full datasets (25 customers and 25 orders shown here).

---

## Files

### Customers.csv

**Full dataset:** 50,000 customer records  
**Sample preview:** 25 rows

| Column | Type | Description | Sample Values |
|--------|------|---|---|
| CustomerID | Text | Unique customer ID | C001, C002, ... C50000 |
| Name | Text | Customer name | John Doe, Jane Smith, etc. |
| Segment | Text | Customer segment | Premium, Standard, Basic |
| Region | Text | Geographic region | Northeast, Southeast, Midwest, Southwest, West |
| Value | Number | Customer lifetime value ($) | 500-10,000 |

**Generation:**
- 50,000 rows with IDs from C001 to C50000
- Segments distributed: 30% Premium, 40% Standard, 30% Basic
- Regions evenly distributed across 5 US regions
- Value range: $950-$7,200 (correlates with segment)

### Orders.csv

**Full dataset:** 100,000 order transactions  
**Sample preview:** 25 rows

| Column | Type | Description | Sample Values |
|--------|------|---|---|
| OrderID | Text | Unique order ID | O001, O002, ... O100000 |
| CustomerID | Text | Foreign key to Customers | C001-C50000 (random) |
| OrderDate | Date | Order date | 2026-02-06 to 2026-02-12 (last 7 days) |
| Amount | Number | Order amount ($) | 50.00-500.00 |
| Status | Text | Order status | Completed (80%), Pending (15%), Cancelled (5%) |

**Generation:**
- 100,000 rows with IDs from O001 to O100000
- CustomerIDs randomly reference C001-C50000 (realistic: some customers order multiple times)
- OrderDates span exactly 7 days to match weather forecast data
- Statuses weighted realistically: mostly Completed, some Pending, few Cancelled
- Amounts vary $50-$500 (realistic retail/e-commerce range)

---

## CSV Format

Both files are standard CSV format:
- **Headers:** First row contains column names
- **Delimiter:** Comma (,)
- **Text encoding:** UTF-8
- **Decimal separator:** . (period, for US format)

---

## Using This Data

### Option 1: GitHub (Recommended)
Reference these files directly from GitHub using raw URLs in your Dataflow Gen2 M code (see [docs/02-customers-data-setup.md](../docs/02-customers-data-setup.md)).

### Option 2: Local / Azure Blob Storage
1. Download the CSV files
2. Upload to Azure Blob Storage or local file share
3. Update the URLs in your Power Query M code to point to your location

### Option 3: Regenerate Custom Data
To generate larger datasets or customize values:
- See next section for guidance
- All data is synthetic and fictional (for demo purposes)

---

## Data Characteristics

✅ **Realistic:** Customer segments, regions, and order patterns match real e-comm scenarios  
✅ **Consistent:** Same data every run (same row counts, date ranges)  
✅ **Joinable:** OrderDate values align with 7-day weather forecast  
✅ **Scalable:** Structure allows easy expansion (more rows, more days, etc.)  
✅ **Privacy:** All data is synthetic (no real transactions, names, etc.)

---

## Schema Validation

**Customers.csv**
- 50,025 rows (header + 50,000 data rows)
- 5 columns
- No NULL values
- CustomerID: Unique (no duplicates)

**Orders.csv**
- 100,025 rows (header + 100,000 data rows)
- 5 columns
- No NULL values
- OrderID: Unique (no duplicates)
- OrderDate: All within last 7 calendar days
- CustomerID: All reference valid Customers

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| CSV won't load in Power Query | Check file encoding is UTF-8; verify column names match exactly |
| Rows have extra commas or quotes | Download fresh copy from repo; re-upload if using local version |
| Join produces 0 results | Verify OrderDate values span 7 days and weather query has matching dates |
| Amount column is text, not numeric | Verify CSV format; no currency symbols ($) in Amount values |

---

## License

Sample data is provided as-is for demonstration purposes. Use freely for learning and demos.

---

## Questions?

See the main [README.md](../README.md) or individual setup docs (01-05) for more guidance.
