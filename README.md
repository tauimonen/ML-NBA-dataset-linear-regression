# 🏀 NBA Team Win Percentage Prediction (Linear Regression)

This project builds and evaluates a **linear regression model** to predict NBA teams’ win percentages (`W_PCT`) based on traditional per-game statistics from the **NBA Stats API**.

---

## 📈 Project Overview
- **Goal:** Predict a team’s win percentage using standard box score metrics.  
- **Data Source:** [NBA Stats API - LeagueDashTeamStats]([https://stats.nba.com/stats/leaguedashteamstats](https://nba-apidocumentation.knowledgeowl.com/))  
- **Seasons Used:**
  - **Training Data:** 2023–24 season  
  - **Testing Data:** 2024–25 (forward prediction) and 2022–23 (backward validation)

---

## ⚙️ Methods
- **Model:** Linear Regression  
- **Libraries:** `nba_api`, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`  
- **Key Features:**  
  `FG_PCT`, `FG3_PCT`, `PTS`, `DREB`, `TOV`, `PLUS_MINUS`

---

## 📊 Performance

| Test Season | MSE | RMSE | R² |
|--------------|-----|------|----|
| 2024–25 | 0.0012 | 0.034 | 0.955 |
| 2022–23 | 0.0016 | 0.040 | 0.892 |

**Interpretation:**  
The model generalizes well, showing high predictive accuracy across seasons. Team-level statistics such as shooting efficiency and point differential are strong indicators of future success.

---

## 🔍 Key Insights
- NBA team performance is relatively consistent from season to season.  
- Shooting accuracy and point differential (`PLUS_MINUS`) are the most reliable predictors of winning.  
- Linear regression provides a simple yet powerful baseline for sports analytics.

---
## Author 
tauimonen. Developed for research and educational purposes in NBA performance analytics.
