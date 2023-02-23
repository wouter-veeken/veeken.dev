---
title: 'Amount-per-day column'
date: Thu, 15 Feb 2023 12:00:00 +0000
draft: false
tags: [powerbi, powerquery]
---

When your sales are not items but services spread out over time, it can be difficult to keep track of how much you have delivered. One possible way in PowerBI, is to generate a list of days that the service lasts for each record, using PowerQuery, and then divide price over those days. But even with a few thousand original records, you soon end up with millions of rows and your model can get slow.

A better way to solve this is to create just an amount-per-day column in your sales table in PowerQuery, then aggregate that column in DAX for all selected dates. This lets you freely select any date range, using the same calculation as basis. I created two measures in DAX for this purpose;

```powerquery
DailyTotal =
var _CurrentDate=SELECTEDVALUE(dim_Calendar[Day])
return
SUMX(FILTER(
    'fact_Sales',
    ('fact_Sales'[EndDate]>=_CurrentDate )
    && 'fact_Sales'[StartDate]<=_CurrentDate
), 'fact_Sales'[DailyAmount])
```
This gets you the total daily generated amount, which you can freely split and filter along other dimensions as usual in your visuals. To aggregate this into other timespans, reuse the measure as follows:

```powerquery
TotalAmount = SUMX( VALUES( 'dim_Calendar'[Day]), [DailyTotal])
```

This keeps your model small, true to reality and consistent along any splits or filters.
