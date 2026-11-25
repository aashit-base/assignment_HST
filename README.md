# Air Pollution and Clean-Air Policy in the Indo-Gangetic Plains

Analytics for Business and Society [HST-105]: Phase 2 empirical work.
Team Members: Aashit Kumar Singh, Bhaskar Bhushan and Dhruv Singh Bisht

This repository holds the code that illustrates our empirical strategy for PM2.5 in the Indo-Gangetic Plains: geo-analysis, forecasting and policy evaluation. The full strategy and the report are submitted separately as PDF files. The notebooks are an illustration of the method on a small set of stations, not final results.

## The three parts
1. `01_data_and_geo_analysis`: data cleaning, haversine neighbours, seasonal maps, distance decay, pollution wave and k-means airsheds (RQ0).
2. `02_forecasting`: random forest vs "tomorrow = today", tested on new years and new cities (RQ1).
3. `03_policy_evaluation`: weather-only "no-policy" model, COVID check, difference-in-differences for NCAP, spillover check and city scorecard (RQ2).

## How to run
### 1. Install
```
pip install -r requirements.txt
```
(Python 3.10 or newer. A GPU is optional; xgboost uses CUDA if it is available, otherwise the CPU.)

### 2. Quick run (no big downloads)
The processed data is already included in `data/processed/`, so Notebooks 02 and 03 can be run straight away. Open them in Jupyter or VS Code and run all cells in order.

### 3. Full replication (from raw data)
Download these two large files into `data/` (they are not in the repository because of GitHub's file size limit):
- `main_data.pkl` (CPCB station data from VayuBench, about 100 MB): https://huggingface.co/datasets/SustainabilityLabIITGN/VayuBench/resolve/main/preprocessed/main_data.pkl
- `india_states.geojson` (India state boundaries, about 150 MB): https://raw.githubusercontent.com/Tanmay53/india-state-boundries-geojson/master/india_states.geojson

`ncap_funding_data.pkl` is already in `data/` (source: https://huggingface.co/datasets/SustainabilityLabIITGN/VayuBench).

Then run the notebooks in order: 01, then 02, then 03. Notebook 01 downloads the weather from the free Open-Meteo API (no key needed) and saves it, so this only happens once. It also makes a small copy of the map file for fast maps.

## Project structure
```
assignment_HST/
├── README.md                          # This file
├── requirements.txt                   # Python packages
├── .gitignore                         # Keeps the two big raw files out of the repo
├── Phase_1_sub_final.pdf              # Our Phase 1 submission
├── Empirical_Strategy_Paper.pdf       # Our empirical strategy paper (Phase 2)
├── data/
│   ├── main_data.pkl                  # CPCB daily station data (download, not in repo)
│   ├── india_states.geojson           # India state boundaries (download, not in repo)
│   ├── ncap_funding_data.pkl          # NCAP funds released per city
│   └── processed/
│       ├── panel.csv                  # Station-day table made by Notebook 01
│       ├── weather.csv                # Daily weather per station from Open-Meteo
│       └── states-small.geojson       # Small, simplified copy of the state borders
├── notebooks/
│   ├── 01_data_and_geo_analysis.ipynb # Data, cleaning and geo-analysis (RQ0)
│   ├── 02_forecasting.ipynb           # Next-day PM2.5 forecast (RQ1)
│   └── 03_policy_evaluation.ipynb     # NCAP policy evaluation (RQ2)
└── figures_outputs/
    │   # nb1: data and geo-analysis
    ├── nb1-stations.csv               # The 15 selected stations
    ├── nb1-station-distances.csv      # Haversine distance between every pair of stations
    ├── nb1-seasonal-maps.png          # Mean PM2.5 per station in each season
    ├── nb1-pair-correlations.csv      # Correlation of daily anomalies for every station pair
    ├── nb1-distance-decay.csv         # Fitted a and L for winter and monsoon
    ├── nb1-distance-decay.png         # Similarity vs distance with fitted curves
    ├── nb1-pollution-wave.csv         # Lead-lag correlation of each city with Delhi
    ├── nb1-pollution-wave.png         # Lead-lag lines for five cities
    ├── nb1-silhouette.png             # Silhouette score for K = 2 to 8
    ├── nb1-airsheds.csv               # Airshed (cluster) of each station
    ├── nb1-airshed-map.png            # Map of the k-means airsheds
    │   # nb2: forecasting
    ├── nb2-forecast-scores.csv        # Test 1 scores: persistence vs random forest
    ├── nb2-forecast-vs-actual.png     # One Delhi station, winter 2023-24
    ├── nb2-predicted-vs-actual.png    # Scatter of forecast vs actual
    ├── nb2-rmse-bars.png              # RMSE of persistence vs random forest
    ├── nb2-feature-importance.png     # Which inputs the random forest uses
    ├── nb2-ablation.csv               # RMSE with and without the neighbour average
    ├── nb2-ablation.png               # Bar chart of the ablation
    ├── nb2-new-city-scores.csv        # Test 2 scores for each unseen city
    ├── nb2-new-city-skill.png         # Skill and hit rate per unseen city
    │   # nb3: policy evaluation
    ├── nb3-no-policy-baseline.png     # Delhi: actual vs no-policy PM2.5
    ├── nb3-monthly-gap.png            # Monthly average gap, all stations
    ├── nb3-covid-check.png            # April-May gap by year
    ├── nb3-did-results.csv            # Difference-in-differences results
    ├── nb3-did-lines.png              # Yearly gap, high- vs low-funded cities
    ├── nb3-spillover-results.csv      # Spillover regression results
    ├── nb3-city-scorecard.csv         # One row per city: gap, funds, group, airshed
    └── nb3-scorecard-map.png          # Map of the city scorecard
```

## Data sources
- CPCB station data via VayuBench (Sustainability Lab, IIT Gandhinagar): https://huggingface.co/datasets/SustainabilityLabIITGN/VayuBench
- NCAP funding data (VayuBench, same link as above)
- Open-Meteo historical weather API: https://open-meteo.com
- India state boundaries GeoJSON (Tanmay53 on GitHub, Apache 2.0): https://github.com/Tanmay53/india-state-boundries-geojson

## Notes
- We use a reduced set of 15 stations in 12 cities (Punjab to Bihar) so the notebooks run fast; no West Bengal station had enough data before 2018.
- Results are an illustration of the method; the policy results use only 10 cities and are not statistically significant.
- If a file in `data/processed/` looks broken, delete it and run Notebook 01 again; it will be rebuilt.
- The raw data files are only read, never changed.
