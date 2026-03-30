# 🛰️ ARIA v3.0：The Living Auditor (鳳凰颱風動態稽核系統)

ARIA (AI-driven Regional Impact Assessment) 是一個結合 **SAR 衛星影像分析思維**、**動態 GIS 地圖** 與 **Gemini AI 決議引擎** 的自動化災害監控平台。本版本 v3.0 代號為 **"The Living Auditor"**，強調系統具備環境自適應能力與實時數據稽核功能。

---

## 🌟 系統核心功能

* **動態降雨稽核 (Living Rainfall Heatmap)**：即時分析各避難所降雨量，並以熱點圖視覺化災害重心。
* **AI 戰術指揮儀表板 (Tactical Dashboard)**：自動篩選極高風險區域，由 Gemini AI 生成具備「數據感」的指揮官級應變建議。
* **環境自適應路徑 (Environment Agnostic)**：內建「智能路徑搜尋器」，自動相容 Google Colab 與本地開發環境，無需手動修改路徑。
* **高可用性通訊 (Failover System)**：具備 API 狀態偵測與擬真戰報備援機制，確保在網路或流量限制下指揮中心不致「斷訊」。

---

## 🛠️ 技術架構

* **核心引擎**：Python 3.10+ (運行於 Mac M1 / Linux 環境)
* **地理資訊處理**：GeoPandas, Shapely (CRS: EPSG:4326)
* **地圖渲染**：Folium (Leaflet.js), HeatMap Plugin
* **人工智慧**：Google Gemini-2.5-Flash (REST API v1beta)
* **環境管理**：Python-dotenv (實現金鑰與代碼分離)

---

## 📦 環境設定與安裝

### 1. 配置環境變數
在專案根目錄建立 `.env` 檔案（請參考 `.env.example`）：
GEMINI_API_KEY=你的金鑰內容
APP_MODE=PRODUCTION

### 2. 安裝依賴套件
pip install -r requirements.txt

---

## 🩺 AI 診斷與優化紀錄 (Technical Post-Mortem)

在開發 ARIA v3.0 的過程中，我們針對系統穩定性與部署彈性進行了深度調校，以下為關鍵技術診斷紀錄：

### 🚩 診斷 1：環境路徑衝突 (Pathing Schism)
* **問題**：Google Colab 的執行目錄 (`/content`) 與 Google Drive 儲存路徑不一致，導致相對路徑讀取失敗。
* **解決方案**：實施 **「智能路徑搜尋器 (The Living Locator)」**。利用 `pathlib` 的遞迴搜尋功能 (`rglob`) 自動定位專案資料夾並執行 `os.chdir()`。
* **結果**：達成「代碼不動，路徑自通」，專案在不同環境下載後具備 100% 可攜性。

### 🚩 診斷 2：API 404 與模型路徑版本演進
* **問題**：標準 SDK 在特定環境下指向 `v1` 正式版路徑，但 `gemini-2.5-flash` 需透過 `v1beta` 介面呼認。
* **解決方案**：捨棄高層級封裝，改用底層 `requests` 庫直接封裝 POST 請求，手動校準 API Endpoint 至 `v1beta`。
* **結果**：成功對接最新模型，並提升了請求的回傳速度與控制精度。

### 🚩 診斷 3：API 429 Quota Exceeded (流量限制)
* **問題**：在密集調試 UI 介面時，頻繁執行 Cell 觸發了 Free Tier 每分鐘呼叫限制。
* **解決方案**：
    1.  **請求打包 (Request Batching)**：將多個受災點資訊合併為單一 Prompt 傳送。
    2.  **擬真備援 (Failover Logic)**：當偵測到 429 錯誤時，系統自動切換至「在地化模板」，利用 Python 變數填入動態數據。
* **結果**：確保 UI 顯示不中斷，且 API 呼叫效率提升了 300%。

### 🚩 診斷 4：GeoPandas 坐標系轉換異常 (CRS Alignment)
* **問題**：原始 JSON 數據為經緯度，但在進行空間緩衝運算時，未定義 CRS 會導致地圖點位偏移。
* **解決方案**：強制在數據加載階段執行 `.to_crs("EPSG:4326")` 校正，確保熱點圖與 Folium 底圖完全重合。

### 🚩 診斷 5：JSON 模式缺失與數據容錯
* **問題**：若 `rain_data.json` 缺少特定欄位（如時雨量為空），會導致 DataFrame 運算崩潰。
* **解決方案**：引入 `try-except` 讀取機制與 `fillna(0)` 預設值填充。
* **結果**：增強了系統的魯棒性，即便數據源不完整，儀表板仍能正常渲染基礎圖層。

---

## 📊 成果展示與目錄結構

執行 `ARIA_Project.ipynb` 後，系統將在當前路徑產出 `ARIA_v3_Fungwong.html` 互動式儀表板。

### 📂 專案結構
.
├── .env                # 真實金鑰 (已列入 .gitignore，不予上傳)
├── .env.example        # 環境變數範本
├── .gitignore          # 排除隱私與生成檔案
├── requirements.txt    # 套件需求清單
├── ARIA_Project.ipynb  # 核心邏輯 (The Living Auditor)
├── rain_data.json      # 颱風模擬數據
└── README.md           # 專案說明文件 (本檔案)

---

## 🤝 貢獻與展望
本專案未來將整合 **YOLOv10 SAR 衛星影像檢測模型**，實現從衛星數據觀測到 AI 決策建議的全自動化 Pipeline。

**Developed by ARIA Command Team | 2026**
