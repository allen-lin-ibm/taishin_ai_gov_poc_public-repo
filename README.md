# Taishin Bank AI Governance PoC

A proof-of-concept project demonstrating AI model governance capabilities for Taishin Bank, featuring TensorFlow model migration and Learning-to-Rank (LTR) model development with deployment on watsonx.ai.

## Overview

This repository contains three main components:

1. **Model Migration**: Converting legacy TensorFlow H5 models to SavedModel format
2. **LTR Model Implementation**: Developing and deploying Learning-to-Rank models using LightGBM/XGBoost
3. **Orchestration Pipeline**: End-to-end automated workflow for data generation, model training, and deployment

All components include complete end-to-end workflows from model development to deployment on IBM watsonx.ai platform.

## Project Structure

```
.
├── model_migration/
│   ├── h5_model_migration_watsonx_ai_complete_flow.ipynb
│   ├── init_weight_savedmodel_scoring_input_data_example.json
│   └── 台新提供資料/
│       ├── df_simulate.pkl
│       └── init_weight.h5
│
├── ltr_model_implementation/
│   ├── lightgbm_ltr_watsonx_ai_complete_flow.ipynb
│   ├── train_xgboost_ltr_watsonx_ai_complete_flow.ipynb
│   ├── LTR_model_input.csv
│   ├── ltr_scoring_input_data_example.json
│   └── 台新提供資料/
│       ├── 演算法資訊.txt
│       └── LTR_data_structure.xlsx
│
├── orchestration_pipeline/
│   ├── 1_data_generation.ipynb
│   ├── 2_model_training_and_deployment.ipynb
│   ├── 3_deployment_testing.ipynb
│   └── README.md
│
├── LICENSE
└── README.md
```

## Features

### 1. Model Migration (H5 → SavedModel)

Complete pipeline for migrating TensorFlow Keras H5 models to SavedModel format:

- **Model**: `buy_model_exchange`
- **Parameters**: 1,315,363 total parameters
- **Inputs**: 32 features (9 business + 6 time series + 17 risk/style)
- **Outputs**: 3 predictions (buy_19, order_6, exchange_9)
- **Target Format**: TensorFlow 2.14 SavedModel

**Workflow includes:**
- H5 to SavedModel conversion
- Model equivalence verification
- Test data generation
- watsonx.ai deployment
- Deployed model testing and validation

### 2. LTR Model Implementation

Learning-to-Rank model development using LightGBM LambdaMART with XGBoost conversion:

- **Algorithm**: LightGBM LambdaMART → XGBoost LambdaMART
- **Objective**: `rank:ndcg` (Normalized Discounted Cumulative Gain)
- **Deployment**: XGBoost 2.0 on watsonx.ai
- **Python Version**: 3.11

**Workflow includes:**
- LightGBM LambdaMART training
- Model conversion to XGBoost format
- Conversion accuracy verification
- watsonx.ai deployment
- Online scoring and testing

### 3. Orchestration Pipeline

Automated end-to-end workflow demonstrating complete model lifecycle management:

- **Pipeline**: Data Generation → Model Training → Deployment → Testing
- **Model**: TensorFlow multi-output classification model
- **Automation**: Automatic deployment and testing
- **Integration**: Full watsonx.ai SDK integration

**Workflow includes:**
- Synthetic training data generation and upload
- Automatic data discovery from project assets
- Model training and SavedModel conversion
- Automated deployment to watsonx.ai
- Batch prediction testing and validation
- Result persistence and analysis

## Getting Started

### Prerequisites

- Python 3.11+
- TensorFlow 2.14+
- LightGBM
- XGBoost 2.0+
- IBM watsonx.ai Python SDK
- Jupyter Notebook

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd taishin_ai_gov_poc_public-repo

# Install required packages
pip install tensorflow==2.14 lightgbm xgboost ibm-watsonx-ai jupyter
```

### Usage

#### Model Migration

1. Open [`model_migration/h5_model_migration_watsonx_ai_complete_flow.ipynb`](model_migration/h5_model_migration_watsonx_ai_complete_flow.ipynb)
2. Configure watsonx.ai credentials
3. Run all cells to execute the complete migration pipeline

#### LTR Model Development

1. Open [`ltr_model_implementation/lightgbm_ltr_watsonx_ai_complete_flow.ipynb`](ltr_model_implementation/lightgbm_ltr_watsonx_ai_complete_flow.ipynb) or [`ltr_model_implementation/train_xgboost_ltr_watsonx_ai_complete_flow.ipynb`](ltr_model_implementation/train_xgboost_ltr_watsonx_ai_complete_flow.ipynb)
2. Configure watsonx.ai credentials
3. Run all cells to train, convert, and deploy the LTR model

#### Orchestration Pipeline

**Complete 3-step workflow:**

1. **Data Generation**: Open [`orchestration_pipeline/1_data_generation.ipynb`](orchestration_pipeline/1_data_generation.ipynb)
   - Generates synthetic training data
   - Uploads to watsonx.ai project assets

2. **Model Training & Deployment**: Open [`orchestration_pipeline/2_model_training_and_deployment.ipynb`](orchestration_pipeline/2_model_training_and_deployment.ipynb)
   - Automatically finds latest training data
   - Trains and converts model to SavedModel
   - Deploys to watsonx.ai automatically

3. **Deployment Testing**: Open [`orchestration_pipeline/3_deployment_testing.ipynb`](orchestration_pipeline/3_deployment_testing.ipynb)
   - Tests deployed model with batch predictions
   - Saves and analyzes results

See [`orchestration_pipeline/README.md`](orchestration_pipeline/README.md) for detailed documentation.

## watsonx.ai Integration

Both workflows support deployment to IBM watsonx.ai platform with:

- **CP4D (on-premises)** deployment
- **IBM Cloud** deployment

Refer to the [IBM watsonx.ai Python SDK Documentation](https://ibm.github.io/watsonx-ai-python-sdk/) for detailed API reference.

## Data Files

### Model Migration
- `init_weight.h5`: Original Keras H5 model
- `df_simulate.pkl`: Simulation data for testing
- `init_weight_savedmodel_scoring_input_data_example.json`: Sample scoring input

### LTR Implementation
- `LTR_model_input.csv`: Training data for LTR model
- `ltr_scoring_input_data_example.json`: Sample scoring input
- `演算法資訊.txt`: Algorithm configuration details
- `LTR_data_structure.xlsx`: Data structure documentation

### Orchestration Pipeline
- Generated training data: `model_input_data_YYYYMMDD_HHMMSS.csv` (auto-generated)
- Prediction results: `prediction_results_YYYYMMDD_HHMMSS.json` (auto-generated)
- Model archive: `{MODEL_NAME}.zip` (auto-generated)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact

For questions, support, or collaboration inquiries, please reach out:

### Allen Lin

*AI Engineer, Client Engineering, IBM Taiwan*

✉️ Email: [Allen.Lin@ibm.com](mailto:Allen.Lin@ibm.com)

<br>

---

<br>

<div align="center">

**Copyright © 2026 Allen Lin** <br>
All rights reserved.

<br>

<sub><i>This README was written with help from <a href="https://www.ibm.com/products/bob">IBM Bob</a>.</i></sub>

</div>
