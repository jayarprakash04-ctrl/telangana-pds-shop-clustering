# Telangana PDS Analytics: Shop Performance Clustering & Anomaly Profiling

Segmenting ~17,000 Telangana Fair Price Shops into behavioral personas and flagging
anomalous shops using unsupervised machine learning, built for the GUVI-HCL Data
Science capstone project.

## Problem Statement

The Telangana State Civil Supplies Department manages thousands of Fair Price Shops
(FPS) to ensure food security. This project integrates transactional, card-status, and
geospatial data to identify shop performance clusters and flag shops with unusual
patterns (e.g. potential ghost beneficiaries or stock diversion), supporting three
business use cases: policy impact analysis, fraud prevention, and logistics optimization.

## Key Findings

- **4 shop personas** identified via K-Means (k=4, chosen via silhouette analysis):
  - **Low-Volume Rural / Steady** (10,537 shops) — stable, low-traffic rural shops
  - **Urban Portability Hub** (1,206 shops) — high-traffic, ONORC-heavy shops in Hyderabad/Medchal/Ranga Reddy
  - **Mid-Volume Semi-Urban** (4,266 shops) — transitional segment
  - **Volatile / Under-Utilized** (894 shops) — erratic activity, low utilization; priority for operational review
- **528 shops (3.1%)** flagged as anomalies via DBSCAN — mostly large, high-portability,
  high-volatility shops rather than simple over-claiming. A smaller subset shows extreme
  utilization (up to 6x entitled cards), the strongest fraud-audit candidates.

Full details in [`Telangana_PDS_Final_Report.docx`](./Telangana_PDS_Final_Report.docx).


## Project Structure

```
telangana-pds-shop-clustering/
├── PDS_Analysis.ipynb              # Full pipeline: EDA, feature engineering, PCA, K-Means, DBSCAN
├── app.py                          # Streamlit dashboard
├── requirements.txt
├── data/                           # Raw data (gitignored - see Dataset section)
│   ├── transactions_2023.csv/
│   ├── transactions_2024.csv/
│   ├── transactions_2025.csv/
│   ├── card_status.csv/
│   └── fps_location.csv/
├── outputs/
│   ├── shop_features_clustered.csv # Feeds the dashboard
│   └── cluster_profile.csv
├── Telangana_PDS_Final_Report.docx
└── README.md
```

## Dataset

Source: [Telangana Open Data Portal](https://data.telangana.gov.in/)

- **Transactions**: monthly shop-wise commodity and portability records, 2023-2025
- **Card Status**: entitlement snapshot (October 2022) — total ration cards per shop
- **FPS Locations**: shop coordinates, active/inactive status

Raw CSVs are not included in this repo (large, publicly downloadable). Download them
from the portal links above and place them in the `data/` folder using the structure
shown above before running the notebook.

## Methodology

1. **Data Integration** — consolidated multi-file yearly transaction data, joined with
   card status and location data on `shopNo`
2. **EDA** — trend analysis (portability growth), correlation (entitlement vs execution),
   distribution checks (commodity by district), seasonality
3. **Feature Engineering** — utilization ratio, rice dependency, portability ratio,
   volatility (coefficient of variation), all computed at shop level and standardized
4. **Clustering** — PCA for visualization, K-Means (k=4, selected via elbow/silhouette
   analysis), DBSCAN for anomaly detection (eps=0.85, tuned via k-distance plot)
5. **Dashboard** — interactive Streamlit app with district/cluster filters, geospatial
   map, and shop-vs-cluster-average lookup

See the Final Report for full methodology notes and limitations (in particular, the
card-status data is a single 2022 snapshot, not a time series — this affects how
utilization ratio should be interpreted).

## How to Run

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Add the data
Download the datasets from the Telangana Open Data Portal and place them in `data/`
as described above.

### 3. Run the analysis notebook
Open `PDS_Analysis.ipynb` in Jupyter or VS Code and run all cells top to bottom. This
produces `outputs/shop_features_clustered.csv`, used by the dashboard.

### 4. Launch the dashboard
```bash
streamlit run app.py
```
Opens at `localhost:8501`. Filter by district or cluster, view the geospatial cluster
map, and look up individual shops (e.g. `2411015`) against their cluster average.

## Tech Stack

Python, Pandas, Scikit-learn, Streamlit, Folium, Matplotlib, PCA, K-Means, DBSCAN

## Deliverables

- [x] Jupyter Notebook — EDA & model development
- [x] Streamlit dashboard (`app.py`)
- [x] Final Report
- [x] GitHub repository

## Author
Jayaprakash R