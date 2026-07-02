# US Visa Approval Prediction - MLOps Production-Ready Project

This repository contains a production-ready MLOps pipeline for predicting US visa approval outcomes. The project combines machine learning training, data validation, model evaluation, cloud storage, containerization, and a web-based inference interface in a modular and extensible structure.

## Overview

The system predicts the target column `case_status` using applicant and company-related features such as education level, job experience, wage, company age, and employment region. The project is organized as a full ML lifecycle pipeline with:

- Data ingestion from MongoDB
- Schema validation and data drift monitoring
- Feature engineering and preprocessing
- Model training with hyperparameter search
- Model evaluation and promotion logic
- FastAPI-based prediction service
- Docker support and AWS deployment workflow

## Project Structure

```text
.
├── app.py                  # FastAPI app for UI and prediction endpoints
├── demo.py                 # Demo script
├── Dockerfile              # Container definition
├── requirements.txt        # Python dependencies
├── setup.py                # Package installation config
├── config/                 # YAML config for schema and model tuning
├── flowcharts/             # Pipeline flow diagrams
├── notebook/               # EDA and experimentation notebooks
├── static/                 # CSS and static frontend assets
├── templates/              # HTML templates for the web app
└── us_visa/                # Main package
    ├── components/         # Data ingestion, validation, transformation, training, evaluation, pusher
    ├── configuration/     # MongoDB and AWS clients/configuration
    ├── constants/         # Project constants and file names
    ├── data_access/       # MongoDB data access layer
    ├── entity/            # Config and artifact dataclasses
    ├── exception/         # Custom exception handling
    ├── logger/            # Logging utilities
    ├── pipline/           # Training and prediction pipeline orchestration
    └── utils/             # Shared helper utilities
```

## Tech Stack

- Python 3.8+
- FastAPI + Uvicorn
- Pandas, NumPy, Scikit-learn
- Imbalanced-learn (SMOTEENN)
- XGBoost, CatBoost
- PyYAML, Dill, Pymongo
- Evidently for data drift monitoring
- Boto3 for AWS S3 integration
- Docker

## Data Source

The project expects a MongoDB collection named `visa_data` in the database `US_VISA`. The data is exported from MongoDB into local artifact files during training.

## Environment Setup

### 1) Create and activate a virtual environment

```bash
conda create -n visa python=3.8 -y
conda activate visa
```

### 2) Install dependencies

```bash
pip install -r requirements.txt
pip install -e .
```

### 3) Set environment variables

```bash
export MONGODB_URL="mongodb+srv://<username>:<password>@<cluster-url>"
export AWS_ACCESS_KEY_ID="<your-aws-access-key>"
export AWS_SECRET_ACCESS_KEY="<your-aws-secret-key>"
export AWS_DEFAULT_REGION="us-east-1"
```

## Running the Project Locally

### Start the web application

```bash
python app.py
```

Then open:

```text
http://localhost:8080
```

### Available endpoints

- `GET /` -> Renders the prediction UI
- `POST /` -> Submits form data and returns the prediction result
- `GET /train` -> Triggers the training pipeline

## Training Pipeline Workflow

The training workflow runs through the following stages:

1. Data Ingestion
   - Reads data from MongoDB
   - Stores it in the artifact folder
   - Splits it into train and test sets

2. Data Validation
   - Checks schema consistency
   - Applies drift detection using Evidently

3. Data Transformation
   - Builds preprocessing pipelines
   - Applies encoding, scaling, and resampling

4. Model Trainer
   - Trains multiple candidate models using configurable search strategies

5. Model Evaluation
   - Compares the new model against the existing production model
   - Approves the model if it meets the acceptance threshold

6. Model Pusher
   - Uploads the approved model to AWS S3 for deployment

## Docker Usage

Build and run the application with Docker:

```bash
docker build -t usvisa-app .
docker run -p 8080:8080 usvisa-app
```

## AWS and CI/CD Notes

The repository is prepared for AWS-based deployment using:

- Amazon ECR for image storage
- Amazon EC2 for serving the container
- GitHub Actions for automated build and deployment

Typical GitHub secrets used for deployment include:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_DEFAULT_REGION`
- `ECR_REPO`

## Git Commands

```bash
git add .
git commit -m "Update project"
git push origin main
```

## Notes

- The project writes generated artifacts under the `artifact/` directory during training.
- The app expects the trained model and preprocessing object to be available for prediction.
- Make sure MongoDB and AWS credentials are configured before running training or inference.


