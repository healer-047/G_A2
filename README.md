# NYC Yellow Taxi 2023 — Interactive Trip Explorer (D3 Dashboard)

This project is a **code-written** (no auto-charting) interactive dashboard built on the provided *2023 Yellow Taxi Trip Data* CSV.

It supports common exploratory analysis tasks:

- **Temporal demand:** *When* are trips most frequent (by date, day-of-week, hour)?
- **Fare vs. distance:** How does **total_amount** scale with **trip_distance**? Where are outliers?
- **Payment behavior:** How do distributions of **total_amount** differ by **payment_type**?
- **Spatial flows (proxy):** Which **pickup → dropoff** zone corridors dominate (Sankey)?

## What’s included

- `preprocess.py` — turns the raw trip CSV into lightweight files for the web dashboard:
  - `web/data/daily.csv`
  - `web/data/daily_hour.csv`
  - `web/data/sample.csv`
  - `web/data/meta.json`
- `web/index.html`, `web/main.js`, `web/style.css` — the interactive visualization (D3 v7 + SVG).
- Pre-generated `web/data/*` based on the attached dataset (so you can run immediately).

## How to run locally

### Option A — run immediately (uses already-generated `web/data/*`)
1. Open a terminal in the `web/` folder:
   ```bash
   cd web
   python -m http.server 8000
   ```
2. Open: `http://localhost:8000`

### Option B — regenerate the data from a raw CSV
1. From the project root:
   ```bash
   python preprocess.py --input /path/to/2023_Yellow_Taxi_Trip_Data.csv --out web/data --sample 50000
   ```
2. Start a local server from `web/` (same as above).

> Note: Browsers block local file reads, so **you need a local server** (e.g., `python -m http.server`) rather than double-clicking `index.html`.

## Visualization techniques (meets assignment requirement)

Distinct techniques used in the dashboard:

1. **Line/area chart** — daily trip volume (time series)
2. **Heatmap** — hour × day-of-week volume
3. **Scatterplot** — trip_distance vs total_amount (sampled points)
4. **Histogram** — distribution of trip_distance
5. **Box plot** — distribution of total_amount by payment_type
6. **Sankey diagram** — pickup → dropoff corridors (zone IDs)

(If your team size is larger than 6, add one more view such as a small-multiples calendar heatmap or a ridgeline plot.)

## Interactions (>= 2)

- **Brushing** on the time series → filters date range (linked to other views)
- **Brushing** on the scatterplot (2D) → filters distance × total range
- **Brushing** on the histogram → filters distance (bidirectional with scatterplot)
- **Click selection** on heatmap → filters hour + weekday
- **Click selection** on box plot + dropdown → filters payment type
- **Click selection** on Sankey nodes → filters pickup or dropoff zone
- **Tooltips** on hover for multiple marks (points, bars, heatmap cells, Sankey links/nodes)

## Notes on performance

- The raw CSV can be big. The dashboard uses:
  - **aggregates** for time series and heatmap
  - a **random sample** for point-heavy views (scatter/box/sankey)
- You can increase/decrease `--sample` based on your machine and browser.

## Data dictionary highlights

- `payment_type` mapping (TLC standard):
  - 1=Credit card, 2=Cash, 3=No charge, 4=Dispute, 5=Unknown, 6=Voided trip
- `PULocationID`, `DOLocationID` are TLC taxi zone IDs (numeric). If you want zone names, add a taxi-zone lookup table and join it in preprocessing.
