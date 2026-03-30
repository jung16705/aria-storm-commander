# 🛰️ ARIA：鳳凰颱風戰術指揮儀表板 (AI-driven Regional Impact Assessment)

ARIA 是一個結合了 **SAR 衛星影像分析思維**、**GIS 地理資訊動態圖層** 與 **Gemini AI 決策模型** 的自動化災害監控平台。本專案以 2026 年鳳凰颱風為模擬情境，透過分析即時雨量與避難所地理數據，自動生成視覺化地圖與指揮官級別的緊急撤離建議。

---

## 🌟 系統核心功能

* **動態降雨熱區 (Rainfall Heatmap)**：即時視覺化受災區域的降雨強度分布，協助指揮官快速鎖定降雨重心。
* **AI 戰術指揮面板 (Tactical Dashboard)**：自動篩選受災最重的 3 處避難所，並由 Gemini AI 提供具備「數據感」的 3 句話精準應變建議。
* **多層次風險視覺化**：
    * 🔴 **CRITICAL (深紅星星)**：極高風險區，時雨量與地形風險達標。
    * 🟠 **WARNING (紅/橘標記)**：預警區域，需密切監控並準備物資。
    * 🟢 **SAFE (半透明微點)**：安全區域，最小化視覺干擾，保留地圖乾淨度。
* **高可用性通訊架構 (High Availability)**：具備自動路徑診斷與「擬真戰術備援系統」，確保在 API 異常時指揮中心依然能產出關鍵報告。

---

## 🛠️ 技術架構與開發環境

* **數據處理**：Python, Pandas, GeoPandas
* **地圖引擎**：Folium (Leaflet.js)
* **AI 模型**：Google Gemini-2.5-Flash (透過 v1beta/REST 接口)
* **環境管理**：Python-dotenv (支援相對路徑移植)

---

## 📦 環境設定與安裝

本專案採用 **環境變數隔離** 與 **相對路徑設計**，確保程式碼具備高度的可移植性，不論在 Colab 或本地環境皆可直接執行。

### 1. 取得 API 金鑰
請至 [Google AI Studio](https://aistudio.google.com/) 申請免費的 API Key。

### 2. 設定環境變數
在專案根目錄建立 `.env` 檔案（此檔案已列入 `.gitignore`，請勿上傳至公開 Repo）：
GEMINI_API_KEY=你的金鑰內容

### 3. 安裝依賴套件
請在終端機執行：
pip install pandas folium python-dotenv requests google-generativeai

---

## 🩺 AI 診斷與優化紀錄 (Technical Post-Mortem)

本專案在開發過程中，針對 AI 通訊穩定性進行了深度調校，成功克服了以下技術挑戰：

### 🚩 挑戰 1：API 404 Not Found (路徑與版本衝突)
* **問題現象**：SDK 預設指向 v1beta 路徑，但在特定專案環境下無法正確對接 gemini-1.5-flash 模型。
* **解決方案**：
    * 改用底層 requests 庫手動封裝 POST 請求，直接校準 API Endpoint 至正式路徑。
    * 實施 「自動偵測模式 (Auto-Discovery)」，動態匹配當前環境支援度最高的模型路徑（經測試為 models/gemini-2.5-flash）。

### 🚩 挑戰 2：API 429 Quota Exceeded (流量限制)
* **問題現象**：在密集開發與偵錯時，觸發了 Google Free Tier 的每分鐘呼叫次數限制。
* **解決方案**：
    * **請求打包 (Request Batching)**：將所有受災點資訊合併為單一 Prompt，將 API 呼叫次數優化至 1 次/執行。
    * **優雅降級 (Graceful Degradation)**：開發 「擬真戰術備援系統 (Failover System)」。當偵測到 429 錯誤或網路中斷時，自動切換至在地化模板，並動態填入 Python 變數數據，確保 UI 顯示不中斷。

---

## 📊 成果展示與資料夾結構

執行程式後將在當前路徑生成 ARIA_Final_Dashboard.html，具備：
1.  **固定式戰情看板**：右下角深色透明科技感介面，主動顯示實時 AI 戰術建議。
2.  **視覺層級優化**：自動過濾低風險點位，實現「視覺淨化」，讓決策者能在一秒內鎖定南澳、蘇澳等核心災區。

### 📂 專案結構
.
├── .env                # API 金鑰 (手動建立，不進入 Git)
├── .gitignore          # 排除隱私與暫存檔
├── ARIA_Project.ipynb  # 核心邏輯 (Colab/Jupyter)
├── rain_data.json      # 模擬颱風數據
└── README.md           # 專案說明書 (本文檔)

---

## 🤝 專案貢獻與未來展望
本專案未來計畫整合 SAR 衛星影像自動檢測模型 (YOLO)，實現從「衛星數據觀測」到「AI 決策建議」的全自動化 Pipeline。

**Developed by ARIA Command Team | 2026**
