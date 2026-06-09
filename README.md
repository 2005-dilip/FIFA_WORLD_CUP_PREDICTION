Cluade Prompt:
Upload all the csv file to claude 

Project: WC 2026 Winner Prediction Model**
**Goal**
Build a machine learning model to predict the FIFA World Cup 2026 winner using a two-stage approach:
- Stage 1: Train a match predictor using historical data
- Stage 2: Simulate the full WC 2026 tournament and output win probabilities per team
**All datasets you have (upload all of these)**
| File | Size | What it is |
|---|---|---|
| train.csv | 17 KB | One row per team per WC (2002–2022) with features + target labels (winner, finalist, semi_finalist, quarter_finalist) |
| test.csv | 5 KB | Same format, 48 WC 2026 teams, no labels — this is what you predict on |
| results.csv | 3,635 KB | 44k international match results 1872–2026 — main training data for match predictor |
| shootouts.csv | 29 KB | Penalty shootout outcomes for knockout rounds |
| former_names.csv | 2 KB | Team name changes for data cleaning |
| goalscorers.csv | 3,181 KB | Individual goalscorer data — optional, skip for now |
| FIFA2026_schedule.csv | 9 KB | WC 2026 match schedule |
| FIFA2026_schedule_Fixtures.csv | 11 KB | WC 2026 fixtures with Elo ratings (home_elo, away_elo, elo_diff — 21 rows missing Elo for UEFA playoff teams) |
| future_match_probabilities_baseline | 9 KB | Pre-computed baseline probabilities — useful reference |
**Features in train.csv / test.csv**
- Recent team form
- Goals scored and conceded
- FIFA ranking information
- Squad age
- Squad market value
- Host status
- Historical World Cup experience (appearances, titles)
- Target labels: winner, finalist, semi_finalist, quarter_finalist (empty in test.csv)
**Architecture**

train.csv (2002–2022 WC data)
         ↓
Train classification model (XGBoost/Random Forest)
Target: winner / finalist / semi_finalist / quarter_finalist
         ↓
Validate on held-out WC editions (2018, 2022)
         ↓
results.csv → match-level predictor (Win/Draw/Loss probabilities)
         ↓
FIFA2026_schedule_Fixtures.csv → simulate group stage
         ↓
Monte Carlo tournament simulation 1000x
         ↓
test.csv → predict 2026 outcomes
         ↓
Output: win probability % per team

**Key decisions made**
- Two models working together: team-level classifier + match-level predictor
- Elo diff is the strongest single feature for match prediction
- UEFA playoff placeholder teams have missing Elo → fill with ~1650
- date column in fixture list is 100% null → drop it
- injury flag columns are all zeros → drop them
- goalscorers.csv skip for now — add later if model needs improvement
- Training uses all nations in results.csv, not just 48 WC teams — more data = better generalization
**Next steps in new chat**
1. Upload all CSV files
2. Inspect and clean each file
3. Train team-level classifier on train.csv (predicts quarter_finalist / semi_finalist / finalist / winner)
4. Train match-level predictor on results.csv (predicts Win/Draw/Loss per match)
5. Validate both models on WC 2018 and 2022
6. Simulate full WC 2026 bracket with Monte Carlo (1000x)
7. Apply to test.csv → output final predictions
