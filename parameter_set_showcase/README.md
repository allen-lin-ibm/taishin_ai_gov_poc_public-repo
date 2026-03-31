# Parameter Set 展示

本目錄包含展示 IBM watsonx.ai Parameter Sets 功能的筆記本。

## 📋 概述

watsonx.ai 中的 Parameter Sets 可讓您：
- 集中儲存和管理模型參數
- 對模型配置進行版本控制
- 在多個部署之間共享參數
- 輕鬆切換不同的參數配置
- 維持跨環境的一致性

## 📓 筆記本

### Parameter Set 示範
**檔案**：`parameter_set_showcase.ipynb`

本筆記本展示：
1. 在 watsonx.ai 中建立 parameter sets
2. 儲存模型配置
3. 檢索和使用 parameter sets
4. 更新 parameter sets
5. 管理參數版本
6. 將參數應用於模型部署

## 🚀 使用方式

### 先決條件

```bash
# 安裝必要套件
pip install numpy pandas ibm-watsonx-ai ibm-cos-sdk

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
jupyter notebook parameter_set_showcase.ipynb
```

## 🔍 主要功能

### Parameter Set 管理
- **建立**：定義具有自訂配置的新 parameter sets
- **儲存**：將參數儲存至 watsonx.ai 以供重複使用
- **檢索**：依名稱或 ID 載入 parameter sets
- **更新**：修改現有的 parameter sets
- **版本控制**：追蹤參數隨時間的變化
- **刪除**：移除未使用的 parameter sets

### 使用案例

1. **模型超參數**：儲存訓練超參數以確保可重現性
2. **部署配置**：管理跨環境的部署設定
3. **特徵工程**：儲存特徵轉換參數
4. **前處理設定**：維持一致的資料前處理配置
5. **A/B 測試**：輕鬆切換不同的參數配置

## 📝 Parameter Set 結構

典型的 parameter set 包含：

```python
parameter_set = {
    'name': 'my_model_params',
    'description': '生產環境的模型配置',
    'parameters': {
        'learning_rate': 0.01,
        'max_depth': 6,
        'n_estimators': 100,
        'batch_size': 32,
        'epochs': 50,
        # 根據需要新增更多參數
    }
}
```

## 🛠️ 常見操作

### 建立 Parameter Set

```python
from ibm_watsonx_ai import APIClient

# 初始化客戶端
client = APIClient(credentials)
client.set.default_space(space_id)

# 建立 parameter set
parameter_set_details = client.parameter_sets.store({
    'name': 'my_params',
    'parameters': {
        'param1': value1,
        'param2': value2
    }
})
```

### 檢索 Parameter Set

```python
# 依 ID 取得
params = client.parameter_sets.get_details(parameter_set_id)

# 列出所有 parameter sets
all_params = client.parameter_sets.list()
```

### 更新 Parameter Set

```python
# 更新參數
client.parameter_sets.update(
    parameter_set_id,
    {
        'parameters': {
            'param1': new_value1,
            'param2': new_value2
        }
    }
)
```

### 在部署中使用 Parameter Sets

```python
# 使用 parameter set 部署模型
deployment_details = client.deployments.create(
    model_id,
    {
        'name': 'my_deployment',
        'parameter_set_id': parameter_set_id
    }
)
```

## 📚 其他資源

- [IBM watsonx.ai Python SDK - Parameter Sets](https://ibm.github.io/watsonx-ai-python-sdk/core_api.html#parameter-sets)
- [watsonx.ai 文件](https://www.ibm.com/docs/en/watsonx-as-a-service)
- [參數管理最佳實踐](https://www.ibm.com/docs/en/watsonx-as-a-service)

## 💡 最佳實踐

1. **命名慣例**：使用描述性名稱來指示目的和環境
   - 範例：`prod_model_v1_params`、`dev_experiment_params`

2. **版本控制**：在 parameter set 名稱或描述中包含版本資訊
   - 範例：`model_params_v2.1`、`config_2024_03_31`

3. **文件記錄**：新增詳細描述來說明參數用途和有效範圍

4. **環境分離**：為開發、測試和生產環境維護獨立的 parameter sets

5. **驗證**：在儲存前驗證參數值以防止部署問題

6. **備份**：定期匯出 parameter sets 以進行備份和災難復原

7. **存取控制**：使用適當的權限來保護敏感配置

## ⚠️ 重要注意事項

1. **參數類型**：確保參數值可進行 JSON 序列化
2. **大小限制**：注意 parameter set 的大小限制
3. **命名**：Parameter set 名稱在空間內必須是唯一的
4. **相依性**：記錄參數之間的任何相依性
5. **測試**：始終在非生產環境中測試參數變更

## 🐛 疑難排解

**問題**：「找不到 parameter set」
- **解決方案**：驗證 parameter set ID 並確保您在正確的空間中

**問題**：「無效的參數值」
- **解決方案**：檢查所有參數值是否可進行 JSON 序列化

**問題**：「權限被拒絕」
- **解決方案**：驗證您對該空間具有適當的存取權限

**問題**：「Parameter set 名稱已存在」
- **解決方案**：使用唯一名稱或更新現有的 parameter set

## ✅ 成功標準

成功執行筆記本後，您應該了解：
- ✅ 如何在 watsonx.ai 中建立 parameter sets
- ✅ 如何檢索和使用儲存的參數
- ✅ 如何更新參數配置
- ✅ 如何將參數應用於模型部署
- ✅ 參數管理的最佳實踐

## 🎯 後續步驟

1. 為您的生產模型建立 parameter sets
2. 實作參數版本控制策略
3. 設定自動化參數驗證
4. 為常見使用案例建立參數範本
5. 將 parameter sets 整合至 CI/CD 流程
6. 記錄參數相依性和關係
7. 實作參數變更追蹤和稽核
8. 建立 parameter set 備份和復原程序