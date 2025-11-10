# Predicting NBA Success from Collegiate Basketball Data  
### Machine Learning–Based Draft Forecasting System  

---

## **Abstract**

This project develops an end-to-end machine learning system to forecast NBA success using collegiate basketball data. Leveraging over a decade of NCAA player statistics, the model identifies which players are most likely to transition successfully to the NBA.  

Two Random Forest–based classifiers were constructed: a baseline benchmark and an enhanced, calibrated model incorporating class balancing (SMOTE).  
The baseline achieved **AUC = 0.93** and **Recall = 50.8 %**, while the enhanced model improved recall to **65.9 %** and reduced false negatives by **19 players**.  

Beyond predictive accuracy, the system produces **draft boards per season**, ranking top prospects by predicted probability of NBA success. These outputs offer actionable insights for data-driven scouting and recruitment.

---

## **Data and Methodology**

### **Dataset and Preprocessing**

The dataset (`PlayerData.xlsx`) contains NCAA player statistics, demographics, and team-level context. After validation, **89 numeric columns** were retained for cleaning and transformation.  
The automated feature-cleaning pipeline performed:

- Dropped **0 features** with > 30 % missing values  
- Removed **2 near-constant** and **40 highly correlated** features (|r| > 0.85)  
- Reduced the dataset to **46 informative predictors**  

This yielded **9,881 training samples** and **5,356 test samples** with **6.6 % NBA** vs **93.4 % non-NBA** cases.  
The split was **chronological**, ensuring realistic forecasting and no data leakage.

---

## **Model Development**

### **Baseline Model — Random Forest (800 Trees)**

A Random Forest classifier (balanced class weights, `OOB = 0.9489`) served as the benchmark.  
Optimal decision threshold for maximum F1 ≈ 0.297.

| Metric | Score |
|:--|--:|
| **AUC** | 0.9315 |
| **PR-AUC** | 0.3881 |
| **Balanced Accuracy** | 0.7450 |
| **F1 Score** | 0.4507 |
| **Recall (NBA)** | 50.8 % |
| **Precision (NBA)** | 40.5 % |
| **Accuracy** | 97.1 % |

**Insights:**  
The model effectively distinguishes NBA prospects (AUC ≈ 0.93) but under-detects some true positives.  
Top predictors included **Value Added**, **Conference Rank**, **Defensive BPM**, and **Box Impact per 40** — metrics tied to all-around efficiency and contextual dominance.

---

### **Enhanced Model — SMOTE + Calibrated Random Forest (1,500 Trees)**

To improve minority-class detection and probability reliability, the final model integrated:  
- **SMOTE oversampling** to balance NBA vs non-NBA examples  
- **Isotonic calibration** (5-fold CV) for well-calibrated probabilities  
- **Threshold optimization** (max F1 ≈ 0.285)

| Metric | Score |
|:--|--:|
| **AUC** | 0.9355 |
| **PR-AUC** | 0.3669 |
| **Balanced Accuracy** | 0.8123 |
| **F1 Score** | 0.4278 |
| **Recall (NBA)** | 65.9 % |
| **Precision (NBA)** | 31.7 % |
| **Brier Score** | 0.0200 |

**Impact Summary**

| Metric | Baseline | Final | Δ (Change) |
|:--|--:|--:|--:|
| **Recall (NBA)** | 50.8 % | **65.9 %** | **+ 15.1 pp** |
| **False Negatives** | 62 | **43** | **– 19 players** |
| **Calibration** | — | Isotonic (5 folds) | — |

**Interpretation:**  
The calibrated model increases sensitivity to NBA-level talent while maintaining strong AUC.  
It “recovers” 19 players missed by the baseline model and provides well-calibrated probabilities for scouting.

---

## **Results and Draft Visualization**

The final system generates **draft boards per season**, ranking the top 30 players by predicted NBA success probability.  

- **2015-16:** Correctly elevates Jamal Murray, Pascal Siakam and others.  
- **2023-24 / 2024-25:** Highlights current prospects (e.g., Stephon Castle, Collin Murray-Boyles) with probabilities from 0.3–0.8.  

---

## **Summary and Conclusions**

This pipeline delivers a reproducible and interpretable framework for NBA draft forecasting:

- Reduced feature space from **89 → 46 variables** without loss of accuracy  
- **Baseline RF:** AUC = 0.93 | Recall = 50.8 %  
- **Final SMOTE + Calibrated RF:** AUC = 0.94 | Recall = 65.9 % | Brier = 0.02  
- **19 previously missed NBA players** correctly identified  
