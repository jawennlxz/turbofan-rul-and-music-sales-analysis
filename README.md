# Turbofan Engine RUL Prediction and Music Sales Analysis

Two data programming projects in Python:

1. **Turbofan engine remaining useful life (RUL):** exploring NASA's C-MAPSS jet engine degradation data and predicting how many cycles each engine has left before failure.
2. **Best-selling music artists:** scraping Wikipedia's tables with BeautifulSoup, cleaning them, and analysing sales across eras, genres, careers and countries.

Module: ICT233 Data Programming, Singapore University of Social Sciences (2026)

## Contents

| Notebook | What it does |
|---|---|
| `turbofan_rul_analysis.ipynb` | Loading and screening sensor data, smoothing, operating-regime clustering, baseline correction and RUL modelling |
| `best_selling_artists_analysis.ipynb` | Web scraping, text cleaning and comparative analysis, finishing with SQL |

Both notebooks include their outputs and charts, and a written interpretation after each step.

## 1. Turbofan engine RUL (NASA C-MAPSS)

- **Careful loading:** each row ends with trailing spaces, so reading the file with `sep=" "` creates two empty columns. `sep=r"\s+"` fixes this. The readme also lists 26 sensors when the file has only 21.
- **Sensor screening:** 8 columns are effectively constant in FD001, but each of them varies in FD002, so dropping them is not safe to reuse across datasets.
- **Smoothing:** a 10-cycle trailing mean cuts cycle-to-cycle noise about 9 times while keeping the sharp end-of-life rise.
- **The baseline problem:** the same sensor reading can mean very different remaining lives on two engines. Subtracting each engine's own starting level makes engines comparable.
- **Operating regimes:** k-means separates FD002's six operating conditions, so sensors can be standardised within each one.
- **Modelling:** Linear Regression (RMSE 31.95) and Random Forest (RMSE 32.93) score almost the same. The residuals tell them apart: Linear Regression predicts impossible negative RUL for 3 engines, while Random Forest is more accurate for engines close to failure.
- **Scoring:** the asymmetric exponential cost function punishes late predictions far more than early ones. One engine predicted 50 cycles late costs more than five engines each 10 cycles late.

## 2. Best-selling music artists

- **Scraping:** kept only the 6 artist tables out of 11 on the page, took each table's sales tier from its heading, removed citation markers, and extracted worldwide figures from mixed regional text. The result is 128 artists.
- **Certified vs claimed sales:** certified sales are strongly biased by era. The median certified-to-claimed ratio is 0.49 for 1960s acts and 2.40 for 2010s acts, so claimed sales give a fairer comparison across eras.
- **Genres:** splitting and normalising genre labels reduces 23 raw labels to 17.
- **Sales per active year:** The Beatles lead on total and per-year sales. Acts with short careers, such as Tupac Shakur (from 110th to 4th) and Nirvana, rise sharply when sales are divided by active years.
- **Concentration (SQL):** the United States accounts for 60.8% of listed sales, and with the United Kingdom 82.5%.

## How to run

```bash
pip install pandas numpy matplotlib scikit-learn beautifulsoup4 jupyter
```

- **Turbofan notebook:** download the C-MAPSS dataset from NASA's Prognostics Data Repository and place the `CMAPSSData` folder inside `data/`.
- **Music notebook:** save a copy of Wikipedia's *List of best-selling music artists* page as `best_selling_artists.html` next to the notebook.

The data files are not included here.

## Skills demonstrated

- Robust data loading and validation with pandas
- Time-series smoothing, per-unit baseline correction and k-means clustering
- Regression modelling and residual analysis with scikit-learn
- Web scraping and text cleaning with BeautifulSoup and regular expressions
- SQL aggregation with SQLite

## Author

Jawenn Lim
