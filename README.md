# Spam Detection MLOps Project

This project demonstrates an end-to-end Machine Learning Operations (MLOps) pipeline for a Spam Detection model. It uses **DVC (Data Version Control)** to manage data, model artifacts, and pipeline stages, along with **DVCLive** for experiment tracking, and **AWS S3** as a remote storage backend.

## Project Structure

```bash
.
├── .dvc/                  # DVC configuration and cache
├── data/                  # Data directories (raw, interim, processed) - Tracked by DVC
├── dvclive/               # DVCLive tracked experiments and plots
├── experiments/           # Jupyter notebooks and experimental code
├── logs/                  # Application logs
├── models/                # Trained model artifacts (.pkl) - Tracked by DVC
├── reports/               # Evaluation metrics
├── src/                   # Source code for pipeline stages
│   ├── data_ingestion.py
│   ├── data_preprocessing.py
│   ├── feature_engineering.py
│   ├── model_building.py
│   └── model_evaluation.py
├── dvc.yaml               # DVC pipeline definition
├── params.yaml            # Hyperparameters and configurations
└── requirements.txt       # Python dependencies
```

## Setup & Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/whysamay/Spam_detection_MLOps.git
   cd Spam_detection_MLOps
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv .venv
   # Windows:
   .venv\Scripts\activate
   # Linux/Mac:
   source .venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Pipeline Stages (`src/`)

The ML pipeline is defined in `dvc.yaml` and consists of the following sequential stages:
1. **Data Ingestion** (`src/data_ingestion.py`): Loads data and splits it into train/test sets based on `params.yaml`.
2. **Data Preprocessing** (`src/data_preprocessing.py`): Cleans and preprocesses the raw data.
3. **Feature Engineering** (`src/feature_engineering.py`): Extracts features (e.g., TF-IDF) using parameters like `max_features`.
4. **Model Building** (`src/model_building.py`): Trains the machine learning model.
5. **Model Evaluation** (`src/model_evaluation.py`): Evaluates the model, calculating Accuracy, Precision, Recall, and AUC.

## Running Experiments

This project uses `dvclive` to track metrics automatically. Each time you run the pipeline, DVC treats it as a distinct "experiment."

1. **Modify Parameters:** Open `params.yaml` and adjust parameters (e.g., `test_size`, `n_estimators`, `max_features`).
2. **Run an Experiment:**
   ```bash
   dvc exp run
   ```
3. **Compare Experiments:**
   ```bash
   dvc exp show
   ```
4. **Apply Best Experiment:**
   Once you find an experiment with the best metrics, apply it to your workspace:
   ```bash
   dvc exp apply <experiment-name>
   ```

## Data Storage & S3 Remote Configuration

To keep the Git repository lightweight, large files (like `.csv` data files and `.pkl` models) are tracked by DVC and pushed to an **AWS S3 bucket**.

**Important DVC Philosophy for this Repo:**
- Large binary files (Data, Models) -> Tracked by **DVC** and pushed to **S3** (`dvc push`)
- Small text files (Code, `params.yaml`, `dvclive/metrics.json`) -> Tracked by **Git** and pushed to **GitHub** (`git push`)

### Pushing to S3
Ensure you have your AWS credentials configured, then run:
```bash
dvc push
```

*Optional: If you want to push your run cache to S3 so others don't have to re-compute pipeline stages, run:*
```bash
dvc push --run-cache
```

### Retrieving Data from S3
If you have just cloned this repository and want to download the data and models from S3:
```bash
dvc pull
```

## Contributing
1. Create a new branch (`git checkout -b feature-branch`)
2. Make your code/parameter changes and run experiments (`dvc exp run`)
3. Commit your code (`git commit -am "Updated features"`)
4. Push data to S3 (`dvc push`)
5. Push code to GitHub (`git push origin feature-branch`)
