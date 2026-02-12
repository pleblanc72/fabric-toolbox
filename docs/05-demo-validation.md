# Step 5: Demo Validation

This final step validates your dataflow before measuring the Modern Evaluator performance improvement.

---

## Checklist: Verify All Data

Before running the Modern Evaluator performance test, confirm everything is in place:

### Marketing_Weather_Enrichment Query
- [ ] Query exists and is named exactly **`Marketing_Weather_Enrichment`**
- [ ] **Refresh succeeds** (displays weather data)
- [ ] Row count: ~168 rows (7 days × 24 hours)
- [ ] Columns: date, hour, temperature_2m, precipitation, wind_speed_10m, weather_category, temperature_segment, high_impact_weather, location
- [ ] All temperatures are °F (roughly 30-40°F range for winter)
- [ ] Weather categories show mix of "Clear", "Mainly Clear", "Overcast", etc.

### Customers Query
- [ ] Query exists and is named exactly **`Customers`**
- [ ] **Refresh succeeds**
- [ ] Row count: ~50,000 rows
- [ ] Columns: CustomerID, Name, Segment, Region, Value
- [ ] Value column contains numbers >0
- [ ] Segments include: Premium, Standard, Basic
- [ ] Regions include: Northeast, Southeast, Midwest, Southwest, West

### Orders Query
- [ ] Query exists and is named exactly **`Orders`**
- [ ] **Refresh succeeds**
- [ ] Row count: ~100,000 rows
- [ ] Columns: OrderID, CustomerID, OrderDate, Amount, Status
- [ ] OrderDate values are in YYYY-MM-DD format and fall within last 7 days
- [ ] Amount column contains numbers ($50-$500)
- [ ] Status includes: Completed, Pending, Cancelled

### Enriched_Sales_Analysis Query
- [ ] Query exists and is named exactly **`Enriched_Sales_Analysis`**
- [ ] **Refresh succeeds** (performs joins)
- [ ] Row count: ~100,000 rows (same as Orders)
- [ ] All 17 columns present (see Step 4 for list)
- [ ] Customer names are populated (join worked)
- [ ] Weather columns are populated (join worked)
- [ ] No unexpected NULL columns

---

## Run a Full Refresh Test

### Test 1: Baseline (Legacy Engine)

1. **Record the start time**
2. In your Power Query Editor, **right-click any query** → **Refresh All**
3. **Wait for all 4 queries to complete**
4. **Record the end time**
5. **Calculate total time** (in seconds)

**Example:** Started 10:00:05, finished 10:02:47 = **162 seconds** (2m 42s)

---

### Test 2: Enable Modern Evaluator

1. In Power Query Editor, click **Options** (top menu)
2. Click **Scale** tab
3. Find **"Modern query evaluation engine"** toggle
4. Toggle it **ON**
5. Click **OK** to save

---

### Test 3: Performance Test (Modern Evaluator)

1. **Record the start time**
2. Right-click any query → **Refresh All** again
3. **Wait for all 4 queries to complete**
4. **Record the end time**
5. **Calculate total time** (in seconds)

**Example:** Started 10:10:05, finished 10:11:22 = **77 seconds** (1m 17s)

---

## Calculate Improvement

| Test | Time (seconds) | Time (minutes) | Improvement |
|------|---|---|---|
| Legacy Engine | 162 | 2:42 | Baseline |
| Modern Evaluator | 77 | 1:17 | **52% Faster** ✅ |

**Formula:** `(Baseline - Modern) / Baseline × 100 = % Improvement`

Example: `(162 - 77) / 162 × 100 = 47.5% improvement`

---

## Expected Results

| Scenario | Expected Time | Range |
|----------|---|---|
| Legacy Engine | 2-3 minutes | 120-180 seconds |
| Modern Evaluator | 1-1.5 minutes | 60-90 seconds |
| Improvement | **40-50% faster** | Typical: 45-55% |

*Your results may vary based on capacity shard load, network conditions, and data size.*

---

## Validation Passed? 

If you've completed all checks and tests:

✅ All 4 queries exist with correct names  
✅ All refreshes succeed without errors  
✅ Row counts match expectations  
✅ Data joins are working (no unexpected NULLs)  
✅ Modern Evaluator toggle is available and working  
✅ You have before/after timing for your demo  

**You're ready to present this demo!** 🎉

---

## If Validation Fails

| Problem | Solution |
|---------|----------|
| Query returns 0 rows | Refresh the source; may be API timeout or CSV access issue |
| Join produces many NULLs | Check join keys match exactly (case-sensitive, data type matching) |
| Modern Evaluator toggle is grayed out | Ensure you're in a Dataflow Gen2 (CI/CD), not classic dataflow |
| Refresh times are identical before/after | Modern Evaluator may not optimize this particular workload; timing noise is normal for small datasets |
| Column names don't match | Double-check query names and column references in M code |

---

## Next Steps

Congratulations! Your `ECommerce_Sales_Weather_Analytics` dataflow is ready for demonstration. 

**Next:** 
- Record your demo showing the before/after Modern Evaluator times
- Check out the video script in [demo-script.md](../demo-script.md)
- Subscribe to Guy in a Cube for more Fabric content!

---

## Summary

You've built:
✅ A **multi-source Dataflow Gen2** combining Web API + CSV data  
✅ **Real transformations** (weather categorization, temperature binning, high-impact flags)  
✅ **Complex joins** demonstrating query engine optimization  
✅ **Measurable performance improvement** (40-50% faster with Modern Evaluator)  

**This is production-ready code.** The patterns and techniques you've learned apply to real enterprise dataflows.

Enjoy! 🚀
