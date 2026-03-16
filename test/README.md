# 🛒 AI 跨平台商品比對系統

智慧型商品比對系統，結合向量嵌入和 AI 驗證技術，自動比對 MOMO 和 PChome 的相同商品。

## 📋 功能特色

- 🕷️ **自動爬蟲**：從 MOMO 和 PChome 抓取商品資料
- 🔍 **雙階段比對**：
  - Stage 1: 向量語意搜尋（Sentence Transformers）
  - Stage 2: AI 智能驗證（Google Gemini）
- 💾 **資料管理**：支援追加或覆蓋模式保存爬蟲資料
- 🎨 **友善介面**：Streamlit 網頁介面，操作簡單直觀

## � 系統需求

- **作業系統**：Windows 10/11, macOS, Linux
- **Python**：3.10 或以上版本
- **瀏覽器**：Google Chrome（用於網頁爬蟲）
- **網路連線**：需要穩定的網路連線
- **硬碟空間**：至少 2GB（用於模型和依賴套件）

## �🚀 完整安裝教學（給新使用者）

### 步驟 1：安裝必要軟體

#### 1.1 安裝 Python

**Windows：**
1. 前往 [Python 官網](https://www.python.org/downloads/)
2. 下載 Python 3.10 或更新版本
3. 執行安裝程式，**務必勾選「Add Python to PATH」**
4. 驗證安裝：
   ```powershell
   python --version
   ```

**macOS：**
```bash
# 使用 Homebrew 安裝
brew install python@3.10
```

**Linux (Ubuntu/Debian)：**
```bash
sudo apt update
sudo apt install python3.10 python3-pip
```

#### 1.2 安裝 Google Chrome

- 前往 [Chrome 官網](https://www.google.com/chrome/) 下載並安裝

#### 1.3 安裝 uv（Python 套件管理器，可選但推薦）

**Windows PowerShell：**
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS/Linux：**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 步驟 2：下載專案

**方法一：使用 Git（推薦）**
```bash
# 如果還沒安裝 Git，先安裝 Git：https://git-scm.com/
git clone <你的專案網址>
cd test
```

**方法二：直接下載 ZIP**
1. 點擊專案頁面的「Code」→「Download ZIP」
2. 解壓縮到任意資料夾
3. 開啟終端機並切換到該資料夾：
   ```bash
   cd C:\Users\你的使用者名稱\Downloads\test
   ```

### 步驟 3：安裝依賴套件

在專案資料夾中執行：

**使用 uv（推薦，速度更快）：**
```bash
uv sync
```

**或使用傳統 pip：**
```bash
pip install -r requirements.txt
```

這會自動安裝所有需要的套件，包括：
- streamlit
- selenium
- pandas
- torch
- sentence-transformers
- google-generativeai
- python-dotenv

### 步驟 4：設定 API Key（重要！）

#### 4.1 取得 Google Gemini API Key

1. 前往 [Google AI Studio](https://makersuite.google.com/app/apikey)
2. 使用 Google 帳號登入
3. 點擊「Create API Key」按鈕
4. 複製產生的 API Key（格式類似：`AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`）

> **注意**：API Key 是免費的，但有使用配額限制。請妥善保管，不要分享給他人。

#### 4.2 建立環境變數檔案

在專案資料夾中：

**Windows：**
```powershell
# 複製範例檔案
copy .env.example .env

# 使用記事本編輯（或任何文字編輯器）
notepad .env
```

**macOS/Linux：**
```bash
# 複製範例檔案
cp .env.example .env

# 使用文字編輯器開啟
nano .env
# 或
vim .env
```

#### 4.3 填入你的 API Key

在 `.env` 檔案中，將 `你的_Gemini_API_金鑰` 替換為你剛才複製的 API Key：

```env
GEMINI_API_KEY=AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
GEMINI_MODEL=gemini-2.5-flash
MODEL_PATH=models/models20-multilingual-e5-large_fold_1
```

**儲存檔案並關閉編輯器。**

### 步驟 5：下載或準備嵌入模型

專案需要 `multilingual-e5-large` 模型。有兩種方式：

**方法一：從專案提供的模型資料夾（如果已包含）**
- 確認 `models/models20-multilingual-e5-large_fold_1/` 資料夾存在
- 如果存在，跳過此步驟

**方法二：自動下載（首次執行時）**
- 程式會在首次執行時自動下載模型
- 需要約 1-2GB 的下載量和儲存空間

### 步驟 6：啟動應用程式

在專案資料夾中執行：

**使用 uv：**
```bash
uv run streamlit run matcher_app.py
```

**或使用一般 Python：**
```bash
streamlit run matcher_app.py
```

### 步驟 7：開始使用！

應用程式會自動在瀏覽器中開啟。如果沒有自動開啟，請手動訪問：

- **本機訪問**：http://localhost:8501
- **區域網路訪問**（從其他裝置）：http://你的電腦IP:8501

你會看到兩個選項：
1. **📁 使用現有資料**：載入已有的商品資料進行比對
2. **🕷️ 開始爬蟲**：抓取最新的商品資料

## 🎯 快速開始（已安裝過的使用者）

### 1. 安裝依賴

```bash
# 使用 uv（推薦）
uv sync

# 或使用 pip
pip install -r requirements.txt
```

### 2. 設定 API Key（重要！）

**方法一：使用環境變數檔案（推薦）**

複製範例檔案並填入你的 API Key：

```bash
copy .env.example .env
```

編輯 `.env` 檔案：

```env
GEMINI_API_KEY=你的_Gemini_API_金鑰
```

**方法二：設定系統環境變數**

Windows PowerShell：
```powershell
$env:GEMINI_API_KEY="你的_Gemini_API_金鑰"
```

**方法三：在應用程式中手動輸入**

如果沒有設定環境變數，應用程式啟動時會在側邊欄顯示輸入框。

### 3. 取得 Gemini API Key

1. 前往 [Google AI Studio](https://makersuite.google.com/app/apikey)
2. 登入 Google 帳號
3. 點擊「Create API Key」
4. 複製 API Key 並保存到 `.env` 檔案

### 4. 啟動應用程式

```bash
uv run streamlit run matcher_app.py
```

應用程式會在瀏覽器中自動開啟：
- 本機：http://localhost:8501
- 區域網路：http://你的IP:8501

## 📁 專案結構

```
test/
├── matcher_app.py           # Streamlit 主程式
├── product_scraper.py       # 爬蟲模組
├── .env                     # 環境變數（包含 API Key，不會被提交）
├── .env.example            # 環境變數範例
├── .gitignore              # Git 忽略規則
├── momo.csv                # MOMO 商品資料
├── pchome.csv              # PChome 商品資料
└── models/                 # 嵌入模型目錄
    └── models20-multilingual-e5-large_fold_1/
```

## 🔒 安全性說明

### API Key 保護機制

1. **環境變數隔離**：API Key 存放在 `.env` 檔案中，不會被提交到 Git
2. **多層讀取**：支援 Streamlit Secrets、環境變數、手動輸入三種方式
3. **Git 忽略**：`.gitignore` 自動排除 `.env` 檔案
4. **密碼輸入**：手動輸入時使用密碼遮罩

### 部署到 Streamlit Cloud

在 Streamlit Cloud 上部署時：

1. 進入專案設定
2. 選擇「Secrets」
3. 添加：
   ```toml
   GEMINI_API_KEY = "你的_API_金鑰"
   ```

## 📖 使用說明

### 1. 爬取商品

1. 選擇「開始爬蟲」
2. 輸入搜尋關鍵字（中文 + 英文標記）
3. 設定抓取數量
4. 選擇儲存模式（追加/覆蓋）
5. 開始爬取

### 2. 比對商品

1. 選擇「使用現有資料」
2. 選擇商品類別和目標商品
3. 點擊「啟動雙階段比對引擎」
4. 查看 AI 分析結果

## ⚙️ 進階設定

### 修改相似度門檻

編輯 `matcher_app.py` 第 479 行：

```python
threshold = 0.739465  # 調整此值
```

### 更換 Gemini 模型

在 `.env` 檔案中設定：

```env
GEMINI_MODEL=gemini-pro
```

## 🛠️ 技術棧

- **後端框架**：Streamlit
- **爬蟲引擎**：Selenium WebDriver
- **向量模型**：Sentence Transformers (multilingual-e5-large)
- **AI 驗證**：Google Gemini API
- **資料處理**：Pandas, NumPy
- **深度學習**：PyTorch

## 📝 注意事項

1. **API 配額**：注意 Gemini API 的使用配額限制
2. **爬蟲禮儀**：程式已加入隨機延遲，避免過於頻繁請求
3. **網路連線**：需要穩定的網路連線來訪問 API 和爬取網頁
4. **Chrome Driver**：Selenium 需要 Chrome 瀏覽器

## 🔧 疑難排解

### API Key 錯誤

```
請設定 Gemini API Key 才能使用 AI 驗證功能
```

**解決方法**：檢查 `.env` 檔案是否正確設定

### 模型載入失敗

```
找不到模型路徑：models/...
```

**解決方法**：確認模型目錄存在且路徑正確

### 爬蟲失敗

**解決方法**：
1. 檢查網路連線
2. 確認 Chrome 瀏覽器已安裝
3. 更新 Selenium WebDriver

## 📄 授權

本專案僅供學習和研究使用。

## 🤝 貢獻

歡迎提交 Issue 和 Pull Request！

---

**注意**：請勿將包含真實 API Key 的 `.env` 檔案提交到公開的版本控制系統。
