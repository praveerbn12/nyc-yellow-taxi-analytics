# NYC Yellow Taxi Analytics

An end-to-end data warehouse and BI analytics project built on NYC Yellow Taxi trip data from January 2024.

This project uses Snowflake for data warehousing, SQL for ELT, Kimball dimensional modeling for star schema design, and Tableau for dashboard reporting.

## Project Overview

This project analyzes approximately 2.87 million NYC Yellow Taxi trips from January 2024.

The goal was to design a complete analytics warehouse that supports business questions around trip volume, revenue, pickup zones, payment behavior, hourly demand, and weekday vs. weekend patterns.

## Tech Stack

- Snowflake
- SQL
- Tableau
- Kimball Dimensional Modeling
- NYC TLC Yellow Taxi Trip Records
- Parquet Data Files

## Objectives

- Design a star schema data warehouse using Snowflake
- Load raw NYC TLC Parquet data into Snowflake
- Transform raw trip data into clean analytical tables
- Build conformed dimensions for date, time, location, and payment
- Create a central fact table for taxi trips
- Build Tableau dashboards for business analysis

## Dataset

Source: NYC TLC Yellow Taxi Trip Records  
Time Period: January 2024  
Format: Parquet  
Valid Trips Loaded: 2,872,007 rows

Key source fields included:

- tpep_pickup_datetime
- tpep_dropoff_datetime
- PULocationID
- payment_type
- fare_amount
- tip_amount
- total_amount
- trip_distance

## Data Warehouse Design

The warehouse follows a Kimball-style dimensional model.

### Fact Table

`fact_trips`

Measures:

- trip_distance
- duration_mins
- fare_amount
- tip_amount
- total_amount

Foreign keys:

- date_id
- time_id
- pickup_location_id
- payment_id

### Dimension Tables

`dim_date`

- date_id
- full_date
- day_name
- day_type
- week_number
- month_num

`dim_time`

- time_id
- hour_of_day
- time_label
- period_of_day

`dim_location`

- location_id
- borough
- zone_name

`dim_payment`

- payment_id
- payment_name

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

## ELT Workflow

```text
Raw TLC Parquet Files
        ↓
Snowflake Internal Stage
        ↓
raw_trips Table
        ↓
SQL Cleaning & Transformation
        ↓
Dimension Tables
        ↓
fact_trips
        ↓
Tableau Dashboard
```

## Key Transformations

- Filtered invalid trips where fare amount or trip distance was not valid
- Loaded raw Parquet data into Snowflake using stage and COPY INTO
- Handled NULL payment types using an `Unknown` payment category
- Derived trip duration using pickup and dropoff timestamps
- Created surrogate keys for dimensional modeling
- Loaded 2,872,007 records into the fact table

## Example SQL Logic

```sql
INSERT INTO fact_trips (
    date_id,
    time_id,
    pickup_location_id,
    payment_id,
    trip_distance,
    duration_mins,
    fare_amount,
    tip_amount,
    total_amount
)
SELECT
    CAST(TO_CHAR(DATE(tpep_pickup_dt), 'YYYYMMDD') AS INT),
    HOUR(tpep_pickup_dt),
    pickup_location_id,
    COALESCE(payment_type, 5),
    trip_distance,
    ROUND(DATEDIFF('second', tpep_pickup_dt, tpep_dropoff_dt) / 60.0, 2),
    fare_amount,
    tip_amount,
    total_amount
FROM raw_trips
WHERE fare_amount > 0
  AND trip_distance > 0;
```

## Dashboard KPIs

January 2024 dashboard results:

- Total Trips: 2,872,007
- Total Revenue: $78.49M
- Average Fare: $27.33
- Average Distance: 3.73 miles
- Average Duration: 15.75 minutes
- Average Tip: $3.40

## Key Insights

### Hourly Demand

Peak demand occurred around 6 PM, with more than 206K trips and approximately $5.54M in revenue.

### Low-Volume Hours

The lowest trip volume occurred between 3 AM and 5 AM, but average fares were higher due to airport and long-distance trips.

### Payment Behavior

Credit card payments made up the majority of trips and generated most of the revenue.

### Airport Zone Revenue

Airport pickup zones such as JFK and LaGuardia produced much higher average fares compared to regular city zones.

### Weekday vs. Weekend

Weekdays generated the majority of trip volume and revenue, while weekend trips had slightly higher average fare and tip values.

## Challenges Solved

- Loaded Parquet data into Snowflake using internal stage
- Handled NULL payment types to avoid foreign key issues
- Derived trip duration because it was not directly available in source data
- Integrated NYC taxi zone lookup data for location analysis
- Designed a schema that supports multiple business analysis areas

## Future Improvements

- Extend analysis to full-year or multi-year taxi data
- Add weather data as an additional dimension
- Automate pipeline execution
- Add anomaly detection for unusual demand or revenue drops
- Build predictive models for taxi demand by zone and time
- Add full SQL scripts and Tableau screenshots to this repository

## Status

The Snowflake data warehouse and Tableau dashboard were completed as part of the project. This repository currently documents the warehouse design, ELT process, star schema, and dashboard insights. SQL scripts and dashboard screenshots can be added later.
