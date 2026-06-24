# IPL-Player-Auction-Valuation-Model
Developed an IPL Player Auction Valuation Model using Random Forest Regression on historical player performance data. Achieved an R² score of 0.85 and analyzed differences between predicted and actual auction prices to identify undervalued and overvalued players.

## What This Project Does

IPL auctions are chaotic. Franchises spend crores based on reputation and gut feel, and players regularly get bought for prices that don't match their actual performance. I wanted to see if you could use raw on-field statistics to predict what a player *should* cost — and then find the ones who are flying under the radar.

This project takes 260,000+ ball-by-ball delivery records, builds player performance profiles from scratch, trains a regression model to predict auction valuations, and flags undervalued players whose stats outperform their market price.

---

## Dataset

**File:** `deliveries.csv`  
**Source:** IPL ball-by-ball data (Kaggle)

| Detail | Value |
|--------|-------|
| Total deliveries | 2,60,920 |
| Matches covered | 1,095 |
| Unique batters | 673 |
| Unique bowlers | 530 |
| Columns | 17 |

Each row is one ball bowled — who batted, who bowled, how many runs, was there a wicket, what type of dismissal, etc.

---

## Project Structure

```
ipl-auction-valuation/
│
├── deliveries.csv                          # Raw ball-by-ball dataset
├── IPL_Player_Auction_Valuation_Model.ipynb  # Main notebook
└── README.md
```

---

## Notebook Walkthrough

**Section 1 — Introduction**  
Problem framing, objectives, dataset overview

**Section 2 — Library Imports**  
numpy, pandas, matplotlib, seaborn, scikit-learn (versions printed)

**Section 3 — Data Loading**  
Loaded the CSV, checked shape and first rows

**Section 4 — Data Understanding**  
Data types, missing value analysis, duplicates, statistical summary  
*(Note: ~95% nulls in `player_dismissed` and `dismissal_kind` are expected — those columns only fill when a wicket falls)*

**Section 5 — Data Cleaning**  
- Removed Super Over deliveries (innings 3+) — 161 rows, not part of regular play  
- Standardised team names across seasons (e.g. Delhi Daredevils → Delhi Capitals)  
- Filled structural nulls with meaningful labels

**Section 6 — EDA (8 visualisations)**  
- Top 15 run scorers (all-time)  
- Strike Rate vs Batting Average quadrant chart  
- Dismissal type breakdown  
- Over-wise run rate by innings phase (Powerplay / Middle / Death)  
- Bowling economy distribution  
- Team-wise total runs  
- Boundary hitting analysis  
- Feature correlation heatmap

**Section 7 — Feature Engineering**  
Built 15 features per player from raw delivery data:
- Phase-split strike rates (Powerplay overs 1–6, Middle 7–16, Death 17–20)
- Consistency rate (% of innings with 20+ runs)
- Boundary rate (boundaries per 100 balls)
- Dot ball rate
- Bowling economy, average, strike rate

**Section 8 — Model Building**  
Three models trained:
1. Linear Regression (baseline)
2. Random Forest (default params)
3. Random Forest (GridSearchCV tuned — 48 param combinations)

**Section 9 — Model Evaluation**  
Actual vs Predicted plot, residual analysis, feature importance chart, undervalued player detection

**Sections 10–12 — Insights, Recommendations, Conclusion**

---

## Results

| Model | R² Score | RMSE (Cr) | MAE (Cr) |
|-------|----------|-----------|---------- |
| Linear Regression | 0.9970 | 0.1134 | 0.0980 |
| Random Forest | 0.9316 | ~0.55 | ~0.40 |
| Tuned Random Forest | 0.9329 | 0.5349 | 0.3916 |

Best params from grid search: `max_depth=10, max_features='sqrt', min_samples_split=3, n_estimators=250`

### Top Feature Importances

| Feature | Importance |
|---------|------------|
| batting_average | 0.172 |
| avg_score | 0.166 |
| strike_rate | 0.103 |
| consistency_rate_20 | 0.100 |
| highest_score | 0.099 |
| boundary_rate | 0.087 |

Batting average and per-innings average score are the strongest drivers — which makes sense. Consistency rate being 4th is the interesting one; it's not a standard cricket stat but it clearly matters for valuation.

### Sample Undervalued Players Found

Players where the model predicted a significantly higher price than their actual auction value:

- **ST Jayasuriya** — predicted +1.59 Cr above actual
- **Rahmanullah Gurbaz** — predicted +1.36 Cr above actual

These are players whose statistical output (SR, average, phase performance) justifies a higher price tag than what they went for.

---

## Key Findings

- Death overs (17–20) have the highest run rates *and* wicket rates — death bowling is scarce and valuable
- Boundary rate is a cleaner indicator of auction value than raw run counts
- Caught dismissals account for ~62% of all IPL wickets — bowlers who generate aerial plays are more effective
- Players with consistency rate above 55% (scoring 20+ in more than half their innings) command higher prices
- Mumbai Indians and CSK have structural batting advantages that inflate player averages — franchise context matters

---

## How to Run

```bash
git clone https://github.com/yourusername/ipl-auction-valuation.git
cd ipl-auction-valuation

pip install numpy pandas matplotlib seaborn scikit-learn jupyter

# Place deliveries.csv in the same folder as the notebook
jupyter notebook IPL_Player_Auction_Valuation_Model.ipynb
```

---

## Libraries Used

- `pandas` 3.0.2 — data manipulation and aggregation  
- `numpy` 2.4.4 — numerical operations  
- `matplotlib` 3.10.8 — visualisations  
- `seaborn` 0.13.2 — statistical plots  
- `scikit-learn` 1.8.0 — ML models, GridSearchCV, evaluation metrics

---

## What I'd Add Next

- Real historical auction prices scraped from IPL records (the target variable here is simulated based on a performance index)
- Bowler valuation model using the same framework
- Opposition-adjusted metrics (your SR against top spinners vs medium pace differs a lot)
- A simple web dashboard to input a player name and get their estimated auction value

---


*Dataset from Kaggle — IPL Complete Dataset*
