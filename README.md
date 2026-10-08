# ZoomRide SQL Analysis

## Overview
ZoomRide is a Transportation network company operating in six African cities: Lagos, Abuja, Port Harcourt, Nairobi, Accra and Kampala. In this project I used MySQL (run on OneCompiler, an online SQL editor) to explore a deliberately messy dataset, clean it, and answer business questions about where the company should invest.

## Data
Three linked tables:
- `customers` (40 rows): who rides
- `drivers` (20 rows): who drives, with city, vehicle type and rating
- `trips` (300 rows before cleaning): one row per booked trip, linking a customer to a driver

All fares are in Naira. Cancelled trips have a fare of 0.

## Part 1: Look at the data

**Q1. How many rows are in the trips table?**
300 rows in total (including cancelled trips), of which 250 are completed.

**Q2. What are the 5 longest completed trips?**

| trip_id | city | distance_km | fare |
|---|---|---|---|
| 250 | Lagos | 35.9 | 6,240.00 |
| 225 | Lagos | 33.4 | 8,820.00 |
| 126 | Kampala | 33.1 | 5,800.00 |
| 98 | Abuja | 32.8 | 5,750.00 |
| 151 | Lagos | 31.3 | 3,330.00 |

## Part 2: Count by a group

**Q3. How many trips happened in each city?**
The answer looked wrong. ZoomRide operates in 6 cities, but the result showed 12 rows. Port Harcourt appeared as "PH" and "Port-Harcourt", Nairobi as "Nairobbi", Kampala as "Kampla", and Accra and Lagos each appeared again with a leading space. Grouping treats each spelling as a different city, so every real city's trip count was split up and understated. The city names needed cleaning before counting.

## Part 3: Clean the data

**Q4a. Which trips are duplicates?**
Grouping by customer, driver, date and fare found 2 duplicate pairs: trips 82 and 299, and trips 253 and 300.

**Q4b. How many completed trips have a missing fare?**
9 completed trips have a NULL fare.

**Q5. Fix the data**
- Trimmed the leading spaces from every city name.
- Corrected the misspellings: "PH" and "Port-Harcourt" became "Port Harcourt", "Nairobbi" became "Nairobi", and "Kampla" became "Kampala".
- Deleted the later copy of each duplicate (trips 299 and 300), keeping the first.
- Left the 9 missing fares untouched. They are reported, not guessed.

**Check:** after cleaning, the table has 298 rows and exactly 6 cities.

## Part 4: Find the answers (on the clean data)

**Q6. Revenue by city (completed trips only)**

| city | trips | total_revenue | avg_fare |
|---|---|---|---|
| Lagos | 93 | 218,890.00 | 2,405.38 |
| Accra | 37 | 92,640.00 | 2,807.27 |
| Abuja | 36 | 88,720.00 | 2,464.44 |
| Port Harcourt | 31 | 71,240.00 | 2,374.67 |
| Nairobi | 32 | 58,960.00 | 1,901.94 |
| Kampala | 19 | 38,020.00 | 2,112.22 |

- Lagos earns the most revenue, more than double any other city.
- Accra has the highest average fare and Nairobi the lowest.
- Revenue and average fare ignore the 9 completed trips with a NULL fare, so they are slightly understated. The trip count includes those trips, which is why total revenue divided by trips does not exactly equal the average fare.

**Q7. Revenue by month (completed trips only)**
The best month is December 2025, with 66,980 Naira from 31 trips.

## Part 5: Join two tables

**Q8. Revenue by vehicle type (completed trips only)**
`vehicle_type` is stored in the `drivers` table, so I joined it to `trips` on `driver_id`. Economy earns the most revenue, with N262,550.

## Data problems found and fixed

| Problem | What I did |
|---|---|
| Inconsistent city names (`PH`, `Port-Harcourt`, `Nairobbi`, `Kampla`, leading spaces) | Standardised to 6 spellings with `TRIM` and `UPDATE` |
| 2 duplicate trips (299 and 300, copies of 82 and 253) | Found by grouping and counting, then deleted the later copies |
| 9 completed trips with a missing fare | Left as they are and reported, rather than inventing numbers |

## Key findings
- Lagos is the strongest market: 218,890 Naira from 93 completed trips.
- Accra has the highest average fare, but far fewer trips than Lagos.
- Revenue is slightly understated because of the 9 missing fares.
- Before a big investment decision, I would want cost data (driver pay, fuel) to compare profit, not just revenue.

## SQL skills used
`SELECT`, `WHERE`, `GROUP BY`, `HAVING`, `ORDER BY`, `LIMIT`, aggregate functions, `TRIM`, `UPDATE`, `DELETE`, `INNER JOIN`, `DATE_FORMAT`, NULL handling

## How to run
1. Open [OneCompiler](https://onecompiler.com/mysql/455jv5trn) and select MySQL.
3. Run the queries
