# Taishin Bank AI Governance PoC

A proof-of-concept project demonstrating AI model governance capabilities for Taishin Bank, featuring TensorFlow model migration and Learning-to-Rank (LTR) model development with deployment on watsonx.ai.

## Overview

This repository contains six main components:

1. **Model Migration**: Converting legacy TensorFlow H5 models to SavedModel format
2. **LTR Model Implementation**: Developing and deploying Learning-to-Rank models using LightGBM/XGBoost
3. **Batch Deployment Scoring**: Inline batch scoring with watsonx.ai batch deployments
4. **Monthly 10M Batch Prediction**: Large-scale monthly batch prediction workflow (10M+ records)
5. **Orchestration Pipeline**: End-to-end automated workflow for data generation, model training, and deployment
6. **Parameter Set Showcase**: Demonstration of watsonx.ai Parameter Sets usage and management

All components include complete end-to-end workflows from model development to deployment on IBM watsonx.ai platform.

## Project Structure

```
.
├── model_migration/
│   ├── h5_model_migration_watsonx_ai_complete_flow.ipynb
│   ├── single_batch_savedmodel_scoring_input_data_example.json
│   ├── 2_batch_savedmodel_scoring_input_data_example.json
│   ├── README.md
│   └── 台新提供資料/
│       ├── df_simulate.pkl
│       └── init_weight.h5
│
├── ltr_model_implementation/
│   ├── lightgbm_ltr_watsonx_ai_complete_flow.ipynb
│   ├── train_xgboost_ltr_watsonx_ai_complete_flow.ipynb
│   ├── LTR_model_input.csv
│   ├── ltr_scoring_input_data_example.json
│   ├── README.md
│   └── 台新提供資料/
│       ├── 演算法資訊.txt
│       └── LTR_data_structure.xlsx
│
├── batch_deployment_scoring/
│   ├── batch_deployment_scoring_with_inline_input.ipynb
│   ├── 2_batch_savedmodel_scoring_input_data_example.json
│   └── job_log/
│
├── monthly_10m_batch_prediction/
│   ├── monthly_prediction_1_convert_csv_to_json.ipynb
│   ├── monthly_prediction_2_zip_all_json_files.ipynb
│   ├── monthly_prediction_3_upload_and_create_job.ipynb
│   └── data/
│       ├── raw_csv/
│       ├── json_files/
│       ├── zipped/
│       └── batch_scoring_output/
│
├── orchestration_pipeline/
│   ├── 1_data_generation.ipynb
│   ├── 2_model_training_and_deployment.ipynb
│   ├── 3_deployment_testing.ipynb
│   └── README.md
│
├── parameter_set_showcase/
│   ├── parameter_set_showcase.ipynb
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

📖 **[View detailed documentation](model_migration/README.md)**

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

📖 **[View detailed documentation](ltr_model_implementation/README.md)**

### 3. Batch Deployment Scoring

Demonstration of inline batch scoring with watsonx.ai batch deployments:

- **Method**: Inline input data (embedded in job payload)
- **Deployment Type**: Batch deployment on watsonx.ai
- **Model**: TensorFlow SavedModel with 32 inputs
- **Features**: Job monitoring, result retrieval, and persistence

**Workflow includes:**
- Connecting to watsonx.ai and setting deployment space
- Loading scoring data from deployment space assets
- Creating batch scoring jobs with inline input
- Monitoring job execution status in real-time
- Retrieving and saving prediction results

📖 **Documentation**: See notebook for detailed inline batch scoring workflow

### 4. Monthly 10M Batch Prediction

Large-scale batch prediction workflow for processing millions of records monthly:

- **Scale**: Designed for 10M+ records per month
- **Approach**: 3-step workflow with CSV to JSON conversion
- **Processing**: Memory-efficient chunked processing
- **Deployment**: Batch deployment on watsonx.ai
- **Automation**: Automated file compression and upload

**3-Step Workflow:**
1. **CSV to JSON Conversion**: Convert large CSV files to individual JSON records
   - Memory-efficient chunked processing (1000 records/batch)
   - Configurable processing range for partial runs
   - Automatic data download from watsonx.ai assets
2. **ZIP Compression**: Compress all JSON files into a single archive
   - Efficient batch compression
   - Progress tracking
   - Size optimization
3. **Upload & Job Creation**: Upload to watsonx.ai and create batch job
   - Automatic upload to deployment space
   - Batch job creation and monitoring
   - Result retrieval and storage

📖 **Documentation**: See individual notebooks for each step

### 5. Orchestration Pipeline

Automated end-to-end workflow demonstrating complete model lifecycle management on **watsonx.ai platform**:

- **Pipeline**: Data Generation → Model Training → Deployment → Testing
- **Model**: TensorFlow multi-output classification model
- **Automation**: Automatic deployment and testing
- **Integration**: Full watsonx.ai SDK integration
- **Execution**: Designed to run on watsonx.ai platform (not local environment)

**Workflow includes:**
- Synthetic training data generation and upload
- Automatic data discovery from project assets
- Model training and SavedModel conversion
- Automated deployment to watsonx.ai
- Batch prediction testing and validation
- Result persistence and analysis

📖 **[View detailed documentation](orchestration_pipeline/README.md)**

### 6. Parameter Set Showcase

Demonstration of IBM watsonx.ai Parameter Sets functionality:

- **Purpose**: Centralized parameter management for ML workflows
- **Features**: Parameter storage, retrieval, and version control
- **Use Cases**: Configuration management, experiment tracking, pipeline parameters

**Workflow includes:**
- Connecting to watsonx.ai (CP4D and IBM Cloud)
- Listing all parameter sets in a project/space
- Retrieving parameter set details by ID
- Accessing parameter sets by name
- Viewing parameter values and configurations

📖 **[View detailed documentation](parameter_set_showcase/README.md)**

## Getting Started

### Prerequisites

- Python 3.11+
- TensorFlow 2.14+
- LightGBM
- XGBoost 2.0+
- IBM watsonx.ai Python SDK
- Jupyter Notebook (for local notebooks) or watsonx.ai platform access (for orchestration pipeline)

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd taishin_ai_gov_poc_public-repo

# Install required packages (for local notebooks)
pip install tensorflow==2.14 lightgbm xgboost ibm-watsonx-ai jupyter numpy pandas
```

**Note**: The orchestration pipeline notebooks are designed to run on the watsonx.ai platform and do not require local installation.

### Usage

#### 1️⃣ Model Migration

1. Open [`model_migration/h5_model_migration_watsonx_ai_complete_flow.ipynb`](model_migration/h5_model_migration_watsonx_ai_complete_flow.ipynb)
2. Configure watsonx.ai credentials
3. Run all cells to execute the complete migration pipeline

📖 **[Detailed instructions](model_migration/README.md)**

#### 2️⃣ LTR Model Development

1. Open [`ltr_model_implementation/lightgbm_ltr_watsonx_ai_complete_flow.ipynb`](ltr_model_implementation/lightgbm_ltr_watsonx_ai_complete_flow.ipynb) or [`ltr_model_implementation/train_xgboost_ltr_watsonx_ai_complete_flow.ipynb`](ltr_model_implementation/train_xgboost_ltr_watsonx_ai_complete_flow.ipynb)
2. Configure watsonx.ai credentials
3. Run all cells to train, convert, and deploy the LTR model

📖 **[Detailed instructions](ltr_model_implementation/README.md)**

#### 3️⃣ Orchestration Pipeline

**Important**: These notebooks must be run on the **watsonx.ai platform**, not in a local environment.

**Complete 3-step workflow:**

1. **Data Generation**: Open [`orchestration_pipeline/1_data_generation.ipynb`](orchestration_pipeline/1_data_generation.ipynb) on watsonx.ai
   - Generates synthetic training data
   - Uploads to watsonx.ai project assets

2. **Model Training & Deployment**: Open [`orchestration_pipeline/2_model_training_and_deployment.ipynb`](orchestration_pipeline/2_model_training_and_deployment.ipynb) on watsonx.ai
   - Automatically finds latest training data
   - Trains and converts model to SavedModel
   - Deploys to watsonx.ai automatically

3. **Deployment Testing**: Open [`orchestration_pipeline/3_deployment_testing.ipynb`](orchestration_pipeline/3_deployment_testing.ipynb) on watsonx.ai
   - Tests deployed model with batch predictions
   - Saves and analyzes results

📖 **[Detailed instructions](orchestration_pipeline/README.md)**

#### 4️⃣ Parameter Set Management

1. Open [`parameter_set_showcase/parameter_set_showcase.ipynb`](parameter_set_showcase/parameter_set_showcase.ipynb)
2. Configure watsonx.ai credentials (CP4D or IBM Cloud)
3. Set default project or deployment space
4. Run cells to explore parameter set operations:
   - List all parameter sets
   - Retrieve by ID or name
   - View parameter configurations

📖 **[Detailed instructions](parameter_set_showcase/README.md)**

## watsonx.ai Integration

All workflows support deployment to IBM watsonx.ai platform with:

- **CP4D (on-premises)** deployment
- **IBM Cloud** deployment

Refer to the [IBM watsonx.ai Python SDK Documentation](https://ibm.github.io/watsonx-ai-python-sdk/) for detailed API reference.

## Data Files

### Model Migration
- `init_weight.h5`: Original Keras H5 model
- `df_simulate.pkl`: Simulation data for testing
- `single_batch_savedmodel_scoring_input_data_example.json`: Single sample scoring input
- `2_batch_savedmodel_scoring_input_data_example.json`: Batch scoring input (2 samples)

### LTR Implementation
- `LTR_model_input.csv`: Training data for LTR model
- `ltr_scoring_input_data_example.json`: Sample scoring input
- `演算法資訊.txt`: Algorithm configuration details
- `LTR_data_structure.xlsx`: Data structure documentation

### Orchestration Pipeline
- Generated training data: `model_input_data_YYYYMMDD_HHMMSS.csv` (auto-generated)
- Prediction results: `prediction_results_YYYYMMDD_HHMMSS.json` (auto-generated)
- Model archive: `{MODEL_NAME}.zip` (auto-generated)

## Documentation

Each component has its own detailed README:

- 📖 [Model Migration Documentation](model_migration/README.md)
- 📖 [LTR Model Implementation Documentation](ltr_model_implementation/README.md)
- 📖 [Batch Deployment Scoring](batch_deployment_scoring/batch_deployment_scoring_with_inline_input.ipynb) - See notebook for inline batch scoring workflow
- 📖 [Monthly 10M Batch Prediction](monthly_10m_batch_prediction/) - See individual notebooks for 3-step workflow
- 📖 [Orchestration Pipeline Documentation](orchestration_pipeline/README.md)
- 📖 [Parameter Set Showcase Documentation](parameter_set_showcase/README.md)

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

<sub><i>This README was written with help from <a href="https://bob.ibm.com/">IBM Bob</a>.</i></sub>

</div>
