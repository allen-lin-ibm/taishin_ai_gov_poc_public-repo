# Orchestration Pipeline for watsonx.ai Model Deployment

This directory contains three independent Jupyter notebooks that demonstrate a complete end-to-end workflow for training and deploying TensorFlow models to watsonx.ai.

## 📋 Overview

The orchestration pipeline consists of three main notebooks:

1. **`1_data_generation.ipynb`** - Data generation and upload to watsonx.ai project assets
2. **`2_model_training_and_deployment.ipynb`** - Model training, conversion, and storage to deployment space
3. **`3_deployment_testing.ipynb`** - Batch prediction testing using deployment endpoint

## 🔧 Prerequisites

### Required Environment Variables

Set the following environment variables before running the notebooks:

```bash
# For notebook 1
export PROJECT_ID="your-watsonx-project-id"
export WX_API_KEY="your-watsonx-api-key"

# For notebook 2
export MODEL_NAME="your-model-name"  # e.g., "buy_model_exchange"
export PROJECT_ID="your-watsonx-project-id"
export SPACE_ID="your-watsonx-deployment-space-id"
export WX_API_KEY="your-watsonx-api-key"

# For notebook 3
export DEPLOYMENT_NAME="buy_model_exchange_deployment"
export SPACE_ID="your-watsonx-deployment-space-id"
export WX_API_KEY="your-watsonx-api-key"
```

### Required Python Packages

```bash
pip install numpy pandas tensorflow ibm-watsonx-ai requests
```

Or use the project's `pyproject.toml`:

```bash
uv sync
```

## 📓 Notebook 1: Data Generation

**File**: `1_data_generation.ipynb`

### Purpose
Generates synthetic training data matching the model's input schema and uploads it to watsonx.ai project assets.

### Workflow
1. **Setup and Configuration** - Import libraries and load environment variables
2. **Define Input Configuration** - Configure 32 input features (embeddings, time series, continuous)
3. **Generate Synthetic Data** - Create 100 samples of synthetic data
4. **Save as CSV** - Convert to DataFrame and save locally
5. **Connect to watsonx.ai** - Establish connection using credentials
6. **Upload to Project Assets** - Upload CSV file to project
7. **Verify Upload** - Confirm successful upload

### Output
- CSV file: `model_input_data_YYYYMMDD_HHMMSS.csv`
- Data asset in watsonx.ai project with Asset ID

### Key Features
- Generates data for 32 model inputs:
  - 9 business contact features (BLOCK_MD_BRN_CONTACTS)
  - 3 time series inputs (MD_CASH_FLOW, MD_CUS_EXCH, MD_CUS_LOAN)
  - 8 customer risk features (BLOCK_MD_CUS_RISK)
  - 7 customer style features (BLOCK_MD_CUS_STYLE)
  - 3 additional time series (MD_DEP_AUM, MD_NEW_INVST, MD_PDH_AUM)
- Handles both categorical (int) and continuous (float) features
- Flattens multi-dimensional arrays for CSV storage

## 📓 Notebook 2: Model Training, Storage, and Deployment

**File**: `2_model_training_and_deployment.ipynb`

### Purpose
Loads training data, trains/fine-tunes a TensorFlow model, converts to SavedModel format, stores it to watsonx.ai deployment space, and automatically deploys it.

### Workflow
1. **Setup and Configuration** - Import libraries and load environment variables
2. **Connect to watsonx.ai** - Establish connection
3. **Find Latest Training Data** - Search project assets for latest CSV file
4. **Download and Load Data** - Load training data into DataFrame
5. **Load Pre-trained Model** - Load H5 model or create demonstration model
6. **Compile and Train** - Train/fine-tune the model
7. **Convert to SavedModel** - Convert to TensorFlow SavedModel format
8. **Store to Deployment Space** - Store model as asset
9. **Deploy Model** - Create online deployment and wait for it to be ready

### Output
- SavedModel directory: `{MODEL_NAME}/`
- Compressed archive: `{MODEL_NAME}.zip`
- Model stored in watsonx.ai deployment space with Model ID
- Online deployment created with Deployment ID

### Key Features
- **Automatic Data Discovery**: Automatically finds and uses the latest training data matching pattern `model_input_data_*` or `training_data_*`
- **Flexible Model Loading**: Supports loading pre-trained H5 models or creating demonstration models
- **Automatic Deployment**: Creates online deployment and waits for it to be ready
- **Deployment Update**: Automatically replaces existing deployment if one exists with the same model name
- **Comprehensive Logging**: Detailed progress tracking and summary information

### Important Note
This notebook now **automatically deploys** the model after storing it. The deployment name will be `{MODEL_NAME}_deployment`. If a deployment with this name already exists, it will be deleted and recreated.

## 📓 Notebook 3: Deployment Testing

**File**: `3_deployment_testing.ipynb`

### Purpose
Tests a deployed model by automatically finding its deployment and performing batch predictions.

### Workflow
1. **Setup and Configuration** - Import libraries
2. **Load Environment Variables** - Load model name, space ID, and API key
3. **Connect to watsonx.ai** - Establish connection and set default space
4. **Find Deployment** - Automatically search for deployment by model name
5. **Define Input Configuration** - Configure 32 input features
6. **Generate Test Samples** - Create 5 test samples
7. **Prepare Scoring Payload** - Format data for batch prediction
8. **Send Batch Prediction Request** - Score using watsonx.ai SDK
9. **Analyze Results** - Display and analyze predictions
10. **Save Results** - Save predictions to JSON file

### Output
- Prediction results displayed in notebook
- JSON file: `prediction_results_YYYYMMDD_HHMMSS.json`

### Key Features
- **Automatic Deployment Discovery**: Searches for deployment by model name
- **Batch Predictions**: Tests multiple samples in a single request
- **watsonx.ai SDK Integration**: Uses official SDK for scoring
- **Result Persistence**: Automatically saves results with timestamp
- **Error Handling**: Comprehensive error handling and helpful messages

### Important Note
This notebook requires the model to be deployed (which notebook 2 does automatically). It will:
1. Search for a deployment containing the `DEPLOYMENT_NAME` in its name
2. Use the found deployment for scoring
3. Display available deployments if the target is not found

## 🚀 Usage

### Step 1: Generate Training Data

```bash
# Open and run notebook 1
jupyter notebook 1_data_generation.ipynb
```

This will:
- Generate 100 synthetic samples
- Save as CSV file
- Upload to watsonx.ai project assets
- Display Asset ID for reference

### Step 2: Train, Store, and Deploy Model

```bash
# Open and run notebook 2
jupyter notebook 2_model_training_and_deployment.ipynb
```

This will:
- Find and load the latest training data
- Load/create the model
- Train the model (demonstration workflow)
- Convert to SavedModel format
- Store to watsonx.ai deployment space
- Create online deployment automatically
- Wait for deployment to be ready
- Display Model ID and Deployment ID

### Step 3: Test Deployed Model

```bash
# Set the deployment name and space ID
export DEPLOYMENT_NAME="buy_model_exchange_deployment"
export SPACE_ID="your-space-id"

# Open and run notebook 3
jupyter notebook 3_deployment_testing.ipynb
```

This will:
- Load deployment endpoint from environment variable
- Generate 5 test samples
- Send batch prediction request
- Display and save results

## 📊 Model Architecture

The model (`buy_model_exchange`) has:
- **32 inputs**: Mix of embeddings, time series, and continuous features
- **3 outputs**: 
  - `buy_19`: 19-class classification
  - `order_6`: 6-class classification
  - `exchange_9`: 9-class classification
- **Total parameters**: ~1.3M (demonstration model is simplified)

## 🔍 Data Schema

### Input Features (32 total)

#### BLOCK_MD_BRN_CONTACTS (9 features)
- `input_x_o_MD_BRN_CONTACTS`: 16 continuous features
- `input_x_c_1_MD_BRN_CONTACTS` to `input_x_c_8_MD_BRN_CONTACTS`: 8 categorical features

#### Time Series (3 features)
- `input_x_t_MD_CASH_FLOW`: 12×22 time series
- `input_x_t_MD_CUS_EXCH`: 12×12 time series
- `input_x_t_MD_CUS_LOAN`: 12×24 time series

#### BLOCK_MD_CUS_RISK (9 features)
- `input_x_o_MD_CUS_RISK`: 1 continuous feature
- `input_x_c_1_MD_CUS_RISK` to `input_x_c_8_MD_CUS_RISK`: 8 categorical features

#### BLOCK_MD_CUS_STYLE (8 features)
- `input_x_o_MD_CUS_STYLE`: 35 continuous features
- `input_x_c_1_MD_CUS_STYLE` to `input_x_c_7_MD_CUS_STYLE`: 7 categorical features

#### Additional Time Series (3 features)
- `input_x_t_MD_DEP_AUM`: 12×12 time series
- `input_x_t_MD_NEW_INVST`: 12×21 time series
- `input_x_t_MD_PDH_AUM`: 12×30 time series

## 🛠️ Customization

### Modify Number of Samples

In `1_data_generation.ipynb`, change:
```python
num_samples = 100  # Change to desired number
```

### Load Your Own H5 Model

In `2_model_training_and_deployment.ipynb`, replace the demonstration model section with:
```python
# Load from project assets using Code Snippets
h5_model_content = client.data_assets.get_content('YOUR_H5_MODEL_ASSET_ID')
with open('temp_model.h5', 'wb') as f:
    f.write(h5_model_content)
model = keras.models.load_model('temp_model.h5')
```

### Implement Actual Training

In `2_model_training_and_deployment.ipynb`, implement proper data preprocessing and training:
```python
# Prepare X_train (list of 32 input arrays) and y_train (list of 3 output arrays)
X_train = [...]  # 32 arrays
y_train = [...]  # 3 arrays

# Train model
history = model.fit(
    X_train,
    y_train,
    epochs=10,
    batch_size=32,
    validation_split=0.2,
    verbose=1
)
```

### Change Number of Test Samples

In `3_deployment_testing.ipynb`, change:
```python
num_test_samples = 5  # Change to desired number
```

## 📝 Notes

### Important Considerations

1. **Data Preprocessing**: The notebooks demonstrate the workflow. In production, implement proper data preprocessing based on your specific data format.

2. **Model Loading**: The demonstration creates a simplified model. For production, load your actual pre-trained H5 model from project assets.

3. **Training**: Actual model training requires proper data preprocessing to convert CSV data into the correct input format (32 separate arrays).

4. **Environment Variables**: Always use environment variables for credentials. Never hardcode API keys.

5. **Automatic Deployment**: Notebook 2 now automatically deploys the model. If a deployment with the same name exists, it will be replaced.

6. **Deployment Naming**: The deployment name follows the pattern `{MODEL_NAME}_deployment`. Ensure this name is unique in your space.

### Troubleshooting

**Issue**: "Required environment variables are missing"
- **Solution**: Set PROJECT_ID, SPACE_ID, and WX_API_KEY environment variables

**Issue**: "No training data found in project assets"
- **Solution**: Run notebook 1 first to generate and upload training data

**Issue**: "Model architecture mismatch"
- **Solution**: Ensure your H5 model has the same input/output structure as defined in the configuration

**Issue**: "Deployment not found" (Notebook 3)
- **Solution**:
  1. Ensure notebook 2 completed successfully and created the deployment
  2. Verify DEPLOYMENT_NAME matches the deployment name (should be `{MODEL_NAME}_deployment`)
  3. Check that SPACE_ID is correct
  4. Review the list of available deployments shown in the error message

**Issue**: "401 Unauthorized" (Notebook 3)
- **Solution**: Check that your WX_API_KEY is valid and has access to the deployment space

**Issue**: "Deployment failed" (Notebook 2)
- **Solution**:
  1. Check the error message in the deployment status
  2. Verify the model format is correct (SavedModel)
  3. Ensure the runtime specification matches your TensorFlow version
  4. Check deployment space quotas and limits

## 🔗 Related Files

- `../model_migration/src/generate_input_data_example.py` - Reference for input data generation
- `../model_migration/model/init_weight_savedmodel_20260310_181624/` - Example SavedModel structure
- `../model_migration/台新提供資料/init_weight.h5` - Original H5 model

## 📚 Additional Resources

- [watsonx.ai Documentation](https://www.ibm.com/docs/en/watsonx-as-a-service)
- [TensorFlow SavedModel Guide](https://www.tensorflow.org/guide/saved_model)
- [IBM watsonx.ai Python SDK](https://ibm.github.io/watsonx-ai-python-sdk/)
- [watsonx.ai REST API](https://cloud.ibm.com/apidocs/machine-learning)

## ✅ Success Criteria

After running all three notebooks successfully, you should have:
- ✅ CSV training data uploaded to watsonx.ai project assets
- ✅ TensorFlow model stored in watsonx.ai deployment space
- ✅ Model automatically deployed with online endpoint
- ✅ Deployment in "ready" state
- ✅ Verified predictions from deployed model
- ✅ Prediction results saved to JSON file

## 🎯 Next Steps

1. Implement proper data preprocessing for your specific use case
2. Load your actual pre-trained H5 model
3. Perform actual model training/fine-tuning with real data
4. Monitor model performance in production
5. Set up automated retraining pipelines
6. Implement model versioning and A/B testing
7. Create automated testing and validation workflows
8. Add deployment health checks and monitoring
9. Implement rollback mechanisms for failed deployments
10. Set up CI/CD pipelines for automated model updates