# Capstone-Project-IIT-G

# Smart Parking Dynamic Pricing System

A real-time, intelligent pricing engine for smart city parking lots that dynamically adjusts rates based on demand, location-based competition, and environmental context (traffic, vehicle type, etc). This project simulates urban-scale parking lot optimization using streaming data and edge-level intelligence.

---

## Overview

Urban congestion and inefficient parking pricing lead to traffic delays, lost revenue, and poor utilization. This system models how cities can use data to drive dynamic pricing for parking in real-time, optimizing:

* Space utilization
* Revenue generation
* Customer fairness

Built using Pathway's stream processing framework, the system processes simulated CSV streams and implements three distinct pricing strategies (models).

---

## Tech Stack

| Layer             | Tools/Frameworks                           |
| ----------------- | ------------------------------------------ |
| Language          | Python 3.11                                |
| Stream Engine     | Pathway (for real-time table processing)   |
| Visualization     | Panel + Bokeh                              |
| IDE/Notebook      | Jupyter Notebook / Google Colab            |
| Data Source       | Simulated CSV stream (parking\_stream.csv) |
| Architecture Tool | Mermaid.js (diagram below)                 |

---

## Architecture Diagram


flowchart TD
    A[parking_stream.csv (simulated real-time feed)]
    A --> B[Pathway Streaming Ingest]
    B --> C[Model 1: Timestamp + Schema Parsing]
    C --> D[Model 2: Demand-Based Pricing]
    D --> E[Model 3: Competitive Adjustment]
    E --> F[Final Table: final_price]
    F --> G[Live Price Visualization (Panel + Bokeh)]


---

## Models Overview & Algorithms

### Model 1 — Preprocessing & Timestamp

* Reads parking\_stream.csv in simulated real-time
* Applies schema parsing
* Converts string timestamp → datetime
* Output: table with real-time streaming rows

🛠️ Tools: Pathway.replay\_csv, dt.strptime

---

### Model 2 — Demand-Aware Pricing

Dynamically adjusts price based on real-time indicators:

Formula:

* demand\_raw =
  α × (Occupancy / Capacity) +
  β × QueueLength −
  γ × traffic\_score +
  δ × IsSpecialDay +
  ε × vehicle\_score

* demand\_norm = min(1, max(0, demand\_raw / 5))

* price = base\_price × (1 + λ × demand\_norm)

Coefficients (Tunable):

| Symbol | Factor             | Default |
| ------ | ------------------ | ------- |
| α      | Occupancy weight   | 1.0     |
| β      | Queue length       | 0.1     |
| γ      | Traffic penalty    | 0.5     |
| δ      | Special day bonus  | 0.3     |
| ε      | Vehicle type score | 1.0     |
| λ      | Demand scaler      | 0.5     |

💡 Algorithm: Feature-weighted scoring → normalization → capped pricing

---

### Model 3 — Competition-Aware Adjustment

Incorporates spatial dynamics: adjusts price downwards if nearby competitors are cheaper.

Steps:

1. Cross join all parking lots → lot pairs
2. Compute Haversine distance between locations
3. Filter only pairs where price\_right < price\_left
4. For each lot, compute avg\_price\_diff = avg(price\_left − price\_right)
5. final\_price = price − φ × avg\_price\_diff

Parameters:

| Variable | Description             | Default |
| -------- | ----------------------- | ------- |
| φ (phi)  | Competition sensitivity | 0.5     |

Algorithm: Cross join → spatial filter → groupby-reduce → price correction

Final Output: final\_price ∈ \[min\_price, max\_price]

---

## Project Workflow

1. Ingest simulated data as a stream
2. Parse timestamps and schema (Model 1)
3. Compute demand-weighted price (Model 2)
4. Identify and incorporate cheaper nearby competitors (Model 3)
5. Output dynamic final\_price
6. Visualize in real-time with Bokeh + Panel

---

## Sample Output

* Time series plot of final\_price per lot
* Reacts to spikes in demand, traffic, and competition
* Highly tunable via phi, lambda, alpha, etc.

---

## Files Included

| File Name                   | Description                                |
| --------------------------- | ------------------------------------------ |
| Sample\_Notebook.ipynb      | Base model development notebook            |
| dataset.csv         | Sample CSV used for simulation             |
| README.md                   | Project documentation                      |

---


