# SOC Alert Triage Using Semi-Supervised Learning (SSL) & Analyst-in-the-Loop  
### Reducing Alert Fatigue in Security Operations Centers (SOC)

This repository contains the implementation, experiments, and automation for the project  
**“Reducing SOC Alert Fatigue Using Semi-Supervised Tri-Training and Analyst-in-the-Loop Feedback."**

The goal is to build an AI-driven alert-triage pipeline that operates under realistic SOC constraints:
- **Minimal labeled data** (only 10% labeled, 90% unlabeled)
- **High recall requirement** 
- **Temporal drift** (Train: Mon–Thu, Test: Friday)
- **Real-world integration** (Wazuh SIEM)
- **Human feedback loop** (AITL learning)

---

# 📌 Project Overview

Modern SOCs receive thousands of alerts per day, most of which are false positives.  
Alert fatigue leads to missed incidents and overwhelmed analysts.

This project develops a **Semi-Supervised Learning (SSL) alert triage system** capable of:

### ✔ Automatically closing safe alerts  
### ✔ Escalating high-risk alerts  
### ✔ Sending uncertain alerts to analysts  
### ✔ Improving continuously from analyst feedback  

The system uses:
- **Tri-Training SSL** (three-model consensus pseudo-labeling)
- **Evasion-aware feature engineering**
- **Isolation-Forest anomaly signals**
- **Safety thresholding (Recall ≥ 99.9%)**
- **Analyst-in-the-Loop retraining**
- **Wazuh alert ingestion for final deployment**

---

# 📁 **Repository Structure**

| Folder/File | Purpose |
|-------------|---------|
| `data/` | CICIDS2017 raw CSVs |
| `processed/` | Cleaned & engineered Parquet files (Mon–Thu & Friday split) |
| `models/` | Saved SSL Tri-Training models & calibrators |
| `notebooks/` | Experiments, ablation studies, AITL results |
| `preprocess_phoenix.py` | Complete preprocessing pipeline (Phoenix Engine) |
| `tri_training_engine.py` | SSL Tri-Training implementation |
| `run_ssl_training.ipynb` | Main experiment notebook |
| `README.md` | Documentation (this file) |

---

# 🔧 **Technologies Used**

| Category | Tools |
|----------|-------|
| ML Frameworks | LightGBM, scikit-learn |
| SSL Algorithms | Tri-Training, Self-Training |
| Anomaly Detection | Isolation Forest |
| Deployment Target | Wazuh SIEM (forwarder → manager → indexer → dashboard) |
| Programming | Python 3.10 |
| Data Formats | Parquet, CSV |
| Visualization | Matplotlib, Seaborn |

---

# 🧪 **Methodology**

## **1. Phoenix Preprocessing Engine**
A robust, leakage-proof transformer that:

### ✔ Cleans + standardizes raw CICIDS2017  
✔ Extracts timestamps and applies Mon–Thu vs Friday split  
✔ Computes:
- Stateless features (ratios, flag anomalies, time features)
- Stateful features (port rarity, protocol rarity)
- Anomaly scores from **Isolation Forest** trained ONLY on benign traffic  

### ✔ Saves:
- `train_mon_thu.parquet`
- `test_friday.parquet`
- Metadata (feature list, distributions)

---

## **2. Semi-Supervised Tri-Training**


---

## **3. Safety-First Triage Engine**
Instead of using a naive classification threshold (0.5), we compute a threshold such that:

### **Recall_attack ≥ 99.9%  
False Negative Rate ≤ 0.1%**

The threshold is learned using a calibration set.

### Output:
- **Auto-Close Alerts** – very low probability of attack  
- **Review Alerts** – uncertain, analyst should inspect  
- **Critical Alerts** – high probability of attack  

This guarantees **zero major misses** even in SSL-based systems.

---

## **4. Analyst-in-the-Loop (AITL)**
A realistic simulation where the model improves as analysts verify uncertain alerts.

Each round:
1. Select most-uncertain alerts  
2. Reveal true labels  
3. Retrain Tri-Training  
4. Measure improvement in ROC, AP, Recall

Produces a **learning curve** that shows how SOC effectiveness compounds over time.

---

# 📊 **Results Summary (Example)**

| Metric | Value |
|--------|--------|
| ROC-AUC |  Need to add|
| SSL ROC-AUC | Need to add |
| Safety Threshold Recall |Need to add |


---

# ▶️ **How to Run the Project**

## **1. Run Preprocessing**
```bash
python preprocess_phoenix.py
