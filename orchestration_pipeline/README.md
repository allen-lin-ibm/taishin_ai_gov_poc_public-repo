# watsonx.ai 模型部署編排流程

本目錄包含三個獨立的 Jupyter 筆記本，展示訓練和部署 TensorFlow 模型至 watsonx.ai 的完整端到端工作流程。

## 📋 概述

編排流程包含三個主要筆記本：

1. **`1_data_generation.ipynb`** - 資料產生並上傳至 watsonx.ai 專案資產
2. **`2_model_training_and_deployment.ipynb`** - 模型訓練、轉換和儲存至部署空間
3. **`3_deployment_testing.ipynb`** - 使用部署端點進行批次預測測試

## 🔧 先決條件

### 必要的環境變數

執行筆記本前設定以下環境變數：

```bash
# 用於筆記本 1
export PROJECT_ID="your-watsonx-project-id"
export WX_API_KEY="your-watsonx-api-key"

# 用於筆記本 2
export MODEL_NAME="your-model-name"  # 例如："buy_model_exchange"
export PROJECT_ID="your-watsonx-project-id"
export SPACE_ID="your-watsonx-deployment-space-id"
export WX_API_KEY="your-watsonx-api-key"

# 用於筆記本 3
export DEPLOYMENT_NAME="buy_model_exchange_deployment"
export SPACE_ID="your-watsonx-deployment-space-id"
export WX_API_KEY="your-watsonx-api-key"
```

### Python 環境

這些 notebook 設計為在 **watsonx.ai 平台**上執行，平台已預先安裝所需的套件：
- numpy
- pandas
- tensorflow
- ibm-watsonx-ai
- requests

無需額外安裝套件。

## 📓 筆記本 1：資料產生

**檔案**：`1_data_generation.ipynb`

### 目的
產生符合模型輸入架構的合成訓練資料，並上傳至 watsonx.ai 專案資產。

### 工作流程
1. **設定和配置** - 匯入函式庫並載入環境變數
2. **定義輸入配置** - 配置 32 個輸入特徵（嵌入、時間序列、連續）
3. **產生合成資料** - 建立 100 個合成資料樣本
4. **儲存為 CSV** - 轉換為 DataFrame 並在本地儲存
5. **連接至 watsonx.ai** - 使用憑證建立連接
6. **上傳至專案資產** - 將 CSV 檔案上傳至專案
7. **驗證上傳** - 確認上傳成功

### 輸出
- CSV 檔案：`model_input_data_YYYYMMDD_HHMMSS.csv`
- watsonx.ai 專案中的資料資產及 Asset ID

### 主要功能
- 為 32 個模型輸入產生資料：
  - 9 個業務聯絡特徵（BLOCK_MD_BRN_CONTACTS）
  - 3 個時間序列輸入（MD_CASH_FLOW、MD_CUS_EXCH、MD_CUS_LOAN）
  - 8 個客戶風險特徵（BLOCK_MD_CUS_RISK）
  - 7 個客戶風格特徵（BLOCK_MD_CUS_STYLE）
  - 3 個額外時間序列（MD_DEP_AUM、MD_NEW_INVST、MD_PDH_AUM）
- 處理類別（int）和連續（float）特徵
- 將多維陣列展平以供 CSV 儲存

## 📓 筆記本 2：模型訓練、儲存和部署

**檔案**：`2_model_training_and_deployment.ipynb`

### 目的
載入訓練資料、訓練/微調 TensorFlow 模型、轉換為 SavedModel 格式、儲存至 watsonx.ai 部署空間，並自動部署。

### 工作流程
1. **設定和配置** - 匯入函式庫並載入環境變數
2. **連接至 watsonx.ai** - 建立連接
3. **尋找最新訓練資料** - 在專案資產中搜尋最新的 CSV 檔案
4. **下載並載入資料** - 將訓練資料載入 DataFrame
5. **載入預訓練模型** - 載入 H5 模型或建立示範模型
6. **編譯和訓練** - 訓練/微調模型
7. **轉換為 SavedModel** - 轉換為 TensorFlow SavedModel 格式
8. **儲存至部署空間** - 將模型儲存為資產
9. **部署模型** - 建立線上部署並等待就緒

### 輸出
- SavedModel 目錄：`{MODEL_NAME}/`
- 壓縮檔案：`{MODEL_NAME}.zip`
- 儲存在 watsonx.ai 部署空間中的模型及 Model ID
- 建立的線上部署及 Deployment ID

### 主要功能
- **自動資料探索**：自動尋找並使用符合模式 `model_input_data_*` 或 `training_data_*` 的最新訓練資料
- **彈性模型載入**：支援載入預訓練的 H5 模型或建立示範模型
- **自動部署**：建立線上部署並等待就緒
- **部署更新**：如果存在相同模型名稱的部署，會自動替換
- **完整日誌記錄**：詳細的進度追蹤和摘要資訊

### 重要注意事項
本筆記本現在會在儲存模型後**自動部署**。部署名稱將為 `{MODEL_NAME}_deployment`。如果已存在同名部署，將會被刪除並重新建立。

## 📓 筆記本 3：部署測試

**檔案**：`3_deployment_testing.ipynb`

### 目的
透過自動尋找部署並執行批次預測來測試已部署的模型。

### 工作流程
1. **設定和配置** - 匯入函式庫
2. **載入環境變數** - 載入模型名稱、空間 ID 和 API 金鑰
3. **連接至 watsonx.ai** - 建立連接並設定預設空間
4. **尋找部署** - 依模型名稱自動搜尋部署
5. **定義輸入配置** - 配置 32 個輸入特徵
6. **產生測試樣本** - 建立 5 個測試樣本
7. **準備評分負載** - 格式化資料以進行批次預測
8. **發送批次預測請求** - 使用 watsonx.ai SDK 進行評分
9. **分析結果** - 顯示和分析預測
10. **儲存結果** - 將預測儲存至 JSON 檔案

### 輸出
- 在筆記本中顯示的預測結果
- JSON 檔案：`prediction_results_YYYYMMDD_HHMMSS.json`

### 主要功能
- **自動部署探索**：依模型名稱搜尋部署
- **批次預測**：在單一請求中測試多個樣本
- **watsonx.ai SDK 整合**：使用官方 SDK 進行評分
- **結果持久化**：自動儲存帶有時間戳記的結果
- **錯誤處理**：完整的錯誤處理和有用的訊息

### 重要注意事項
本筆記本需要模型已部署（筆記本 2 會自動執行此操作）。它將：
1. 搜尋名稱中包含 `DEPLOYMENT_NAME` 的部署
2. 使用找到的部署進行評分
3. 如果找不到目標部署，則顯示可用的部署

## 🚀 使用方式

**重要**：這些 notebook 設計為在 **watsonx.ai 平台**上執行，而非本地環境。

### 步驟 1：在 watsonx.ai 上開啟專案

1. 登入 watsonx.ai 平台
2. 開啟或建立一個專案
3. 將這三個 notebook 上傳至專案

### 步驟 2：設定環境變數

在 watsonx.ai 專案中設定環境變數：

1. 進入專案設定
2. 在環境變數區域新增：
   - `PROJECT_ID`：您的專案 ID
   - `SPACE_ID`：您的部署空間 ID
   - `WX_API_KEY`：您的 API 金鑰
   - `MODEL_NAME`：模型名稱（例如："buy_model_exchange"）
   - `DEPLOYMENT_NAME`：部署名稱（例如："buy_model_exchange_deployment"）

### 步驟 3：執行 Notebook 1 - 產生訓練資料

在 watsonx.ai 平台上開啟並執行 `1_data_generation.ipynb`

這將：
- 產生 100 個合成樣本
- 儲存為 CSV 檔案
- 上傳至 watsonx.ai 專案資產
- 顯示 Asset ID 供參考

### 步驟 4：執行 Notebook 2 - 訓練、儲存和部署模型

在 watsonx.ai 平台上開啟並執行 `2_model_training_and_deployment.ipynb`

這將：
- 尋找並載入最新的訓練資料
- 載入/建立模型
- 訓練模型（示範工作流程）
- 轉換為 SavedModel 格式
- 儲存至 watsonx.ai 部署空間
- 自動建立線上部署
- 等待部署就緒
- 顯示 Model ID 和 Deployment ID

### 步驟 5：執行 Notebook 3 - 測試已部署的模型

在 watsonx.ai 平台上開啟並執行 `3_deployment_testing.ipynb`

這將：
- 從環境變數載入部署資訊
- 產生 5 個測試樣本
- 發送批次預測請求
- 顯示並儲存結果

## 📊 模型架構

模型（`buy_model_exchange`）具有：
- **32 個輸入**：嵌入、時間序列和連續特徵的混合
- **3 個輸出**：
  - `buy_19`：19 類別分類
  - `order_6`：6 類別分類
  - `exchange_9`：9 類別分類
- **總參數數量**：約 130 萬（示範模型已簡化）

## 🔍 資料架構

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

## 🛠️ 客製化

### 修改樣本數量

在 watsonx.ai 平台上編輯 `1_data_generation.ipynb`，變更：
```python
num_samples = 100  # 變更為所需數量
```

### 載入您自己的 H5 模型

在 watsonx.ai 平台上編輯 `2_model_training_and_deployment.ipynb`，將示範模型部分替換為：
```python
# 從專案資產載入您的 H5 模型
h5_model_content = client.data_assets.get_content('YOUR_H5_MODEL_ASSET_ID')
with open('temp_model.h5', 'wb') as f:
    f.write(h5_model_content)
model = keras.models.load_model('temp_model.h5')
```

### 實作實際訓練

在 watsonx.ai 平台上編輯 `2_model_training_and_deployment.ipynb`，實作適當的資料前處理和訓練：
```python
# 準備 X_train（32 個輸入陣列的列表）和 y_train（3 個輸出陣列的列表）
X_train = [...]  # 32 個陣列
y_train = [...]  # 3 個陣列

# 訓練模型
history = model.fit(
    X_train,
    y_train,
    epochs=10,
    batch_size=32,
    validation_split=0.2,
    verbose=1
)
```

### 變更測試樣本數量

在 watsonx.ai 平台上編輯 `3_deployment_testing.ipynb`，變更：
```python
num_test_samples = 5  # 變更為所需數量
```

## 📝 注意事項

### 重要考量

1. **執行環境**：這些 notebook 必須在 **watsonx.ai 平台**上執行，而非本地 Jupyter 環境。

2. **環境變數**：在 watsonx.ai 專案設定中配置環境變數，切勿在程式碼中硬編碼 API 金鑰。

3. **資料前處理**：筆記本展示工作流程。在生產環境中，根據您的特定資料格式實作適當的資料前處理。

4. **模型載入**：示範建立簡化的模型。在生產環境中，從專案資產載入您實際的預訓練 H5 模型。

5. **訓練**：實際的模型訓練需要適當的資料前處理，將 CSV 資料轉換為正確的輸入格式（32 個獨立陣列）。

6. **自動部署**：筆記本 2 會自動部署模型。如果存在同名部署，將會被替換。

7. **部署命名**：部署名稱遵循模式 `{MODEL_NAME}_deployment`。確保此名稱在您的空間中是唯一的。

### 疑難排解

**問題**：「缺少必要的環境變數」
- **解決方案**：在 watsonx.ai 專案設定中配置 PROJECT_ID、SPACE_ID 和 WX_API_KEY 環境變數

**問題**：「在專案資產中找不到訓練資料」
- **解決方案**：先執行筆記本 1 以產生並上傳訓練資料

**問題**：「模型架構不匹配」
- **解決方案**：確保您的 H5 模型具有與配置中定義相同的輸入/輸出結構

**問題**：「找不到部署」（筆記本 3）
- **解決方案**：
  1. 確保筆記本 2 成功完成並建立了部署
  2. 驗證 DEPLOYMENT_NAME 與部署名稱相符（應為 `{MODEL_NAME}_deployment`）
  3. 檢查 SPACE_ID 是否正確
  4. 查看錯誤訊息中顯示的可用部署列表

**問題**：「401 未授權」（筆記本 3）
- **解決方案**：檢查您的 WX_API_KEY 是否有效且具有部署空間的存取權限

**問題**：「部署失敗」（筆記本 2）
- **解決方案**：
  1. 檢查部署狀態中的錯誤訊息
  2. 驗證模型格式是否正確（SavedModel）
  3. 確保執行時規格與您的 TensorFlow 版本相符
  4. 檢查部署空間配額和限制

## 🔗 相關檔案

- `../model_migration/src/generate_input_data_example.py` - 輸入資料產生的參考
- `../model_migration/model/init_weight_savedmodel_20260310_181624/` - SavedModel 結構範例
- `../model_migration/台新提供資料/init_weight.h5` - 原始 H5 模型

## 📚 其他資源

- [watsonx.ai 文件](https://www.ibm.com/docs/en/watsonx-as-a-service)
- [TensorFlow SavedModel 指南](https://www.tensorflow.org/guide/saved_model)
- [IBM watsonx.ai Python SDK](https://ibm.github.io/watsonx-ai-python-sdk/)
- [watsonx.ai REST API](https://cloud.ibm.com/apidocs/machine-learning)

## ✅ 成功標準

成功執行所有三個筆記本後，您應該擁有：
- ✅ CSV 訓練資料已上傳至 watsonx.ai 專案資產
- ✅ TensorFlow 模型已儲存在 watsonx.ai 部署空間
- ✅ 模型已自動部署並具有線上端點
- ✅ 部署處於「就緒」狀態
- ✅ 已驗證已部署模型的預測
- ✅ 預測結果已儲存至 JSON 檔案

## 🎯 後續步驟

1. 為您的特定使用案例實作適當的資料前處理
2. 載入您實際的預訓練 H5 模型
3. 使用真實資料執行實際的模型訓練/微調
4. 監控生產環境中的模型效能
5. 設定自動化重新訓練流程
6. 實作模型版本控制和 A/B 測試
7. 建立自動化測試和驗證工作流程
8. 新增部署健康檢查和監控
9. 實作失敗部署的回滾機制
10. 設定 CI/CD 流程以進行自動化模型更新