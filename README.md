# 🛒 CSE — AI 跨平台商品比對系統

> **C**ross-platform **S**imilarity **E**ngine  
> 結合向量語意搜尋（Sentence Transformers）與 LLM 智能驗證（Ollama），自動比對 MOMO 與 PChome 平台上的相同商品。

---

## 📋 功能特色

| 功能 | 說明 |
|------|------|
| 🕷️ **自動爬蟲** | 使用 Selenium 從 MOMO 與 PChome 即時抓取商品資料 |
| 🔍 **雙階段比對引擎** | Stage 1 向量語意搜尋 + Stage 2 LLM 智能驗證 |
| 🔄 **雙向比對** | 支援 MOMO→PChome 或 PChome→MOMO 兩種比對方向 |
| 👥 **多用戶並行** | 佇列系統管理爬蟲與 LLM 請求，支援多人同時使用 |
| 💾 **資料管理** | 爬蟲資料支援追加或覆蓋模式，CSV 格式存取 |
| 🎨 **友善介面** | Streamlit 網頁介面，操作簡單直觀，支援手機瀏覽 |

---

## 🏗️ 系統架構

```
使用者輸入關鍵字
        │
        ▼
┌───────────────────┐
│   Stage 0: 爬蟲    │  ← Selenium 同時爬取 MOMO & PChome
│  product_scraper   │
└────────┬──────────┘
         │ 商品資料 (CSV)
         ▼
┌───────────────────┐
│  Stage 1: 向量比對  │  ← SentenceTransformer (multilingual-e5-large)
│ similarity_calculator│    計算餘弦相似度，門檻 0.739465
└────────┬──────────┘
         │ 候選配對
         ▼
┌───────────────────┐
│  Stage 2: LLM 驗證 │  ← Ollama (qwen2.5:14b)
│   matcher_app      │    批次驗證品牌/型號/規格是否一致
└────────┬──────────┘
         │
         ▼
     比價結果呈現
```

---

## 📁 專案結構

```
CSE/
├── README.md                  # 專案完整說明文件（本檔案）
├── QUICKSTART.md              # 快速安裝指南（給新使用者）
├── pyproject.toml             # Python 專案設定與依賴套件定義（uv / pip 用）
├── requirements.txt           # pip 依賴套件清單（替代 pyproject.toml）
├── uv.lock                    # uv 套件管理器鎖定檔（確保依賴版本一致）
├── .python-version            # Python 版本指定（3.10）
├── .env.example               # 環境變數範例檔案（需複製為 .env 並填入 API Key）
├── .gitignore                 # Git 忽略規則（排除 .env、__pycache__ 等）
├── .gitattributes             # Git 屬性設定
├── cloudflared-config.yml     # Cloudflare Tunnel 設定範例（用於外網部署）
│
├── src/                       # 原始碼目錄
│   ├── matcher_app.py         # Streamlit 主程式（UI + 比對流程 + 佇列管理）
│   ├── product_scraper.py     # 爬蟲模組（Selenium 抓取 MOMO & PChome）
│   └── similarity_calculator.py # 第一階段向量相似度計算模組
│
├── data/                      # 商品資料目錄
│   ├── momo.csv               # MOMO 商品資料（爬蟲產出）
│   └── pchome.csv             # PChome 商品資料（爬蟲產出）
│
├── state/                     # 執行期 JSON 狀態檔（佇列、記錄、性能）
│   ├── active_scrapers.json
│   ├── active_llm_requests.json
│   ├── active_users.json
│   ├── search_logs.json
│   ├── user_peak.json
│   ├── stage2_performance.json
│   └── session_comparison_times.json
│
└── docs/                      # 文件目錄
```

---

## 📄 各檔案詳細說明

### 原始碼 (`src/`)

#### `src/matcher_app.py` — Streamlit 主程式（~2800 行）

整個系統的核心入口，包含：

- **UI 介面**：搜尋框、商品卡片、比價彈窗、進度條
- **Session 管理**：使用 `st.session_state` 追蹤用戶狀態
- **第一階段比對 (Stage 1)**：呼叫 SentenceTransformer 模型計算向量相似度
  - 模型來源：[`eric920609/20-multilingual-e5-large_fold_1`](https://huggingface.co/eric920609/20-multilingual-e5-large_fold_1)（HuggingFace）
  - 門檻值：`0.739465`（餘弦相似度）
- **第二階段驗證 (Stage 2)**：透過 Ollama 呼叫本地 LLM 進行批次驗證
  - 預設模型：`qwen2.5:14b`
  - 判斷規則：品牌、型號、規格、容量、數量一致（顏色不同視為相同商品）
- **佇列系統**：管理多用戶的爬蟲請求與 LLM 請求
  - 爬蟲佇列：`state/active_scrapers.json`
  - LLM 佇列：`state/active_llm_requests.json`（最多 3 個並行）
  - 用戶追蹤：`state/active_users.json`、`state/user_peak.json`
- **搜尋記錄**：`state/search_logs.json`（記錄每次搜尋的關鍵字與結果）

主要函數：

| 函數 | 說明 |
|------|------|
| `get_api_key()` | 從 Streamlit Secrets / 環境變數 / 用戶輸入取得 Gemini API Key |
| `load_model(path)` | 載入 SentenceTransformer 模型（有 `@st.cache_resource` 快取） |
| `load_local_data()` | 從 `data/` 目錄載入現有的 CSV 商品資料 |
| `calculate_similarities_in_memory()` | 在記憶體中計算兩平台商品的向量相似度 |
| `gemini_verify_batch()` | 呼叫 Ollama LLM 批次驗證候選商品配對 |
| `handle_product_search()` | 處理搜尋流程（並行爬蟲 + 相似度計算） |
| `show_comparison_dialog()` | 顯示商品比價結果的彈窗 |

#### `src/product_scraper.py` — 爬蟲模組（~1460 行）

使用 Selenium WebDriver 自動化爬取商品資料：

| 函數 | 說明 |
|------|------|
| `fetch_products_for_momo()` | 從 MOMO 購物網搜尋並抓取商品（標題、價格、圖片、URL、SKU） |
| `fetch_products_for_pchome()` | 從 PChome 線上購物搜尋並抓取商品 |
| `save_to_csv()` | 將商品資料儲存為 CSV，支援追加模式 |

特性：
- Headless Chrome 模式運行
- 隨機延遲避免被偵測
- 支援進度回呼（`progress_callback`）
- 支援取消機制（`cancel_check`）
- 自動重試機制

#### `src/similarity_calculator.py` — 相似度計算模組（~185 行）

獨立的第一階段向量相似度計算工具：

| 函數 | 說明 |
|------|------|
| `prepare_text()` | 為不同平台的商品標題加上 `query:` / `passage:` 前綴 |
| `get_batch_embeddings()` | 批次計算文本的向量嵌入 |
| `calculate_similarities_for_all()` | 計算所有 MOMO 與 PChome 商品的相似度矩陣 |
| `calculate_all_similarities()` | 完整流程：載入資料 → 載入模型 → 計算相似度 → 儲存 JSON |

可作為獨立腳本執行：`python src/similarity_calculator.py`

### 資料目錄 (`data/`)

| 檔案 | 說明 |
|------|------|
| `momo.csv` | MOMO 商品資料，欄位：`id`, `title`, `price`, `image`, `url`, `sku`, `query` |
| `pchome.csv` | PChome 商品資料，欄位同上 |

這兩個檔案由爬蟲模組產生，也可手動準備。`query` 欄位記錄搜尋時使用的關鍵字。

### 狀態目錄 (`state/`)

| 檔案 | 說明 |
|------|------|
| `active_scrapers.json` | 爬蟲佇列狀態 |
| `active_llm_requests.json` | LLM 佇列狀態 |
| `active_users.json` | 在線用戶追蹤 |
| `search_logs.json` | 搜尋關鍵字與結果紀錄 |
| `user_peak.json` | 在線用戶峰值統計 |
| `stage2_performance.json` | Stage 2 LLM 驗證效能紀錄 |
| `session_comparison_times.json` | 每個 Session 的比對耗時統計 |

### 設定檔

| 檔案 | 說明 |
|------|------|
| `pyproject.toml` | Python 專案設定，定義名稱、版本、依賴套件（供 `uv` 或 `pip` 使用） |
| `requirements.txt` | pip 格式的依賴清單（與 `pyproject.toml` 功能重疊，提供給習慣 pip 的用戶） |
| `uv.lock` | `uv` 套件管理器的鎖定檔，確保所有開發者使用完全相同的依賴版本 |
| `.python-version` | 指定 Python 版本為 `3.10` |
| `.env.example` | 環境變數範例，使用者需複製為 `.env` 並填入自己的 API Key |
| `cloudflared-config.yml` | Cloudflare Tunnel 設定範例，用於將本地 Streamlit 服務暴露到外網 |

---

## 🔧 系統需求

- **Python** 3.10 或以上
- **Google Chrome**（Selenium 爬蟲需要）
- **Ollama**（本地 LLM 推理引擎，用於 Stage 2 驗證）
- **硬碟空間**：約 2GB（依賴套件 + 模型快取）
- **記憶體**：建議 8GB 以上

---

## 🚀 安裝與啟動

### 1. 安裝依賴

```bash
# 使用 uv（推薦，速度快）
uv sync

# 或使用 pip
pip install -r requirements.txt
```

### 2. 設定環境變數

```bash
cp .env.example .env
```

編輯 `.env` 檔案：

```env
GEMINI_API_KEY=你的_Gemini_API_金鑰
GEMINI_MODEL=gemini-2.5-flash
```

> **取得 API Key**：前往 [Google AI Studio](https://makersuite.google.com/app/apikey) 免費申請

### 3. 安裝 Ollama（Stage 2 LLM 驗證用）

```bash
# Linux
curl -fsSL https://ollama.com/install.sh | sh

# 下載預設模型
ollama pull qwen2.5:14b
```

### 4. 啟動應用程式

```bash
streamlit run src/matcher_app.py
```

瀏覽器會自動開啟 http://localhost:8501

---

## 📖 使用流程

### 方式一：即時搜尋比對

1. 在搜尋框輸入商品關鍵字（例如：`iPhone 16`）
2. 系統自動從 MOMO 與 PChome 爬取商品
3. 爬取完成後自動計算向量相似度（Stage 1）
4. 點擊任一商品卡片，觸發 LLM 驗證（Stage 2）
5. 查看比價結果

### 方式二：使用現有資料

1. 確認 `data/momo.csv` 與 `data/pchome.csv` 有資料
2. 啟動程式後直接選擇商品進行比對

---

## ⚙️ 環境變數一覽

| 變數名稱 | 預設值 | 說明 |
|---------|--------|------|
| `GEMINI_API_KEY` | （無） | Google Gemini API 金鑰 |
| `GEMINI_MODEL` | `gemini-2.5-flash` | Gemini 模型名稱 |
| `MODEL_PATH` | `eric920609/20-multilingual-e5-large_fold_1` | Stage 1 語意模型（HuggingFace ID 或本地路徑） |
| `OLLAMA_MODEL` | `qwen2.5:14b` | Stage 2 LLM 模型名稱 |
| `OLLAMA_HOST` | `http://localhost:11434` | Ollama 服務位址 |

---

## 🛠️ 技術棧

| 領域 | 技術 |
|------|------|
| 網頁框架 | Streamlit |
| 爬蟲引擎 | Selenium WebDriver + ChromeDriver |
| 向量模型 | Sentence Transformers (`multilingual-e5-large`) |
| LLM 驗證 | Ollama (`qwen2.5:14b`) |
| 資料處理 | Pandas, NumPy |
| 深度學習 | PyTorch |
| 並行處理 | ThreadPoolExecutor |

---

## 🔒 安全性說明

- API Key 存放在 `.env` 檔案中，已被 `.gitignore` 排除
- 支援三種 API Key 讀取方式：Streamlit Secrets → 環境變數 → 手動輸入
- 手動輸入時使用密碼遮罩

---

## 🌐 外網部署（可選）

使用 Cloudflare Tunnel 將本地服務暴露到網際網路：

1. 安裝 `cloudflared`
2. 修改 `cloudflared-config.yml` 中的 Tunnel ID 與 hostname
3. 執行 `cloudflared tunnel run my-price-compare`

---

## 🔧 疑難排解

| 問題 | 解決方法 |
|------|---------|
| 模型載入失敗 | 檢查網路連線，模型會從 HuggingFace 自動下載 |
| 爬蟲失敗 | 確認 Chrome 已安裝、網路連線正常 |
| LLM 驗證無回應 | 確認 Ollama 服務已啟動（`ollama serve`） |
| API Key 錯誤 | 檢查 `.env` 檔案是否正確設定 |

---

## 📝 注意事項

1. **API 配額**：Gemini API 有免費使用配額限制
2. **爬蟲禮儀**：程式已內建隨機延遲，避免過於頻繁的請求
3. **模型快取**：首次執行會從 HuggingFace 下載模型（約 1-2GB），之後會自動快取
4. **Ollama**：Stage 2 需要本地執行 Ollama，建議有 GPU 加速

---

## 📄 授權

本專案僅供學習和研究使用。

## 🤝 貢獻

歡迎提交 Issue 和 Pull Request！

---

**⚠️ 請勿將包含真實 API Key 的 `.env` 檔案提交到公開的版本控制系統。**
