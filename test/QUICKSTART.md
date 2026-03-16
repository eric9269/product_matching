# 🛒 AI 跨平台商品比對系統 - 使用指南

## 📥 給想要使用這個程式的人

### 🎯 這個程式能做什麼？

自動從 MOMO 和 PChome 抓取商品資料，並使用 AI 智慧比對出相同的商品。

---

## ⚡ 快速安裝（3 分鐘）

### 步驟 1：下載程式

點擊右上角綠色「**Code**」按鈕 → 選擇「**Download ZIP**」→ 解壓縮

### 步驟 2：安裝 Python

1. 前往 https://www.python.org/downloads/
2. 下載並安裝（記得勾選「Add Python to PATH」）

### 步驟 3：安裝套件

開啟終端機（在專案資料夾按右鍵選「在終端機中開啟」），執行：

```bash
pip install -r requirements.txt
```

### 步驟 4：設定 API Key

1. 前往 https://makersuite.google.com/app/apikey 取得免費 API Key
2. 複製 `.env.example` 檔案並改名為 `.env`
3. 用記事本開啟 `.env`，填入你的 API Key：
   ```
   GEMINI_API_KEY=你的API金鑰
   ```

### 步驟 5：啟動程式

```bash
streamlit run matcher_app.py
```

瀏覽器會自動開啟 http://localhost:8501

---

## 📖 詳細教學

需要更詳細的說明？請查看：

- **📘 完整安裝指南**：[INSTALLATION.md](./INSTALLATION.md)
  - 適合第一次使用 Python 的新手
  - 包含截圖和常見問題解答

- **📗 技術文件**：[README.md](./README.md)
  - 完整的功能說明
  - 進階設定選項

---

## ❓ 常見問題

### Q: 需要付費嗎？

A: 不用！Gemini API 是免費的，只需要 Google 帳號註冊。

### Q: 我的電腦需要很強嗎？

A: 不需要。一般筆電（8GB RAM）就可以順暢執行。

### Q: 可以用在手機上嗎？

A: 程式需要在電腦執行，但執行後可以用手機瀏覽器訪問（需要在同一個 WiFi）。

### Q: API Key 安全嗎？

A: 安全！API Key 儲存在本機的 `.env` 檔案中，不會上傳到網路。

### Q: 遇到錯誤怎麼辦？

A: 查看 [INSTALLATION.md](./INSTALLATION.md) 的「常見問題與解決方法」章節。

---

## 🎓 使用教學影片（建議觀看）

1. **第一次使用**：
   - 啟動程式
   - 爬取 10 筆 "iPhone" 商品
   - 執行比對

2. **進階功能**：
   - 追加不同關鍵字的資料
   - 調整比對參數
   - 匯出比對結果

---

## 📧 需要協助？

如果遇到任何問題：

1. ✅ 先檢查 [INSTALLATION.md](./INSTALLATION.md) 的常見問題
2. ✅ 確認已按照步驟正確安裝
3. ✅ 在 GitHub 開 Issue 描述你的問題（附上錯誤訊息截圖）

---

## 🎉 開始使用

準備好了嗎？

1. 完成上述安裝步驟
2. 啟動程式
3. 選擇「開始爬蟲」
4. 輸入關鍵字（例如：iPhone, dyson）
5. 等待結果並查看 AI 比對分析！

**祝你使用愉快！** 🚀
