# ⚽ World Cup Predictor: Market-Calibrated Quantitative Model

## 📋 Project Overview & Architectural Framework

This repository contains an end-to-end mathematical and probabilistic simulation engine built in Python to forecast the outcome of the World Cup Final matchup between Spain and Argentina. It uses a quantitative engine fueled by live FIFA performance statistics and calibrated via reverse engineering against real-world betting market implied probabilities.

## 🛠️ Backend Architecture & Data Pipeline

The analytical engine follows a modular architecture structured across the following stages within a Jupyter Notebook:

1. **Resilient Data Ingestion (Selenium):** Developed a modular web scraper using Google Chrome Driver to extract statistics across FIFA's 7 core performance categories (Attack, Distribution, Defense, Disipline, Goalkeeping, Movement, Physical). The architecture implements explicit waits (`WebDriverWait` and wildcard `XPATH` selectors) to systematically mitigate `StaleElementReferenceException` errors and cookie banner interruptions.
2. **Data Sanitization & Consolidation (Pandas):** Multi-dimensional arrays were mapped using `zip()` into structural dictionaries to build a unified DataFrame (`model_df`). Feature datatypes were mass-converted from `object` to `int64`/`float64` while isolating the categorical team index using `.apply(pd.to_numeric)`.
3. **Cross-Feature Engineering:** Built normalized composite scores (0 to 1 scale) for offensive (`of_score`) and defensive (`def_score`) performance. Negative metrics (e.g., *Goals Conceded* and *Ball Recovery Time*) were mathematically inverted using the formula `1 - (X / X.max())`, ensuring that high-performing defensive attributes act as positive weights.
4. **Calibrated Poisson Distribution Engine:** Modeled discrete independent events on expected goals (`xG`) and corner kicks, normalized by matches played (7). 
   * **Market Governance Integration:** Offensive $\lambda$ values were cross-referenced against the opponent's defensive metrics, introducing a mitigation hyperparameter (`def_weight`). This factor was reverse-engineered using numerical constraints to perfectly align with real-world betting market data (an American moneyline of +200 for a Draw, representing a ~33.33% implied probability), protecting the model from raw unit distortions.
5. **Vectorized Monte Carlo Simulation (NumPy):** Executed 10,000 matches in-memory within milliseconds via vectorized array operations (`np.random.poisson`), extracting definitive 90-minute win/draw rates and a symmetric 100%-balanced ball possession distribution.

## 🎯 Repository Deliverables
* `/notebooks/predictor_backend.ipynb`: Documented, modularized python backend code.
* `/data/processed/`: Structured relational CSV tables formatted for Power BI ingestion.
* `/dashboards/predictor_analytics.pbix`: Interactive executive dashboard for quantitative data visualization.
