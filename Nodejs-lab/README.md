# NodeJS 實戰筆記整理

誰說前端仔就不用碰 nodeJS？
我不，我不，我不不不偏偏要碰，一切就像龍捲風來的太快。

> 這個系列記錄了我從最基礎的 AJAX + Node.js 串接，一路到 CLI 工具、影音下載與爬蟲開發的完整過程。
> 每一篇都是「實驗 + 爬坑」的真實經驗，目的是讓自己能快速掌握 Node.js 與前後端整合的能力。

---

## 🧭 目錄總覽

| 編號                                | 檔案名稱                 | 中文標題                         | 技術重點                 | 說明備註 |
| ----------------------------------- | ------------------------ | -------------------------------- | ------------------------ | -------- |
| [1](1-ajax-cambridge-dictionary.md) | 劍橋字典 AJAX 查詢（上） | fetch + Node.js 基本串接         | 初探 API + DOM 解析      |          |
| [2](2-node-cambridge-crawler.md)    | 劍橋字典 AJAX 查詢（下） | Node.js + cheerio 爬蟲           | HTML 解析與字典查詢      |          |
| [3](3-node-cambridge-update-fix.md) | 劍橋字典查詢更新與修正   | 爬蟲結構優化、POST 資料傳遞      | 修正抓不到資料的 bug     |          |
| [4](4-hahow-downloader-cli.md)      | Hahow 課程下載器 CLI     | Node CLI 工具設計 + dotenv       | 選課章節下載、自動化腳本 |          |
| [5](5-download-m3u8-ts.md)          | 下載 m3u8 TS 片段        | axios + stream 處理              | 影音分段下載             |          |
| [6](6-ptt-image-crawler.md)         | PTT 圖片爬蟲             | request + cheerio                | 批次抓取並儲存圖片       |          |
| [7](7-play-local-video.md)          | 本地影片播放             | Node.js + HTML5 video            | 測試串流與播放           |          |
| [8](8-send-data-to-html.md)         | 後端數據傳輸到前端       | express + EJS / 靜態檔案輸出     | 後端傳值 → 動態渲染      |          |
| [9](9-upload-image-api.md)          | 使用 UploadCC 上傳圖片   | FormData + axios                 | 圖片 API 上傳流程        |          |
| [10](10-login-password-sniffing.md) | 偽裝登入監聽密碼         | express 模擬登入頁               | 資安測試案例             |          |
| [11](11-receive-ajax-data.md)       | AJAX 傳值接收與處理      | GET / POST 請求解析              | express-body-parser 應用 |          |
| [12](12-node-utils-summary.md)      | Node.js 工具總結         | fs、path、async、stream、cheerio | 多篇實作中用到的工具整理 |          |

| 編號                           | 檔案名稱         | 中文標題                         | 技術重點                 | 說明備註 |
| ------------------------------ | ---------------- | -------------------------------- | ------------------------ | -------- |
| [12](12-node-utils-summary.md) | Node.js 工具總結 | fs、path、async、stream、cheerio | 多篇實作中用到的工具整理 |          |

---

## 🧠 學習心得與主軸

- **從實戰中切入，不學空架構**：每篇都是解決一個「實際目標」，不是照課本範例碼寫。
- **Node.js 不只是伺服器**：可以當 CLI 工具、爬蟲平台、下載器、甚至畫面渲染工具。
- **爬蟲與 API 是兄弟**：API 拿不到？自己抓 HTML！ cheerio 是你的雙眼。
- **從 fetch 到 stream 的遞進**：AJAX 是開端，深入後就會接觸 async、stream、pipeline、資料儲存等底層處理。
- **express 是萬用膠**：不論是 POST、前後傳值、模擬登入、資料交換，全都能靠它搞定。

---

## 💬 補充

這個 NodeJS 實戰筆記系列，是用我青春的屍體累積出來的結果。

不是學校教材、也不是 Udemy 的標準路線，不是什麼照抄 YT 課程的鳥東西。

是真實「我要做到某件事」時，一步步摸出來的流程。從一開始只是想查個字，到最後寫出一個 CLI 影片下載器，
每一篇都是進化史。

寫下來不是因為我很懂，而是我怕我會忘。
希望未來的我，多一點勇敢亂寫的動力。

**因為這並不是幹話，這是兩句幹話。**
