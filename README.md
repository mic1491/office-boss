# 社畜辦公室 CAM 01

把你**真實的待辦事項**變成一間動物員工辦公室的放置遊戲。你是老闆：把待辦派給員工，現實中做完就按完成，公司賺錢、升級辦公室。

畫面風格：監視器畫面（CAM 01 / REC / 台北時間）＋等角插畫辦公室，2D SVG。

## 檔案結構

只有一個檔案，沒有建置步驟、沒有外部相依：

```
index.html   ← 全部 HTML / CSS / JS 都在這裡（JS 在 <script id="app-js">）
README.md
```

本機預覽：直接用瀏覽器打開 `index.html`，或 `python3 -m http.server` 後開 http://localhost:8000

部署：Netlify 綁定此 repo，publish directory = `/`（根目錄），沒有 build command。push 到 main 就會自動部署。

## 程式結構（index.html 內 `app-js`）

| 區塊 | 說明 |
|---|---|
| `CSS` | 所有樣式，是一個字串陣列，啟動時注入 `<style>` |
| `CATS` | 任務分類：write 寫作 / design 設計 / tech 技術 / plan 企劃 / misc 雜事 |
| `ROSTER` | 員工資料（名字、動物、職稱、擅長分類、衣服顏色）。penguin 需在商店招募 |
| `SHOP` | 辦公室升級項目與價格 |
| `seed()` | 第一次打開時的初始狀態與範例任務 |
| `eff()` | 依時間計算員工目前體力 / 心情（會隨時間恢復） |
| `guessCat()` | 用關鍵字自動判斷任務分類 |
| `bestFor()` | 「交給最適合的」的評分邏輯 |
| 存檔區 `save()` | 存到 `localStorage`（key：`office-boss-v1`）。若在 Claude 頁面中執行，另會透過 `claude.use("artifact")` 存回頁面本身 |
| 角色繪圖 `head()` / `eyes()` / `bodySVG()` | 每種動物的 SVG，原點在腳底中心，往上約 100 單位 |
| 場景 `staticScene()` / `deskEl()` / `propEls()` | 辦公室背景（viewBox 0 0 1000 700）、桌子、會依升級出現的家具 |
| `DESKS` / `SPOTS` | 座位座標、閒晃地點座標 |
| 行為 `desired()` / `think()` | 員工狀態機：cheer 歡呼 / sleep 睡覺（體力 < 20）/ work 工作（有進行中任務）/ idle 閒晃 |
| `LINES` | 對話泡泡台詞庫，`{t}` = 任務名、`{n}` = 件數 |
| `frame()` | requestAnimationFrame 主迴圈：移動、依 y 排序繪製、泡泡跟隨、跑馬燈 |
| UI `renderUI()` / `bindUI()` | 下方面板：新增待辦、看板分頁、員工狀態、商店 |
| `assign()` / `complete()` / `buy()` | 主要遊戲動作，都透過 `mutate()` 改狀態 → 重繪 → 存檔 |
| `meeting()` | AI 晨會：只在 Claude 頁面（`claude.use("sample")`）可用；其他地方按鈕會自動隱藏 |

## 遊戲規則

- 完成任務：+$100，派給擅長的員工（⭐）再 +$50；員工心情 < 30 時獎金 ×0.7
- 完成後員工體力 −12（擅長 −6）、心情 +8
- 同時派超過 2 件給同一人：心情 −10
- 體力每小時 +8（咖啡機 +8、沙發 +5），心情每小時 +5（盆栽 +3、販賣機 +3）
- 每完成 5 件公司升 1 級

## 想改的時候

- 改員工名字 / 職稱：改 `ROSTER`
- 加台詞：改 `LINES`
- 加商店項目：在 `SHOP` 加一筆，並在 `staticScene()` 或 `propEls()` 依 `S.upgrades.<id>` 畫出來
- 狀態格式有破壞性變更時，把 `localStorage` key 改成 `office-boss-v2`，避免讀到舊資料

## 之後的方向

- AI 晨會改串自己的 API（需要後端或 Netlify Function 保管金鑰）
- 多裝置同步（例如 Supabase / Firebase）
- 3D 版辦公室（Three.js）
