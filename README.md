# Profit-Driven Client Targeting (Python)

> **Academic group project** · Predictive Analytics, M.Sc. Business Analytics, Católica Lisbon School of Business & Economics · Spring 2026  
> Team: Alexander Wanstrath, Berkant Ismail Kahveci, Francisca Menano, Giovanni Cantù

*Course case set in the context of an asset manager's retail channel. The data was provided by the course; the project is not affiliated with or endorsed by the company named in the case.*

## Business case
Each week, a retail engagement team receives about 7,700 client leads but can contact at most 1,200. Every call costs €5, and a client who invests generates a 4.5% commission on the amount invested. The goal was to decide each week which clients to contact to maximise expected profit.

The project ran as a live, five-week campaign: each week we submitted a client selection, received the actual outcomes and used them to retrain the models.

| Week | Approach |
|---|---|
| 1 | Descriptive statistics and business rules |
| 2 | Classification: who will invest? |
| 3 | Regression: how much will they invest? |
| 4 | Combined expected-profit model |
| 5 | Final calibrated combined model |

## Data
About 12,200 historical client records (baseline data plus outcomes from weeks 1 to 4) with demographics, account balance, loans and marketing history. Only 11.4% of clients invested, so class imbalance shaped most modelling decisions. The data is not included in this repository.

## Approach
1. **Preprocessing:** scikit-learn pipelines to prevent data leakage, with imputation, one-hot encoding with rare-category grouping and scaling
2. **Classification:** seven models (Logistic Regression, KNN, Ridge, Lasso, Random Forest, HistGradientBoosting, XGBoost) compared with 5-fold stratified cross-validation on ROC-AUC, recall and log loss, plus stacking and nested cross-validation
3. **Probability calibration:** Platt scaling, so predicted probabilities can be used directly in the profit formula
4. **Regression:** six models (OLS, Ridge, Lasso, ElasticNet, Random Forest, HistGradientBoosting) trained on confirmed investors only
5. **Combined model:** every client is ranked by expected profit

   **EP = P(invest) × E(investment amount) × 4.5% − €5**

   and only clients with positive expected profit are contacted

## Key results
- **Classifier:** calibrated Random Forest with ROC-AUC 0.79 and 69% recall, capturing most investors in the top 16% of leads at a lift above 3×
- **Regressor:** Lasso trained on investors only, R² 0.94 and RMSE €162, compared with R² 0.24 for OLS on the full dataset. Account balance is by far the strongest driver of investment volume
- **Final campaign:** 885 clients selected, all with positive expected profit, with an average conversion probability of 31% versus a base rate of 11%
- **Realised profit:** €4,534 against a predicted €5,990. The gap is partly explained by a shift in the client mix toward lower-converting segments

## Recommendations
1. Rank clients by expected profit instead of conversion probability or investment amount alone
2. Treat probability calibration as a mandatory step before probabilities enter the profit formula
3. Train the investment-amount model on confirmed investors only when most clients invest nothing

## Tools
Python · pandas · scikit-learn · XGBoost · matplotlib · seaborn

## Files
- [`client_targeting_models.ipynb`](client_targeting_models.ipynb): final modelling notebook (week 5), covering data overview, classification, calibration, regression and the combined model
- [`final_report.pdf`](final_report.pdf): final report including the week 5 results
- [`pitch_deck.pdf`](pitch_deck.pdf): management presentation
