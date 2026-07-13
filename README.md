# Three-Tier Hierarchical XGBoost for IoT Intrusion Detection with Per-Level SMOTE and SHAP Explainability

A hierarchical and explainable Intrusion Detection System (IDS) for IoT networks built using **XGBoost**, **Per-Level SMOTE**, and **SHAP Explainability**. The system progressively classifies network traffic through three decision levels—Binary Detection, Attack Category Classification, and Specific Attack Identification—providing accurate, scalable, and interpretable intrusion detection.

Unlike conventional flat multiclass classifiers, this approach decomposes the detection task into multiple specialized stages, improving classification efficiency while maintaining transparent decision making through Explainable AI.

---

# Features

- Three-Tier Hierarchical Intrusion Detection System
- XGBoost-based Classification at All Three Levels
- Branched Inference Architecture
- Per-Level SMOTE for Class Balancing
- SHAP Explainability
- GPU-Accelerated Model Training
- Hierarchical Label Engineering
- Stratified Data Splitting
- Data Leakage Prevention
- Cross Validation
- Comprehensive Performance Evaluation
- Feature Importance Analysis
- Pareto Analysis

---

# Problem Statement

The rapid growth of Internet of Things (IoT) devices has significantly expanded the cyberattack surface, making efficient intrusion detection an essential component of modern network security.

Traditional intrusion detection systems generally treat attack detection as a single multiclass classification problem. As the number of attack categories increases, these models become increasingly complex, struggle with highly imbalanced datasets, and provide limited interpretability for security analysts.

This project addresses these challenges through a hierarchical machine learning framework that decomposes intrusion detection into three sequential decision stages while integrating explainable artificial intelligence for transparent model interpretation.

---

# Dataset

The project is developed using a curated subset of the **CICIoT2023** benchmark dataset.

The complete CICIoT2023 repository contains multiple traffic captures representing various IoT attack scenarios. This implementation utilizes **10 selected traffic files**, covering six attack categories and nine specific attack types.

After preprocessing and duplicate removal, the final dataset contains:

- **1,062,947 Network Flows**
- **39 CICFlowMeter Features**
- **6 Attack Categories**
- **9 Specific Attack Types**
- **1 Benign Traffic Class**

Attack Categories:

- DDoS
- DoS
- Spoofing
- Reconnaissance
- Web-Based Attacks
- Backdoor

The processed dataset is **not included** in this repository. Researchers interested in reproducing the experiments should obtain the original CICIoT2023 dataset from its official source and follow the preprocessing pipeline implemented in this project.

---

# System Architecture

```text
                           +---------------------------+
                           |     CICIoT2023 Dataset    |
                           +-------------+-------------+
                                         |
                                         v
                    +--------------------------------------+
                    | Data Cleaning & Preprocessing        |
                    |--------------------------------------|
                    | Duplicate Removal                    |
                    | Missing Value Handling               |
                    | Feature Scaling                      |
                    | Label Engineering                    |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | Hierarchical Label Construction      |
                    |--------------------------------------|
                    | Level 1 : Binary                     |
                    | Level 2 : Attack Category            |
                    | Level 3 : Specific Attack            |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | Stratified Train/Test Split          |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | StandardScaler                       |
                    | (Training Data Only)                 |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | Per-Level SMOTE                      |
                    +------------------+-------------------+
                                       |
            +--------------------------+--------------------------+
            |                          |                          |
            v                          v                          v
   +----------------+         +----------------+         +----------------+
   | Level 1        |         | Level 2        |         | Level 3        |
   | Binary XGBoost | ------> | Category XGB   | ------> | Specific XGB   |
   +----------------+         +----------------+         +----------------+
                                       |
                                       v
                    +--------------------------------------+
                    | Hierarchical Prediction Engine       |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | Model Evaluation                     |
                    |--------------------------------------|
                    | Accuracy                             |
                    | Precision                            |
                    | Recall                               |
                    | F1 Score                             |
                    | ROC-AUC                              |
                    | Cross Validation                     |
                    | Confusion Matrix                     |
                    +------------------+-------------------+
                                       |
                                       v
                    +--------------------------------------+
                    | SHAP Explainability                  |
                    | Pareto Feature Analysis              |
                    +--------------------------------------+
```

---

# Hierarchical Classification Pipeline

The intrusion detection framework follows a three-stage decision hierarchy.

## Level 1 – Binary Classification

The first stage determines whether incoming network traffic is:

- Benign
- Attack

Traffic identified as benign exits the pipeline immediately, while malicious traffic proceeds to the next classification stages.

---

## Level 2 – Attack Category Classification

Only traffic classified as malicious is processed further.

This stage predicts the broader attack category:

- DDoS
- DoS
- Spoofing
- Reconnaissance
- Web-Based
- Backdoor

---

## Level 3 – Specific Attack Classification

The final stage performs fine-grained attack identification by predicting the exact attack variant belonging to the previously detected malicious traffic.

This hierarchical structure reduces classification complexity while providing detailed security information.

---

# Methodology

## 1. Data Preprocessing

The raw CICIoT2023 traffic captures undergo several preprocessing steps before model training.

These include:

- Duplicate removal
- Missing value handling
- Feature normalization
- Hierarchical label construction
- Stratified train-test splitting
- Standard feature scaling

To prevent information leakage, the StandardScaler is fitted exclusively on the training partition.

---

## 2. Hierarchical Label Engineering

Three independent label sets are generated.

| Level | Classification Task |
|--------|---------------------|
| Level 1 | Binary Classification (Benign / Attack) |
| Level 2 | Attack Category Classification |
| Level 3 | Specific Attack Classification |

Each hierarchy level is trained independently while maintaining relationships between prediction stages.

---

## 3. Per-Level SMOTE

Network intrusion datasets often suffer from severe class imbalance.

Instead of performing oversampling once during preprocessing, this implementation applies SMOTE independently at every hierarchy level.

SMOTE is applied only to the training partition to prevent information leakage into the evaluation set.

---

## 4. Three-Tier XGBoost Architecture

Three independent XGBoost classifiers are trained.

| Model | Objective |
|--------|-----------|
| Binary XGBoost | Benign vs Attack |
| Category XGBoost | Attack Category |
| Specific XGBoost | Exact Attack Type |

GPU acceleration is used during training to improve computational efficiency.

---

## 5. Branched Inference

The prediction process follows a branched architecture.

```text
Incoming Network Flow
        │
        ▼
Level 1
Binary Detection
        │
 ┌──────┴──────┐
 │             │
 ▼             ▼
Benign      Attack
 Stop          │
               ▼
      ┌────────┴────────┐
      ▼                 ▼
 Level 2            Level 3
Category          Specific
Classifier        Classifier
```

Unlike sequential dependency models, the Level 2 and Level 3 classifiers operate independently once malicious traffic is detected, reducing propagation errors and improving scalability.

---

## 6. Explainable AI

Model explainability is integrated using SHAP (SHapley Additive exPlanations).

The implementation provides:

- Global Feature Importance
- SHAP Summary Plots
- Feature Contribution Analysis
- Directional Feature Impact
- Pareto Feature Ranking

These explanations enable analysts to understand why a particular network flow is classified as malicious.

---

# Experimental Results

The proposed hierarchical IDS was evaluated using a held-out 20% test partition.

| Classification Level | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
|----------------------|---------:|----------:|--------:|---------:|--------:|
| Binary Detection | **93.03%** | 91.51% | 94.01% | 92.47% | **0.9801** |
| Attack Category | **90.19%** | 68.86% | 70.69% | 69.31% | **0.9850** |
| Specific Attack | **90.45%** | 69.63% | 71.19% | 69.88% | **0.9872** |

The models were additionally evaluated using **5-Fold Stratified Cross Validation**, demonstrating stable generalization with minimal performance variance across all hierarchy levels.

---

# Technology Stack

| Category | Technology |
|-----------|------------|
| Programming Language | Python |
| Machine Learning | XGBoost |
| Dataset | CICIoT2023 |
| Explainable AI | SHAP |
| Data Processing | Pandas |
| Numerical Computing | NumPy |
| Feature Scaling | StandardScaler |
| Class Balancing | SMOTE |
| Model Evaluation | Scikit-learn |
| Visualization | Matplotlib |
| Statistical Visualization | Seaborn |
| Hardware | NVIDIA T4 GPU |

---

# Project Highlights

- Hierarchical three-stage intrusion detection framework
- Independent XGBoost models for each classification level
- Per-level SMOTE balancing to improve minority class detection
- Branched inference architecture for efficient prediction
- SHAP-based explainability for transparent model interpretation
- GPU-accelerated model training
- Low variance across cross-validation folds
- Modular and scalable architecture suitable for future IDS research

---

# Future Improvements

- Integration of all CICIoT2023 traffic files
- Real-time intrusion detection for streaming network traffic
- Hyperparameter optimization using Bayesian Optimization or Optuna
- Payload-aware feature engineering
- Cost-sensitive learning for minority attack classes
- Advanced oversampling techniques such as KMeans-SMOTE
- Distributed model training
- Deployment using FastAPI and Docker
- Interactive explainability dashboard

---

# Contact

**Chava Sathwik**

- GitHub: https://github.com/sathwikchava
- LinkedIn: https://www.linkedin.com/in/sathwik-chava-91096027b/
- Email: sathwikchava2005@gmail.com

For implementation-related questions, collaboration opportunities, or reproducibility discussions, feel free to reach out.

---

# License

This project is released under the **MIT License**.
