# ML-AWS — End-to-End Student Performance Prediction & AWS Deployment

An end-to-end machine learning project that predicts a student's **math score** from demographic and academic features, structured as a production-style pipeline (ingestion → transformation → training → serving) and deployed as a Flask web app on AWS.

## Overview

Rather than a single notebook, this project is organized the way a real ML service would be: modular pipeline components, a trained model artifact, a Flask front-end for inference, and a clear separation between experimentation (`notebook/`) and production code (`src/`).

**Problem:** predict a student's math score using features such as gender, race/ethnicity, parental level of education, lunch type, test preparation course completion, and reading/writing scores.

## Architecture

```
notebook/data/stud.csv
        │
        ▼
  Data Ingestion       (src/components/data_ingestion.py)
        │                 → reads raw data, splits into train/test
        ▼
  Data Transformation   (src/components/data_transformation.py)
        │                 → encodes categoricals, scales numeric features
        ▼
  Model Trainer         (src/components/model_trainer.py)
        │                 → trains & compares multiple regressors
        │                   (Linear Regression, KNN, Decision Tree, Random Forest,
        │                    AdaBoost, Gradient Boosting, SVR, CatBoost, XGBoost)
        │                 → selects the best model by R² score, saves artifact/model.pkl
        ▼
  Predict Pipeline      (src/pipeline/predict_pipeline.py)
        │                 → loads the saved model + preprocessor for inference
        ▼
  Flask App (app.py)    → serves predictions via a web form (templates/home.html)
```

## Features

- **Multi-model comparison** — trains 9 regression algorithms and automatically selects the best performer via `RandomizedSearchCV` + R² evaluation.
- **Reusable pipeline objects** — `CustomException` and a centralized `logging` module (mirroring production Python service conventions) wrap every pipeline stage.
- **Web UI for inference** — a simple Flask form (`/predictdata`) lets a user enter student attributes and get a predicted score in real time.
- **Cloud-ready** — designed to be containerized/deployed on AWS (Elastic Beanstalk / EC2).

## Project Structure

```
ML-AWS/
├── app.py                              # Flask web app (prediction UI)
├── requirements.txt
├── setup.py
├── notebook/
│   ├── EDA STUDENT PERFORMANCE.ipynb   # Exploratory data analysis
│   ├── MODEL TRAINING.ipynb            # Model comparison & selection
│   └── data/stud.csv                   # Source dataset
├── src/
│   ├── components/
│   │   ├── data_ingestion.py
│   │   ├── data_transformation.py
│   │   └── model_trainer.py
│   ├── pipeline/
│   │   ├── train_pipeline.py
│   │   └── predict_pipeline.py
│   ├── exception.py                    # Custom exception handling
│   ├── logger.py                       # Centralized logging
│   └── utils.py                        # save_object / evaluate_models helpers
└── templates/
    ├── index.html
    └── home.html
```

## Tech Stack

| Layer | Technology |
|---|---|
| Modeling | scikit-learn, CatBoost, XGBoost |
| Data processing | pandas, numpy |
| Web framework | Flask |
| Deployment target | AWS |

## Getting Started

### Installation

```bash
git clone https://github.com/MohammedTabarakAhmed/ML-AWS.git
cd ML-AWS
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### Train the model

```bash
python -m src.pipeline.train_pipeline
```

This runs ingestion → transformation → training, and saves the best model to `artifact/model.pkl`.

### Run the web app

```bash
python app.py
```

Visit `http://localhost:5000`, click "Predict", fill in the student attributes, and get a predicted math score back instantly.

## Deployment

This project is structured for deployment on **AWS** (e.g. Elastic Beanstalk or EC2 + Flask/Gunicorn). The Flask app in `app.py` is deployment-ready as-is; add a `Procfile`/WSGI entry point per your chosen AWS service.

## License

Available for educational and personal portfolio use.
