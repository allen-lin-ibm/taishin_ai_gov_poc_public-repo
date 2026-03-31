# 模型遷移

本目錄包含將 TensorFlow 模型從 H5 格式遷移至 SavedModel 格式的筆記本和資源，並部署至 watsonx.ai。

## 📋 概述

模型遷移工作流程提供完整的流程：
- 將 Keras H5 模型轉換為 TensorFlow SavedModel 格式
- 驗證轉換後的模型等效性
- 產生測試輸入資料
- 部署模型至 watsonx.ai 部署空間
- 使用批次預測測試已部署的模型

## 📓 筆記本

### H5 模型遷移完整流程
**檔案**：`h5_model_migration_watsonx_ai_complete_flow.ipynb`

完整的端到端流程：
1. 將 H5 模型轉換為 SavedModel 格式
2. 驗證模型等效性
3. 產生測試輸入資料
4. 部署至 watsonx.ai 部署空間
5. 測試已部署的模型

**模型資訊：**
- **原始格式**：Keras H5
- **目標格式**：SavedModel（TensorFlow 2.14）
- **模型名稱**：buy_model_exchange
- **總參數數量**：1,315,363
- **輸入**：32 個特徵（9 個業務 + 6 個時間序列 + 17 個風險/風格特徵）
- **輸出**：3 個分類（buy_19、order_6、exchange_9）

## 📊 資料檔案

### 輸入資料範例
- **`single_batch_savedmodel_scoring_input_data_example.json`** - 單一樣本評分格式
- **`2_batch_savedmodel_scoring_input_data_example.json`** - 批次評分格式（2 個樣本）

### 參考資料（台新提供資料/）
- **`init_weight.h5`** - 原始 Keras H5 模型檔案
- **`df_simulate.pkl`** - 用於測試的模擬資料

## 🚀 使用方式

### 先決條件

```bash
# 安裝必要套件
pip install tensorflow numpy pandas ibm-watsonx-ai

# 或使用專案的 pyproject.toml
uv sync
```

### 環境變數

設定以下環境變數：

```bash
export WX_API_KEY="your-watsonx-api-key"
export SPACE_ID="your-deployment-space-id"
export PROJECT_ID="your-project-id"  # 選用
```

### 執行筆記本

```bash
jupyter notebook h5_model_migration_watsonx_ai_complete_flow.ipynb
```

## 🔍 模型架構

### 輸入特徵（共 32 個）

#### BLOCK_MD_BRN_CONTACTS（9 個特徵）
- `input_x_o_MD_BRN_CONTACTS`：16 個連續特徵
- `input_x_c_1_MD_BRN_CONTACTS` 到 `input_x_c_8_MD_BRN_CONTACTS`：8 個類別特徵

#### 時間序列（3 個特徵）
- `input_x_t_MD_CASH_FLOW`：12×22 時間序列
- `input_x_t_MD_CUS_EXCH`：12×12 時間序列
- `input_x_t_MD_CUS_LOAN`：12×24 時間序列

#### BLOCK_MD_CUS_RISK（9 個特徵）
- `input_x_o_MD_CUS_RISK`：1 個連續特徵
- `input_x_c_1_MD_CUS_RISK` 到 `input_x_c_8_MD_CUS_RISK`：8 個類別特徵

#### BLOCK_MD_CUS_STYLE（8 個特徵）
- `input_x_o_MD_CUS_STYLE`：35 個連續特徵
- `input_x_c_1_MD_CUS_STYLE` 到 `input_x_c_7_MD_CUS_STYLE`：7 個類別特徵

#### 額外時間序列（3 個特徵）
- `input_x_t_MD_DEP_AUM`：12×12 時間序列
- `input_x_t_MD_NEW_INVST`：12×21 時間序列
- `input_x_t_MD_PDH_AUM`：12×30 時間序列

### 輸出分類

- **buy_19**：19 類別分類（產品購買預測）
- **order_6**：6 類別分類（訂單類型預測）
- **exchange_9**：9 類別分類（交易行為預測）

## 📝 工作流程步驟

### 1. 設定和配置
- 匯入必要的函式庫
- 載入環境變數
- 配置 watsonx.ai 憑證

### 2. 載入原始 H5 模型
- 載入 Keras H5 模型
- 檢查模型架構
- 驗證輸入/輸出形狀

### 3. 轉換為 SavedModel 格式
- 將 H5 轉換為 SavedModel
- 以 TensorFlow SavedModel 格式儲存
- 建立壓縮檔案以供部署

### 4. 驗證模型等效性
- 產生測試資料
- 比較 H5 和 SavedModel 的預測結果
- 確保數值等效性

### 5. 產生測試輸入資料
- 建立符合模型架構的樣本輸入資料
- 儲存為 JSON 格式以供評分測試
- 驗證資料格式

### 6. 本地測試模型
- 載入 SavedModel
- 使用測試資料執行預測
- 驗證輸出格式

### 7. 部署至 watsonx.ai
- 連接至 watsonx.ai
- 將模型儲存至部署空間
- 建立線上部署
- 等待部署就緒

### 8. 測試已部署的模型
- 向部署端點發送評分請求
- 比較本地與已部署的結果
- 驗證部署功能

## 🛠️ 客製化

### 使用您自己的 H5 模型

將模型載入部分替換為您自己的 H5 檔案：

```python
model = keras.models.load_model('path/to/your/model.h5')
```

### 修改輸入資料產生

調整筆記本中的輸入配置以符合您的模型輸入架構：

```python
input_config = {
    'your_input_name': {
        'shape': (batch_size, feature_dim),
        'dtype': 'float32'
    },
    # 根據需要新增更多輸入
}
```

### 變更部署配置

修改部署設定：

```python
deployment_details = {
    'name': 'your_deployment_name',
    'hardware_spec': {
        'name': 'S',  # 或 'M'、'L'、'XL'
        'num_nodes': 1
    }
}
```

## 📚 其他資源

- [TensorFlow SavedModel 指南](https://www.tensorflow.org/guide/saved_model)
- [Keras 模型序列化](https://keras.io/guides/serialization_and_saving/)
- [IBM watsonx.ai Python SDK](https://ibm.github.io/watsonx-ai-python-sdk/)
- [watsonx.ai 文件](https://www.ibm.com/docs/en/watsonx-as-a-service)

## ⚠️ 重要注意事項

1. **模型相容性**：確保您的 H5 模型與 TensorFlow 2.x 相容
2. **輸入格式**：模型預期 32 個獨立的輸入陣列，而非單一串接陣列
3. **輸出格式**：模型產生 3 個獨立的輸出陣列用於多任務分類
4. **資料前處理**：根據您的特定需求實作適當的資料前處理
5. **版本相容性**：使用 TensorFlow 2.14 或相容版本進行 SavedModel 轉換

## 🐛 疑難排解

**問題**：「模型轉換失敗」
- **解決方案**：檢查 TensorFlow 版本相容性和模型架構

**問題**：「H5 和 SavedModel 之間的預測不匹配」
- **解決方案**：驗證輸入資料前處理並確保數值精度設定

**問題**：「部署失敗」
- **解決方案**：檢查模型大小、格式和部署空間配額

**問題**：「輸入形狀不匹配」
- **解決方案**：驗證輸入資料格式是否符合模型預期的輸入架構

## ✅ 成功標準

成功執行筆記本後，您應該擁有：
- ✅ H5 模型已轉換為 SavedModel 格式
- ✅ 已驗證模型等效性（預測結果相符）
- ✅ 已產生測試輸入資料
- ✅ 模型已部署至 watsonx.ai
- ✅ 部署處於「就緒」狀態
- ✅ 已驗證已部署模型的預測
- ✅ 本地和已部署的結果相符

## 🎯 後續步驟

1. 實作生產級資料前處理
2. 建立自動化模型重新訓練流程
3. 監控生產環境中的模型效能
4. 實作模型版本控制策略
5. 建立自動化測試工作流程
6. 設定部署健康檢查
7. 實作回滾機制
8. 新增效能監控和日誌記錄