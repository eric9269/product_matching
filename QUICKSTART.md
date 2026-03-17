# 🛒 快速開始指南

> 這份指南適合第一次使用的新手，**3 分鐘**即可上手。

---

## 🎯 這個程式能做什麼？

自動從 **MOMO** 和 **PChome** 抓取商品資料，並使用 **AI 雙階段比對** 找出相同商品進行比價。

---

## ⚡ 快速安裝

### 步驟 1：下載專案

```bash
git clone <你的專案網址>
cd CSE
```

或點擊 GitHub 頁面右上角的「**Code**」→「**Download ZIP**」→ 解壓縮。

### 步驟 2：安裝 Python

前往 https://www.python.org/downloads/ 下載 Python 3.10+

> ⚠️ Windows 安裝時務必勾選「**Add Python to PATH**」

### 步驟 3：安裝依賴套件

```bash
# 使用 uv（推薦，速度快）
uv sync

# 或使用 pip
pip install -r requirements.txt
```

### 步驟 4：設定 API Key

1. 前往 https://makersuite.google.com/app/apikey 免費取得 Gemini API Key
2. 建立環境變數檔案：

```bash
cp .env.example .env
```

3. 編輯 `.env` 填入 API Key：

```env
GEMINI_API_KEY=你的API金鑰
```

### 步驟 5：安裝 Ollama（LLM 驗證引擎）

```bash
# Linux / macOS
curl -fsSL https://ollama.com/install.sh | sh

# 下載模型
ollama pull qwen2.5:14b
```

### 步驟 6：啟動程式

```bash
streamlit run src/matcher_app.py
```

瀏覽器會自動開啟 **http://localhost:8501** 🎉

---

## 📖 使用方式

1. **輸入關鍵字**（如：`iPhone 16`、`dyson 吸塵器`）
2. **等待爬蟲**完成（系統同時抓取 MOMO 與 PChome）
3. **點擊商品卡片**，觸發 AI 比對
4. **查看結果**！

---

## ❓ 常見問題

| 問題 | 解答 |
|------|------|
| 需要付費嗎？ | 不用！Gemini API 免費，Ollama 在本地運行 |
| 電腦需要很強嗎？ | 一般筆電（8GB RAM）即可，有 GPU 更佳 |
| 可以用手機看嗎？ | 程式在電腦執行，同 WiFi 下可用手機瀏覽器訪問 |
| API Key 安全嗎？ | 安全，存在本機 `.env` 檔案中，不會上傳 |

---

## 📘 更多資訊

詳細的技術說明與完整設定請參考 [README.md](./README.md)。

---

**祝你使用愉快！** 🚀
