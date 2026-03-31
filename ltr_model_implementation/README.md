# LTR 模型實作

本目錄包含使用 LightGBM 和 XGBoost 實作學習排序（Learning-to-Rank, LTR）模型的筆記本，並部署至 watsonx.ai。

## 📋 概述

LTR 模型實作提供完整的工作流程：
- 訓練 LightGBM LambdaMART 模型
- 將 LightGBM 模型轉換為 XGBoost 格式
- 驗證模型轉換準確性
- 部署模型至 watsonx.ai 部署空間
- 測試已部署的模型

## 📓 筆記本

### 1. LightGBM LTR 完整流程
**檔案**：`lightgbm_ltr_watsonx_ai_complete_flow.ipynb`

完整的端到端流程：
1. 訓練 LightGBM LambdaMART 模型
2. 將 LightGBM 模型轉換為 XGBoost 格式
3. 驗證模型轉換準確性
4. 部署至 watsonx.ai 部署空間
5. 測試已部署的模型

**模型資訊：**
- **原始演算法**：LightGBM LambdaMART
- **目標演算法**：XGBoost LambdaMART
- **目標函數**：rank:ndcg
- **部署類型**：xgboost_2.0
- **Python 版本**：3.11

### 2. XGBoost LTR 訓練流程
**檔案**：`train_xgboost_ltr_watsonx_ai_complete_flow.ipynb`

直接使用 XGBoost LambdaMART 訓練和部署的工作流程。

## 📊 資料檔案

### 輸入資料
- **`LTR_model_input.csv`** - LTR 模型的訓練資料
- **`ltr_scoring_input_data_example.json`** - 評分輸入格式範例

### 參考資料（台新提供資料/）
- **`演算法資訊.txt`** - 演算法配置資訊
  - 演算法：LightGBM
  - 目標函數：lambdarank
  - 評估指標：ndcg
  - 提升類型：gbdt
- **`LTR_data_structure.xlsx`** - 資料結構文件

## 🚀 使用方式

### 先決條件

```bash
# 安裝必要套件
pip install lightgbm xgboost numpy pandas ibm-watsonx-ai

# 或使用專案的 pyproject.toml
uv sync
```

### 環境變數

設定以下環境變數：

```bash
export WX_API_KEY="your-watsonx-api-key"
export SPACE_ID="your-deployment-space-id"
export PROJECT_ID="your-project-id"  # 選用，用於專案相關操作
```

### 執行筆記本

1. **訓練並部署 LightGBM 模型（含 XGBoost 轉換）**：
   ```bash
   jupyter notebook lightgbm_ltr_watsonx_ai_complete_flow.ipynb
   ```

2. **直接訓練並部署 XGBoost 模型**：
   ```bash
   jupyter notebook train_xgboost_ltr_watsonx_ai_complete_flow.ipynb
   ```

## 🔍 主要功能

### LightGBM 到 XGBoost 轉換
- 自動將模型從 LightGBM 轉換為 XGBoost 格式
- 驗證轉換準確性
- 確保與 watsonx.ai 部署的相容性

### 模型訓練
- 用於學習排序任務的 LambdaMART 演算法
- NDCG（標準化折損累積增益）指標最佳化
- 支援 LightGBM 和 XGBoost 實作

### 部署
- 自動部署至 watsonx.ai
- 模型版本控制和管理
- 建立線上評分端點

## 📝 演算法配置

根據提供的演算法資訊（`演算法資訊.txt`）：

```python
lambdamart_params = {
    'objective': 'lambdarank',
    'metric': 'ndcg',
    'boosting_type': 'gbdt'
}
```

## 🛠️ 客製化

### 修改訓練參數

在筆記本中調整 LambdaMART 參數：

```python
params = {
    'objective': 'rank:ndcg',
    'learning_rate': 0.1,
    'max_depth': 6,
    'n_estimators': 100,
    # 根據需要新增更多參數
}
```

### 使用自己的訓練資料

將 `LTR_model_input.csv` 替換為您自己的資料，確保遵循 `LTR_data_structure.xlsx` 中記錄的相同結構。

## 📚 其他資源

- [LightGBM 文件](https://lightgbm.readthedocs.io/)
- [XGBoost 文件](https://xgboost.readthedocs.io/)
- [IBM watsonx.ai Python SDK](https://ibm.github.io/watsonx-ai-python-sdk/)
- [學習排序指南](https://en.wikipedia.org/wiki/Learning_to_rank)

## ✅ 成功標準

成功執行筆記本後，您應該擁有：
- ✅ 已訓練的 LTR 模型（LightGBM 或 XGBoost）
- ✅ 已轉換為 XGBoost 格式的模型（如果使用 LightGBM）
- ✅ 已部署至 watsonx.ai 的模型
- ✅ 已驗證的模型預測
- ✅ 可使用的線上評分端點

## 🎯 後續步驟

1. 針對您的特定使用案例微調模型超參數
2. 實作交叉驗證以進行穩健的模型評估
3. 建立自動化重新訓練流程
4. 監控生產環境中的模型效能
5. 實作 A/B 測試以進行模型比較