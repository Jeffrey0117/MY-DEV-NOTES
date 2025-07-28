# JS 筆記-如何用 GitHub + jsDelivr 託管 JS 檔案並在外部使用（包含實戰驗證）

## 前情提要

什麼時候會需要託管 JS 代碼？jsnop 以及 XSS 攻擊的時候！

這時候需要找到資源來幫忙把 JS 內容上傳，以及調用，研究了一陣子，終於了解怎麼操作。

這樣 XSS 要注入的時候調用起來才方便，可以把程式碼縮到一行，裡面執行一大狗票髒東西 X

jsnop 則是解決跨域問題，這篇文章的技巧實在太重要。

## 學習資源

[免费 CDN：jsDelivr+Github 使用方法](https://zhuanlan.zhihu.com/p/76951130)

## 注意事項

如果直接在 github 上檔案點 raw 那個網址，是不能調用的哦。
舉例：
https://raw.githubusercontent.com/vi000246/RescueTimeRealTime/master/RescueTimeRealTime/index.js

以此可知https://raw.githubusercontent.com/
這種開頭的託管不成立！

## 操作方法

上傳到 github 之後，最重要的就是 release！

![image](https://hackmd.io/_uploads/S1a-uyIhp.png)

接下來在自己 js 的檔案網址前面加上

https://cdn.jsdelivr.net/gh/Jeffrey0117/倉庫名/檔案名.js

這個 url 就可以放在 script 元素裡面調用。

## 實際測試

在某個網頁中試試看調用
![image](https://hackmd.io/_uploads/HkIhdJI3T.png)

console 面板呼叫函數看有沒有辦法使用到：
![image](https://hackmd.io/_uploads/H1foO1L2a.png)

成功！證明已經託管成功囉！

https://cdn.jsdelivr.net/gh/Jeffrey0117/test/Todo%20list.js
這個我不會刪除，應該看一下這連結可不可以用，就知道能否託管。

## 網站的介紹與 GPT 的講解

![image](https://hackmd.io/_uploads/B13o6WMha.png)

https://www.jsdelivr.com/?docs=gh

將 JavaScript 代碼託管到 jsDelivr 是一種常見的方法，它可以幫助加速您的網站載入速度，提高性能。以下是將 JavaScript 代碼託管到 jsDelivr 的基本步驟：

1. **在 jsDelivr 上創建帳戶（可選）：** 首先，您可以選擇在 jsDelivr 上創建帳戶，這樣您就能夠更好地管理和追踪您的項目。註冊一個帳戶是免費的。

2. **準備您的 JavaScript 代碼：** 確保您的 JavaScript 代碼是完整和可運行的。確保您的代碼沒有錯誤並且可以在瀏覽器中正確執行。

3. **在 jsDelivr 上託管文件：** 在 jsDelivr 上託管文件的方式有兩種：使用 GitHub、npm、或直接上傳文件。

   - **使用 GitHub：** 如果您的代碼存儲在 GitHub 上，您可以使用 GitHub 的 URL 進行託管。例如，如果您的 GitHub 項目是https://github.com/yourusername/yourrepo，那麼您可以使用以下URL：

     ```
     https://cdn.jsdelivr.net/gh/yourusername/yourrepo/file.js
     ```

   - **使用 npm：** 如果您的代碼是通過 npm 包管理器發布的，您可以使用以下 URL：

     ```
     https://cdn.jsdelivr.net/npm/package/file.js
     ```

   - **直接上傳：** 您還可以直接將文件上傳到 jsDelivr，然後獲取該文件的 URL。

4. **使用 jsDelivr 的 URL：** 將上面獲得的 jsDelivr 的 URL 嵌入到您的網站代碼中，替換您本地載入 JavaScript 的 URL。例如，將以下代碼插入到您的 HTML 文件中：

   ```html
   <script src="https://cdn.jsdelivr.net/..."></script>
   ```

5. **測試和部署：** 測試您的網站，確保 jsDelivr 正確載入並執行您的 JavaScript 代碼。然後，將您的網站部署到您的主機或網站托管服務中。

這樣，您的 JavaScript 代碼就成功託管在 jsDelivr 上了，並可以享受到它提供的快速和穩定的全球 CDN 服務。請注意，jsDelivr 有詳細的文檔和使用指南，您可以參考它們以獲得更多詳細信息和高級配置選項。
