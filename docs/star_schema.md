# NYC Yellow Taxi Star Schema

## Overview

This project uses a Kimball-style dimensional model to analyze NYC Yellow Taxi trips from January 2024.

The model contains one central fact table and four dimension tables.

## Star Schema

```text
                 dim_date
                    |
                    |
dim_time ---- fact_trips ---- dim_location
                    |
                    |
              dim_payment
```

## Fact Table

### fact_trips

The `fact_trips` table stores measurable trip-level facts.

### Measures

- `trip_distance`
- `duration_mins`
- `fare_amount`
- `tip_amount`
- `total_amount`

### Foreign Keys

- `date_id`
- `time_id`
- `pickup_location_id`
- `payment_id`

## Dimension Tables

## dim_date

Used for date-based analysis.

Columns:

- `date_id`
- `full_date`
- `day_name`
- `day_type`
- `week_number`
- `month_num`

Business questions supported:

- Weekday vs weekend trip volume
- Daily revenue trends
- Revenue by day of week

## dim_time

Used for time-of-day analysis.

Columns:

- `time_id`
- `hour_of_day`
- `time_label`
- `period_of_day`

Business questions supported:

- Peak travel hours
- Morning/evening demand patterns
- Low-volume late-night periods

## dim_location

Used for pickup zone analysis.

Columns:

- `location_id`
- `borough`
- `zone_name`

Business questions supported:

- Top pickup zones
- Airport zone revenue
- Borough-level demand

## dim_payment

Used for payment behavior analysis.

Columns:

- `payment_id`
- `payment_name`

Business questions supported:

- Revenue by payment type
- Tip behavior by payment method
- Credit card vs cash trip patterns

## Why Star Schema?

A star schema was used because it makes analytics queries simpler, faster, and easier to connect with BI tools like Tableau.

Benefits:

- Clear separation between facts and dimensions
- Easy filtering by date, time, location, and payment
- Supports multiple business questions using the same model
- Works well for dashboard reporting

## Bus Matrix Summary

| Business Process | dim_date | dim_time | dim_location | dim_payment | Metrics |
|---|---|---|---|---|---|
| Trip Revenue Analysis | Yes | Yes | Yes | No | Total Revenue, Avg Fare |
| Demand & Volume | Yes | Yes | Yes | No | Total Trips, Passengers |
| Zone Performance | No | No | Yes | No | Revenue by Zone, Avg Fare |
| Payment Behavior | No | No | No | Yes | Trips, Revenue, Avg Tip |
| Weekday vs Weekend | Yes | No | No | No | Trips, Revenue, Fare, Tip |
| Time-of-Day Demand | No | Yes | No | No | Hourly Trips, Revenue |
