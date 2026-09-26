# E-Commerce Risk & Demand Intelligence System
## Technical Blueprint (Production-Grade)

---

## 1. Real-World Relevance

### Core ML Problems
- **Cancellation Risk**: Binary classification (Delivered vs. Cancelled).
- **Quantity Prediction**: Regression (transaction-level quantity, **not** forecasting).

### Framework Mapping
| Component | Technology | Role |
| :--- | :--- | :--- |
| Prediction engine | Traditional ML (Scikit-learn) | Core model training & inference |
| Serving interface | FastAPI | REST API for prediction requests |
| Infrastructure | PostgreSQL | Logging predictions & telemetry |
| Frontend | HTML/CSS/JS | User interface |

### No LLM Required
- No Llama, Mistral, LoRA, RAG, embeddings, vector DBs, or Transformers.
- Fine-tuning is **not** needed for the defined problems.

---

## 2. Core Theory (Minimum to Understand)

### Supervised Learning
- **Input**: `X` (order/customer features).
- **Output**: `y` (known outcome).
- The model learns patterns from historical examples.

### Classification vs. Regression
- **Classification**: Output is a probability (e.g., `P(cancelled) = 0.73`). A threshold converts it to a class (0/1). Notebook studies thresholds (0.20–0.60).
- **Regression**: Output is numeric (e.g., predicted quantity = `3.7`). Must check for fractional or negative predictions.

### Train/Test Split
- 80/20 split with stratification for classification.
- Test set is the **final exam**; use cross-validation on training **only** for tuning.

### Data Leakage (Critical)
- **Leakage**: Model receives information unavailable at prediction time.
- **Example**: `order_status` cannot be used to predict cancellation.
- **Quantity leakage**: `gross_amount = quantity × unit_price`; excluding these features is mandatory.
- **Rule**: Always verify: *Was this known when the order was created?*

### One-Hot Encoding
- Categorical variables (`city`, `product`, `category`, `payment_method`) are converted to binary indicator columns.
- Notebook uses `OneHotEncoder(handle_unknown="ignore")`.

### Pipeline (Essential)
- Combine preprocessing (imputation, encoding) and model into a single `sklearn.pipeline.Pipeline`.
- Save the entire pipeline to ensure identical preprocessing in training and inference.

---

## 3. Exact Math & Statistics

### Cancellation Metrics
- **Accuracy**: `(TP+TN) / (TP+TN+FP+FN)` — not relied upon alone (imbalanced data).
- **Precision**: `TP / (TP+FP)` – of flagged orders, how many actually cancelled?
- **Recall**: `TP / (TP+FN)` – of all cancellations, how many were caught?
- **F1**: `2 * (Precision * Recall) / (Precision + Recall)` – harmonic mean.
- **ROC-AUC**: Measures ranking ability; higher AUC → better.

### Threshold Engineering
- Model outputs probability; threshold decides action.
- Lower threshold → higher recall, lower precision, more flags.
- Higher threshold → higher precision, lower recall, fewer flags.
- Business cost trade-off should guide final threshold selection (see Section 9).

### Quantity Prediction Metrics
- **MAE**: `(1/n) * Σ|y - ŷ|` – average error in units; business-friendly.
- **RMSE**: `√((1/n) * Σ(y - ŷ)²)` – penalizes large errors.
- **R²**: `1 - (SS_res / SS_tot)` – variance explained relative to mean baseline.

### Residuals
- `Residual = Actual - Predicted`.
- Inspect residuals to detect systematic errors (e.g., cheap products good, expensive poor).

### Cross-Validation
- Notebook uses `StratifiedKFold(n_splits=5)`.
- Compute mean and standard deviation of fold scores.
- Answers: *Is the model consistently good?*

### Probability Concepts
- **Conditional probability**: `P(Cancelled | Features)` – estimate given available info.
- **Class probability**: e.g., `0.81` – do not treat as absolute truth; calibration is separate.

### Linear Algebra (Minimal)
- **Feature vector**: `[quantity, unit_price, discount, city_encoded, ...]`.
- **Dataset matrix**: rows = orders, columns = features.
- No eigenvectors, SVD, PCA, or matrix calculus required.

---

## 4. Core Algorithms

### Logistic Regression (Classification)
- `p = 1 / (1 + exp(-z))`, `z = b + w₁x₁ + w₂x₂ + …`
- Converts weighted feature sum into probability.

### Random Forest Classifier
- Ensemble of 300 decision trees (`n_estimators=300`, `class_weight="balanced"`).
- Uses bagging and majority voting.
- Understand: decision trees, splits, impurity, bagging, feature importance.

### Ridge Regression (Regression baseline)
- Linear regression with L2 penalty: `Loss = PredictionError + λ * Σ w_i²`.
- Prevents overfitting by penalizing large coefficients.

### Random Forest Regressor
- Ensemble of regression trees; averages predictions.
- Similar to classifier but for numeric targets.

### Preprocessing Algorithms
- `SimpleImputer` (numeric: median; categorical: most frequent).
- `OneHotEncoder` (binary columns).
- `ColumnTransformer` – applies different transformations to different columns.

---

## 5. Dataset & Feature Considerations

**Dataset**: 25,000 rows, 31 columns (e.g., `customer_tenure_days`, `previous_orders`, `prior_cancellations`, `session_duration_minutes`, `pages_viewed`, `cart_items`, `product`, `category`, `quantity`, `unit_price`, `discount_percent`, `shipping_fee`, `delivery_distance_km`, `estimated_delivery_days`, `payment_method`, `gross_amount`, `discount_amount`, `total_amount`, `order_status`, `actual_delivery_days`, `customer_rating`).

### Legitimate Prediction-Time Features (for cancellation)
- Customer tenure, previous orders, prior cancellations, `email_verified`, `traffic_source`, `device_type`, session behavior, product, category, quantity, price, discount, city, payment method.

### Forbidden (Leakage)
- `order_status`, `actual_delivery_days`, `customer_rating` (outcomes after order).

### Quantity Leakage
- `gross_amount = quantity × unit_price` → must exclude `gross_amount`, `discount_amount`, `total_amount` from features.

---

## 6. Demand Forecasting Feasibility

- **Current**: Transaction-level quantity prediction (supervised regression).
- **True demand forecasting** requires time-ordered split (e.g., 2024 train, 2025 test) and repeated observations per product/city over time.
- Do **not** call this forecasting unless data supports it.
- No ARIMA/LSTM/Transformers unless feasibility study confirms.

---

## 7. Essential Statistics & Mathematics

### Required Statistics
- Mean, median, variance, standard deviation, percentiles, distribution, correlation, probability, conditional probability, sampling, train/test split, cross-validation, confusion matrix, precision, recall, F1, ROC-AUC, MAE, RMSE, R².

### Not Required Initially
- Confidence intervals, hypothesis testing, calibration, bootstrapping, Bayesian inference, Markov chains, advanced calculus, measure theory, advanced stochastic processes.

### Required Mathematics
- Arithmetic, percentages, basic algebra, functions, exponentials, vectors, matrices, basic logarithms, sigmoid function, weighted sums, squared error, absolute error, L2 regularization.

### Not Required
- Multivariable calculus, Jacobians, Hessians, eigenvalue decomposition, Fourier analysis, advanced optimization theory.

---

## 8. Recommended Learning Path

### From Krish Naik’s Udemy Courses (current curricula)
- **Course**: *Complete Data Science, Machine Learning, DL, NLP Bootcamp* or *Data Analytics, Data Science, ML, DL & NLP – All in 1*.

### Priority Topics
- Data preprocessing
- Feature engineering
- Linear Regression
- Logistic Regression
- Decision Tree
- Random Forest
- Cross-validation
- Regularization (L1/L2)
- Model evaluation
- Imbalanced data

### Ignore (not required)
- CNN, RNN, Transformers, NLP, Generative AI, Computer Vision.

### Study Order for This Project
1. Data preprocessing
2. EDA
3. Probability basics
4. Descriptive statistics
5. Train/test split
6. Logistic Regression
7. Confusion Matrix
8. Precision/Recall/F1
9. ROC-AUC
10. Decision Trees
11. Random Forest
12. Linear Regression
13. Ridge Regression
14. MAE/RMSE/R²
15. Cross-validation
16. L1/L2 regularization
17. Feature engineering
18. Data leakage
19. Error analysis
20. Model deployment

---

## 9. Production Improvement: Cost-Driven Threshold Selection

### Recommended Addition
Define business costs:
- `Cost(False Positive)` = unnecessary verification cost.
- `Cost(False Negative)` = cost of missed cancellation.
- **Expected Cost** = `FP × Cost_FP + FN × Cost_FN`.
- Choose the threshold that minimizes expected cost, subject to business constraints.

---

## 10. End-to-End Architecture

```text
E‑Commerce DB
      │
      ▼
Data Processing → Feature Engineering
      │
      ├───────────────┐
      ▼               ▼
Cancellation Model   Quantity Model
(Logistic / RF)     (Ridge / RF)
      │               │
      └───────┬───────┘
              ▼
      Saved ML Pipelines
              │
              ▼
          FastAPI
      ┌──────┴──────┐
      ▼             ▼
 /cancellation    /quantity
      │             │
      └──────┬──────┘
             ▼
        PostgreSQL
             │
             ▼
         Frontend
No LLM, no fine-tuning, no RAG.

11. Definition of “Production-Grade” for This Project
Correct prediction problem

No leakage

Strong baseline

Measured model improvement

Correct preprocessing

Saved pipeline

Validated API

Prediction logging

Automated tests

Deployment

Monitoring

12. Final “Must Know” Checklist
Data
What one row represents.

Why the target is valid.

Which features are available at prediction time.

Which columns leak information.

Why an outlier is not automatically an error.

Classification
Logistic Regression and Random Forest behaviour.

Precision vs recall, F1, ROC-AUC, confusion matrix.

Threshold selection, false positive vs false negative.

Regression
Ridge and Random Forest Regressor behaviour.

MAE, RMSE, R², residuals.

Why negative quantity predictions are problematic.

Validation
Train/test separation, cross-validation, tuning against test set.

Leakage and fake performance.

Engineering
Preprocessing inside pipeline, saving full pipeline.

API input validation, prediction logging, model/version metadata.

Business
Decision each prediction supports.

Costs of false positive and false negative.

Operational/financial definition of success.

13. Study Boundary
text
                       REQUIRED
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
   Data Science      Machine Learning     Engineering
        │                 │                 │
    EDA/Stats       Logistic Regression    sklearn Pipeline
    Probability     Random Forest          FastAPI
    Features        Ridge                  PostgreSQL
    Leakage         Metrics                Testing
    Validation      Cross-validation       Deployment
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
               E-Commerce ML System