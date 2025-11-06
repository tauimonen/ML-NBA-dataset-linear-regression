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

## Author
Developed by tauimonen (2025) for research and educational purposes in NBA performance analytics.


---

# NBA Win Percentage Prediction - Model Evaluation & Analysis

## Executive Summary

This linear regression project aimed to predict NBA team win percentage (W_PCT) using traditional box score statistics. Through iterative refinement and diagnostic testing, we discovered critical insights about feature selection, multicollinearity, and the limitations of predictive modeling in sports analytics.

---

## Model Evolution & Key Findings

### Phase 1: Initial Model with PLUS_MINUS
**Features:** FG_PCT, FG3_PCT, PTS, DREB, TOV, PLUS_MINUS  
**Results:**
- R² (2024-25): 0.955
- R² (2022-23): 0.892
- RMSE: 0.034

**Critical Issue:** PLUS_MINUS (point differential) accounted for approximately 66% of the model's explanatory power. This variable is essentially a summary statistic of winning itself—teams that outscore opponents naturally have higher win percentages. While not technically data leakage, it made the model trivial and non-instructive.

### Phase 2: Removed PLUS_MINUS
**Features:** FG_PCT, FG3_PCT, PTS, DREB, TOV  
**Results:**
- R² (2024-25): 0.299
- R² (2022-23): 0.222
- RMSE: 0.134

**Key Insight:** Performance dropped dramatically, revealing that the remaining features had modest predictive power. This exposed severe multicollinearity issues among shooting statistics.

### Phase 3: Diagnostic Analysis - VIF Testing

**Variance Inflation Factor Results:**
```
Feature          VIF
FG3_PCT      2,121.65  ← Catastrophic
FG_PCT       1,831.57  ← Catastrophic  
DREB         1,116.49  ← Severe
TOV            126.41  ← Moderate
```

**Interpretation:** VIF > 10 indicates problematic multicollinearity; VIF > 1000 renders the model unreliable. FG_PCT and FG3_PCT are nearly perfectly collinear because three-point shots are included in overall field goal percentage calculations.

**Observed Symptoms:**
- Unstable coefficients (PTS had negative coefficient: -0.0018)
- FG3_PCT dominated with coefficient of 7.34 while FG_PCT was only 0.84
- Standard errors likely inflated (unreliable statistical inference)

### Phase 4: Regularization Solutions

**Ridge Regression:**
- R²: 0.458 (+99% improvement over basic linear regression)
- Shrinks coefficients to reduce multicollinearity impact
- Retains all features with dampened weights

**Lasso Regression (BEST MODEL):**
- R²: 0.473 (+106% improvement)
- Automatic feature selection through L1 penalty
- **Selected features:** DREB (0.055), TOV (-0.069)
- **Eliminated features:** FG_PCT (0.0), FG3_PCT (0.0)

### Phase 5: Refined Model with True Shooting & Advanced Metrics

**Features:**
- TS_PCT — True Shooting Percentage (overall scoring efficiency)  
- DREB — Defensive rebounds  
- AST_TOV_RATIO — Assist-to-turnover ratio (ball control)  
- STL — Steals (defensive pressure)  
- BLK — Blocks (rim protection)  
- PFD — Personal fouls drawn (offensive aggressiveness)  

**Results (Linear Regression):**
- Mean Squared Error (forward): 0.0089  
- Root Mean Squared Error (forward): 0.094  
- R² (forward): **0.652**  
- Mean Squared Error (backward): 0.0057  
- Root Mean Squared Error (backward): 0.075  
- R² (backward): **0.607**

**Ridge Regression (standardized):**
- R²: **0.674**
- Top standardized coefficients:
  - TS_PCT: **0.0819**
  - DREB: **0.0395**
  - AST_TOV_RATIO: **0.0276**
  - STL: **0.0412**
  - BLK: **−0.0120**
  - PFD: **0.0255**

**Variance Inflation Factor (VIF):**
| Feature | VIF |
|----------|------|
| TS_PCT | 2609.99 |
| DREB | 1242.79 |
| PFD | 860.46 |
| AST_TOV_RATIO | 230.83 |
| STL | 218.87 |
| BLK | 52.20 |

---

### Observations & Interpretation

1. **Strong Generalization (R² ≈ 0.65):**  
   The refined model explains around **65% of variance** in team win percentage using *only interpretable basketball metrics* — no leakage variables like PLUS_MINUS.  
   Both forward and backward validation confirm solid generalization (R² = 0.65 → 0.61).

2. **True Shooting Dominates:**  
   `TS_PCT` emerges as the most influential predictor. It captures holistic scoring efficiency, integrating FG%, 3P%, and FT% into one robust metric — eliminating collinearity between separate shooting stats.

3. **Defense & Discipline Matter:**  
   Rebounding (DREB), steals (STL), and foul drawing (PFD) all have meaningful positive coefficients. These reflect **effort-based, repeatable team qualities** that persist across seasons.

4. **Negative BLK Coefficient:**  
   The small negative weight on blocks (−0.012) may suggest that high block rates sometimes correlate with **riskier defensive styles** or poor team rebounding positioning.

5. **VIF Still High — But Managed:**  
   Multicollinearity remains substantial due to natural correlation in team stats, yet Ridge regression effectively **stabilizes coefficients** and prevents overfitting.  
   High VIF is diagnostic, not fatal, when regularization is applied.

6. **Improved Predictive Stability:**  
   Compared with earlier models (R² = 0.30–0.47), the Phase 5 configuration delivers both **higher accuracy** and **stronger interpretability** — a clear step forward.

---

### Analytical Insights

- **Shooting quality matters, but only when aggregated correctly.**  
  Replacing raw FG% and 3P% with TS_PCT dramatically reduced redundancy and improved predictive coherence.

- **Defensive consistency outperforms offensive variance.**  
  Metrics like DREB and STL appear more stable year-to-year than hot shooting streaks or offensive surges.

- **Ridge over plain Linear Regression:**  
  Regularization dampened the noise caused by highly correlated features and improved out-of-sample generalization.

- **Feature balance achieved:**  
  Offensive efficiency (`TS_PCT`), ball control (`AST_TOV_RATIO`), defensive stability (`DREB`, `STL`, `BLK`), and aggressiveness (`PFD`) together capture the multidimensional structure of team success.

---

### Assessment Summary

| Category | Evaluation |
|-----------|-------------|
| **Predictive Power** | Excellent – R² ~0.65 |
| **Generalization** | Strong across forward/backward seasons |
| **Interpretability** | High – all coefficients basketball-meaningful |
| **Multicollinearity** | Present but controlled via Ridge |
| **Model Stability** | Stable coefficients, consistent performance |
| **Educational Value** | Demonstrates practical feature engineering and diagnostic rigor |

---

### Insights for Future Work

- Integrate **pace-adjusted and opponent-based stats** (e.g., Opp FG%, ORTG, DRTG).  
- Extend data to **multi-year panels** for fixed-effects modeling.  
- Experiment with **non-linear algorithms** (Random Forest, XGBoost) to capture interaction effects.  
- Use **cross-validation** to refine α in Ridge/Lasso models.  
- Visualize results with:
  - Predicted vs Actual W_PCT scatter plot
  - Standardized coefficient importance chart
  - Residual diagnostics

---

### Phase 5 Conclusion

The refined model marks a major progression from naïve correlation-based analysis to a **statistically sound, interpretable, and generalizable framework** for NBA team success prediction.  
It validates that **true shooting efficiency, defensive control, and disciplined play** are reliable long-term indicators of winning basketball — while excessive reliance on summary stats like PLUS_MINUS or raw shooting splits can mislead analysts.

This phase completes the transition from theoretical modeling to practical, data-driven insight — a realistic portrayal of what modern sports analytics can (and cannot) predict.

---

## Key Insights After Phase 5: True Shooting & Advanced Metrics

### Defensive Stability > Shooting Volume

The refined Ridge model (R² ≈ 0.65) revealed that **defensive consistency and all-around efficiency** explain NBA win percentage more effectively than raw scoring metrics.  
True Shooting Percentage (TS_PCT) captures offensive quality, while DREB, STL, and AST_TOV_RATIO quantify discipline and defensive reliability — metrics that remain stable across seasons.

**Key Reasons:**
1. **True Shooting Unifies Efficiency:**  
   Combines FG%, 3P%, and FT% into one balanced variable — reducing redundancy and boosting model coherence.  
2. **Defensive Consistency Persists:**  
   Stats like rebounds, steals, and ball control depend more on system and effort than on roster volatility.  
3. **Multicollinearity Managed:**  
   Ridge regularization stabilizes coefficients despite correlated team stats, avoiding the instability seen in earlier linear models.

---

## Model Performance Summary

| Model Type | Features | R² (Forward) | R² (Backward) | RMSE |
|-------------|-----------|--------------|----------------|------|
| Linear + PLUS_MINUS | 6 | 0.955 | 0.892 | 0.034 |
| Linear (basic) | 5 | 0.299 | 0.222 | 0.134 |
| Lasso (v4) | 2 | 0.473 | — | ~0.090 |
| **Ridge (Phase 5)** | **6 refined** | **0.652** | **0.607** | **0.094 / 0.075** |

**Interpretation:**  
The Phase 5 model explains roughly **65% of win percentage variance**, cutting prediction error to ±9%.  
This is a realistic ceiling for single-season team-level prediction, given roster turnover and unpredictable dynamics.

---

## Statistical Review

- **Assumptions satisfied:** Continuous variables, independent observations, zero-mean residuals  
- **Partial:** Mild heteroskedasticity and residual normality deviations  
- **Multicollinearity:** High VIF (>1000) persists, but mitigated by Ridge regularization  

Regularization remains essential for stabilizing models with correlated performance metrics.

---

## Conclusions

1. **TS_PCT replaces FG% & 3P% effectively**, reducing redundancy.  
2. **Defensive and discipline stats (DREB, STL, AST_TOV_RATIO)** are more predictive than volume shooting.  
3. **Ridge regression achieves balance** between accuracy and interpretability.  
4. **Model generalizes well** both forward and backward in time (R² ≈ 0.65).  
5. **Remaining variance** likely reflects roster changes, injuries, and situational unpredictability.

---

## Recommendations for Next Steps

- Add **pace-adjusted and opponent-based** metrics (e.g., Opp FG%, Defensive Rating).  
- Perform **k-fold cross-validation** for better reliability.  
- Explore **non-linear models** (Random Forest, XGBoost) to capture interactions.  
- Extend to **multi-season panel data** for longitudinal analysis.  
- Visualize **feature importance** and **residuals** to interpret model bias.  

---

### Final Reflection

The evolution from high but misleading R² (0.955) with PLUS_MINUS to a balanced, interpretable R² (0.65) demonstrates the value of rigorous feature engineering and statistical discipline.  
**The refined model doesn’t just predict wins — it explains them.**

---