# JS API 實戰筆記整理

> 本系列是我在學習 JavaScript 操作 API 過程中的紀錄與實驗心得。
> 很多 API 技術中文資源不多，甚至要付費才看得到，因此我選擇用實際的例子，一篇篇打通，留下完整筆記與踩雷心得。
> 希望這些記錄能幫助像我一樣的初學者，少走彎路，也能學會真正「與網站對話」的能力。

---

## 🧭 目錄總覽

| 編號                       | 檔案名稱                     | 中文標題                          | 技術重點                     | 說明備註 |
| -------------------------- | ---------------------------- | --------------------------------- | ---------------------------- | -------- |
| [0](0-api-core-concept.md) | API 核心觀念整理             | GET / POST / headers / CORS 基礎  | 建議先讀本篇再實作           |
| [1](1-imgur-upload.md)     | 上傳圖片到 imgur             | 使用 `FormData` + fetch           | multipart/form-data 格式處理 |
| [2](2-uploadcc.md)         | 利用 upload.cc 上傳圖片      | DevTools 逆向分析 + POST 上傳     | 多圖上傳、跨域限制           |
| [3](3-meteor-upload.md)    | 發送米特站內信 API           | POST JSON 資料、授權 headers 設定 | Cookie / Authorization 搭配  |
| [4](4-qrcode.md)           | 建立 QRCode 產生器           | Google Chart API + 動態插入 img   | URL 參數操作                 |
| [5](5-reurl.md)            | reurl.cc 縮網址 API 串接     | POST JSON 封包                    | 傳統短網址服務               |
| [6](6-googlemap.md)        | 串接 Google Map 地圖         | script 標籤引入 + Marker 標記     | Google Maps JS SDK           |
| [7](7-fetch-message.md)    | 用 fetch 發送訊息            | 自訂 JSON payload + 錯誤處理      | fetch 基礎應用               |
| [8](8-node-uploadcc.md)    | 用 node 上傳圖片到 upload.cc | node-fetch + fs 搭配 FormData     | 非瀏覽器環境處理 multipart   |

---

## 🧠 學習心得與主軸

- **從 DevTool 開始觀察**：透過觀察現有網站的行為，學會如何反向還原 API。
- **會抄是一種能力**：右鍵 `Copy as fetch` 不是作弊，是學習最快的捷徑。
- **資料格式 ≠ 想像**：API 的格式比想像中更嚴謹，空格或順序錯了都可能導致失敗。
- **畫面是假的，API 是真的**：前端 UI 是用來給人看的，API 才是跟伺服器對話的核心。

---

## 💬 補充

這些筆記不只是技巧的記錄，更是一種反覆試錯、練出 API 感覺的歷程。
不管是縮網址、上傳圖片、還是打聊天室訊息，其實底層概念是通的：
**request + header + body → response + error handling**
這個流程理解了，API 就不再神秘。

雖然時間已久很多 API 已經換了死了，但概念永遠活著。

給未來的我：拿去學吧！
