# Network-Intrusion-Detection
This project develops a machine learning-based Network Intrusion Detection System (NIDS) for classifying network connections into Normal, DoS, Probe, R2L, and U2R. It uses the KDD Cup 1999 dataset, preprocesses 41 network traffic features, handles class imbalance using balanced sample weights, and compares Random Forest and XGBoost.
Absolutely. Here is a **clean, professional, GitHub-ready `README.md`** based on what you actually built and tested.

# Network Intrusion Detection System

A machine learning-based Network Intrusion Detection System (NIDS) that analyzes network traffic and classifies it into normal activity or different attack categories.

The project uses the KDD Cup 1999 dataset, compares Random Forest and XGBoost models, handles class imbalance, evaluates attack-focused performance, and deploys the selected model through a Flask API and Docker.

---

## Problem Statement

Network systems generate large volumes of traffic, making manual detection of malicious activity difficult.

This project uses machine learning to automatically analyze network connection features and identify whether the traffic is:

- Normal
- DoS (Denial of Service)
- Probe
- R2L (Remote to Local)
- U2R (User to Root)

The project demonstrates an end-to-end ML workflow from data preprocessing and model training to API deployment and containerization.

---

## Features

- KDD Cup 1999 network intrusion dataset
- Automatic attack-category mapping
- Duplicate removal
- 41 network traffic features
- Categorical feature encoding
- Numerical feature preprocessing
- Class imbalance handling using balanced sample weights
- Random Forest vs XGBoost comparison
- Attack-focused evaluation
- Classification reports
- Confusion matrix
- Feature importance analysis
- Flask REST API
- Interactive web interface
- Automated testing with Pytest
- Dockerized deployment

---

## Attack Categories

| Category | Description |
|---|---|
| Normal | Legitimate network traffic |
| DoS | Denial of Service attacks |
| Probe | Network scanning and reconnaissance |
| R2L | Remote-to-Local attacks |
| U2R | User-to-Root attacks |

---

## Dataset

The project uses the **KDD Cup 1999** dataset through `scikit-learn`.

Each record represents a network connection and contains 41 traffic-related features.

### Dataset Processing

The original dataset contained:

```text
494,021 records
````

After duplicate removal:

```text
145,585 records
```

A total of:

```text
348,436 duplicate records
```

were removed.

### Class Distribution

| Class  | Proportion |
| ------ | ---------: |
| Normal |     60.33% |
| DoS    |     37.48% |
| Probe  |      1.46% |
| R2L    |      0.69% |
| U2R    |      0.04% |

The dataset is highly imbalanced, particularly for U2R attacks.

---

## Machine Learning Pipeline

```text
KDD Cup 1999
      ↓
Data Cleaning
      ↓
Attack Label Mapping
      ↓
Duplicate Removal
      ↓
Feature / Target Split
      ↓
Stratified Train-Test Split
      ↓
Preprocessing
      ↓
Class Imbalance Handling
      ↓
Random Forest + XGBoost
      ↓
Model Evaluation
      ↓
Best Model Selection
      ↓
Flask API
      ↓
Docker Container
```

---

## Feature Processing

The dataset contains 41 features.

Three features are categorical:

* `protocol_type`
* `service`
* `flag`

Categorical features are processed using:

```text
OneHotEncoder
```

Numerical features are processed using:

```text
StandardScaler
```

A scikit-learn `ColumnTransformer` combines both preprocessing steps into a single pipeline.

This ensures that the same preprocessing is applied during both training and prediction.

---

## Models

Two machine learning models were evaluated:

1. Random Forest
2. XGBoost

Class imbalance was addressed using balanced sample weights during training.

---

## Model Results

| Model         | Accuracy | Attack Recall |
| ------------- | -------: | ------------: |
| Random Forest |   0.9993 |        0.9987 |
| XGBoost       |   0.9993 |    **0.9997** |

XGBoost was selected because it achieved the higher attack recall.

### XGBoost Classification Report

| Class  | Precision | Recall | F1-Score |
| ------ | --------: | -----: | -------: |
| DoS    |      1.00 |   1.00 |     1.00 |
| Probe  |      0.99 |   1.00 |     0.99 |
| R2L    |      0.97 |   0.97 |     0.97 |
| U2R    |      0.69 |   0.90 |     0.78 |
| Normal |      1.00 |   1.00 |     1.00 |

**Macro F1-score:** approximately 0.95

---

## Evaluation Metric

Accuracy alone can be misleading for intrusion detection because the dataset contains significant class imbalance.

Therefore, the project uses **attack recall** as the primary model-selection metric.

Attack recall measures:

> Of all actual attacks, how many were correctly identified as an attack rather than normal traffic?

XGBoost achieved:

```text
Attack Recall = 99.97%
```

This metric does **not** mean that 99.97% of attacks were assigned to their exact attack category. It measures whether an attack was detected as an attack.

---

## Flask API

The trained model is served using Flask.

### Health Check

```http
GET /health
```

Example response:

```json
{
  "ok": true
}
```

### Prediction

```http
POST /predict
```

The endpoint accepts the 41 KDD99 traffic features and returns the predicted attack category and class probabilities.

Example response:

```json
{
  "prediction": "R2L",
  "probabilities": {
    "DoS": 0.0,
    "Probe": 0.0,
    "R2L": 0.9988,
    "U2R": 0.0004,
    "normal": 0.0008
  }
}
```

---

## Running Locally

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd Network-intrusion-detection
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

### 3. Activate the environment

Windows:

```powershell
.venv\Scripts\Activate.ps1
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

### 5. Run the application

```bash
python -m src.serve
```

Open:

```text
http://127.0.0.1:5000
```

---

## Training the Model

To retrain the models:

```bash
python -m src.train
```

The training script:

1. Loads the KDD99 dataset
2. Cleans the raw data
3. Maps attacks into five categories
4. Removes duplicate records
5. Splits the data using stratification
6. Builds the preprocessing pipeline
7. Handles class imbalance
8. Trains Random Forest and XGBoost
9. Evaluates both models
10. Selects the model using attack recall
11. Saves the trained model

---

## Testing

The project includes automated tests for:

* Data processing
* Evaluation metrics
* Feature preprocessing
* Flask API

Run:

```bash
python -m pytest
```

Current test result:

```text
16 passed
```

---

## Docker

The application is containerized using Docker.

### Build the Docker image

```bash
docker build -t network-intrusion-detector .
```

### Run the container

```bash
docker run --rm -p 5000:5000 network-intrusion-detector
```

Open:

```text
http://localhost:5000
---

## Project Structure

```text
Network-intrusion-detection/
│
├── src/
│   ├── __init__.py
│   ├── data.py
│   ├── features.py
│   ├── train.py
│   ├── evaluate.py
│   └── serve.py
│
├── tests/
│   ├── test_data.py
│   ├── test_evaluate.py
│   ├── test_features.py
│   └── test_serve.py
│
├── data/
│   └── examples.json
│
├── models/
│   ├── model.joblib
│   ├── label_encoder.joblib
│   └── model_name.txt
│
├── templates/
│   └── index.html
│
├── reports/
│   └── feature_importance.png
│
├── Dockerfile
├── .dockerignore
├── .gitignore
├── requirements.txt
├── pytest.ini
└── README.md
```

---

## Limitations

* KDD Cup 1999 is an older benchmark dataset and does not represent modern network traffic.
* U2R attacks are extremely rare in the dataset.
* The evaluated test split contains only 10 U2R samples.
* The reported results come from a random train-test split from the same dataset distribution.
* High benchmark performance does not guarantee real-world intrusion detection performance.
* The model has not been evaluated against modern zero-day attack techniques.

---

## Future Improvements

* Evaluate on newer intrusion detection datasets
* Add SHAP-based model explainability
* Add batch prediction endpoint
* Add prediction history and monitoring
* Add GitHub Actions CI/CD
* Use a production WSGI server for deployment
* Evaluate against unseen/novel attack distributions

---

## Technologies

* Python
* Pandas
* NumPy
* Scikit-learn
* XGBoost
* Matplotlib
* Flask
* Pytest
* Docker

---

## Author

**Madhura Kale**

Computer Engineering

[LinkedIn](https://linkedin.com/in/madhura-kale-979833291)

[GitHub](https://github.com/Madhura0807)

````
