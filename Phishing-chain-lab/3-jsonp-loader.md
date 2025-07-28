# JSONP 學習筆記-觀念整理與實例運用

## 前情提要

![image](https://hackmd.io/_uploads/B1Qidp22a.png)

先備知識：發送請求、跨域。
JSONP 全名是 JSON with padding。
img link iframe script 這些標籤本身就可以跨域，透過這個特性，
聰明的人就想到有這個方法可以玩。
它的原理是透過 script 標籤，設置回調函數。

## 影片講解

<iframe width="560" height="315" src="https://www.youtube.com/embed/wK9flZOu1dM?si=cPGjhrqh-zYKHBeX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

[IT 幫文章-解釋 JSONP 如何運作](https://ithelp.ithome.com.tw/articles/10267740?sc=rss.iron)

[JSONP 維基百科說明](https://zh.wikipedia.org/zh-tw/JSONP)

[用 JSONP 跨域 GET 簡易示範 ＆ 說明](https://medium.com/@brianwu1201/jsonp-with-simple-example-4711e2a07443)

[JSONP 跨域原理及实现](https://segmentfault.com/a/1190000041946934)
這篇寫的超級詳細，從同源開始講解，到最後使用 JSONP 的安全問題都探討到了。

[JSONP (JSON with Padding)](https://www.fooish.com/json/jsonp.html)

## 思路整理

總之也就是說透過 script 發送一個請求，但這個做法有兩個限制：
第一個是只能 GET 請求，第二個是返回的數據只能用函數去呈現，接受就要本地寫接口。

也就是說 JSONP 發送的請求必須是丟到動態網站(有後端、後台)，
而且這個後端本身也是我們自己的程式，才能去寫返回的形式。

舉例如圖：
![image](https://hackmd.io/_uploads/rJn2xRnnp.png)

那麼我原本想像中的，透過 script 標籤發送請求到靜態網站，加載 JS 程式碼，這個就不太成立。
這僅僅只是 XSS 注入中的一種靈活運用罷了。
這意味著，兩種事情的本質是有差別的，但不代表 XSS 跟 JSONP 沒有辦法結合。

就這樣想像好了 XSS 的話，就等於把別人的網站變成你自己的！
那麼現在你擁有了一個自己的網站，你想要幹嘛都可以，當然你網站原本有 JSONP 的需求，
也就自然可以辦到了。

## 重點說明

也就是說，今天如果你需要在你的網站上 get 到一些資料，
這個資料是你自己(無論是本機或伺服器上)的，那麼就適合在你的 index.html 寫 JSONP，
這樣一來有辦法 AJAX GET 到自己架設網站上的資料。

那麼換句話說，如果自己本身沒有資料的話，那你要在後端的部分先處理好，
把資料都找到拿到了以後，在 index.html 使用 JSONP 的技術就可以叫到別人的資料了！

我把原本進行的流程(X)跟使用聰明手段(O)的流程記下來比較：

A.你寫了一個前端網頁-->需要 ajax 某個資料-->遇到跨域問題-->失敗
B.你寫了一個前端網頁-->需要 ajax 某個資料-->使用 JSONP 技術-->叫到自己後端伺服器-->
後端伺服器把處理好的資料，以唯一合法格式"函數"回傳-->成功拿到資料

但這樣整理下來我想了一下，為什麼沒有第三種？

一開始網頁就是後端渲染：

C.你寫了一個後端網頁-->直接 axios 資料-->不會有跨域問題-->拿到資料後渲染。

## JSONP 的漏洞

千锋 Web 前端教程：101 跨域和 JSONP

<iframe width="560" height="315" src="https://www.youtube.com/embed/HluGTvS5mpM?si=XwFblwXVc3BwGlo5" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

從同源的源是什麼意思開始講解，還算滿清晰的。

當然可以，以下是你筆記中「效果」與「實戰走起」兩段的補完內容：

---

## 效果

簡單來說，當我們透過 `<script>` 標籤載入一個跨域的 JS 檔案時，這個 JS 檔案實際上就是一段函式呼叫（如：`callback({資料})`）。
而這個 `callback` 是我們事先定義在本地端的函式名稱。這樣一來，資料就能在載入後立即進入我們的程式邏輯中處理。

例如我們預先定義好：

```js
function handleData(data) {
  console.log("成功取得資料：", data);
}
```

然後載入的 script 是這樣：

```html
<script src="https://example.com/api?callback=handleData"></script>
```

而這個遠端 API 回傳的內容會是：

```js
handleData({ name: "小明", age: 18 });
```

這樣就完成了一次跨域的資料獲取，而且是合法且立即可執行的方式。

---

## 實戰走起

以下是一個最基本的 JSONP 實作範例：

### 1️⃣ 建立前端 HTML（index.html）

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>JSONP 範例</title>
  </head>
  <body>
    <h1>JSONP Demo</h1>
    <button onclick="getData()">取得資料</button>

    <script>
      function handleResponse(data) {
        alert("取得資料成功：" + JSON.stringify(data));
      }

      function getData() {
        const script = document.createElement("script");
        script.src = "http://localhost:3000/data?callback=handleResponse";
        document.body.appendChild(script);
      }
    </script>
  </body>
</html>
```

### 2️⃣ 建立 Node.js 模擬後端（server.js）

```js
const express = require("express");
const app = express();

app.get("/data", (req, res) => {
  const callback = req.query.callback;
  const data = { message: "Hello from JSONP", time: new Date().toISOString() };
  res.type("text/javascript");
  res.send(`${callback}(${JSON.stringify(data)})`);
});

app.listen(3000, () => {
  console.log("JSONP Server running on http://localhost:3000");
});
```

### ✅ 測試步驟

1. 先啟動 Node.js 後端：`node server.js`
2. 開啟 `index.html`，點擊按鈕後，應該會跳出 alert 顯示後端回傳的資料。

---

這樣你就完成了一個**完整的 JSONP 跨域資料傳遞實作**，不需要 CORS、不需要 proxy，中規中矩但很聰明的老派做法！
