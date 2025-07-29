---
title: 'JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (上)'

---

# JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (上)

## 前情提要

原本練習打ajax弄劍橋字典，做一做就不知道為什麼架起sever，
還寫出一個後端爬蟲去抓圖片XDD
只是爬蟲導致效果非常慢！
這部分都可以慢慢改善，從一個基底開始整修。

## 效果圖
![](https://i.imgur.com/otH0QQR.png)

超酷的呢。

## 觀念筆記

### 專案架構

![](https://i.imgur.com/B39jTrB.png)

### 程式碼備份區-node.js
```javascript=
const express = require("express");
const axios = require("axios");
const { JSDOM } = require("jsdom");
const fs = require("fs");
const Pu = require("./test.js");

const app = express();
let urlPngOrJpg;
app.get("/search/:word", async (req, res) => {
  const { word } = req.params;
  const url = `https://dictionary.cambridge.org/zht/%E8%A9%9E%E5%85%B8/%E8%8B%B1%E8%AA%9E-%E6%BC%A2%E8%AA%9E-%E7%B9%81%E9%AB%94/${word}`;
  await Pu(word).then((res) => {
    urlPngOrJpg = res;
  });

  try {
    // 读取CSS文件内容
    const cssContent = fs.readFileSync("./style.css", "utf8");

    // 读取JS文件内容
    const jsContent = fs.readFileSync("./script.js", "utf8");
    const { data } = await axios.get(url);
    const dom = new JSDOM(data);
    const targetElement = dom.window.document.querySelector(".sense-body");

    // 将HTML、CSS和JS内容合并并发送回浏览器
    const content = `
  <html>
    <head>
      <style>${cssContent}</style>
    </head>
    <body>
    <div class="centerDiv">
    <input class="searchText" type="text" placeholder="輸入要搜尋的英單">
    <button class="getText">查詢</button>
    </div>
      ${targetElement.innerHTML}
      <img src="${urlPngOrJpg}" alt="">
      <script>${jsContent}</script>
    </body>
  </html>
`;

    res.send(content);
  } catch (error) {
    console.error(error);
    res.status(500).send("單字是否有拚錯？沒有找到這個單字唷！");
  }
});

app.listen(3000, () => {
  console.log("http://localhost:3000/search/dog");
});

```
### 程式碼備份區-test.js
```javascript=
const puppeteer = require("puppeteer");

async function Pu(word) {
  try {
    const browser = await puppeteer.launch();
    const page = await browser.newPage();
    await page.goto(`https://stocksnap.io/search/${word}`);
    await page.waitForSelector("img");

    const myElements = await page.$$eval("img", (elements) => {
      return elements.map((el) => el.src);
    });

    const regex = /\.(jpg|png)$/i;
    const urlPngOrJpg = myElements.find((urlPngOrJpg) =>
      regex.test(urlPngOrJpg)
    );
    //console.log(urlPngOrJpg);
    return urlPngOrJpg;
    await browser.close();
  } catch {
    return false;
  }
}

module.exports = Pu;

```
### 程式碼備份區-style.css

```Css=
body {
  max-width: 100vw;
}

.i.i-plus.ca_hi {
  display: none;
}

.centerDiv {
  display: flex;
  justify-content: center;
}

.searchText {
  text-align: center;
  border: 2px solid orange;
  font-size: 17px;
}

.def-block {
  margin-bottom: 20px;
}

.ddef_block {
  border-left: 5px solid #36a5e6;
  padding-left: 10px;
}

.dwl {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.dwla {
  color: #fff;
  background-color: #36a5e6;
  padding: 5px 10px;
  border-radius: 3px;
  text-decoration: none;
}

.dwla:hover {
  background-color: #136fbf;
}

.dwla i {
  display: inline-block;
  margin-right: 5px;
}

.ddef_h {
  font-size: 16px;
  margin-bottom: 10px;
}

.def-info {
  color: #aaa;
  font-size: 14px;
  margin-right: 10px;
}

.epp-xref {
  background-color: #aaa;
  border-radius: 3px;
  color: #fff;
  font-size: 12px;
  padding: 2px 5px;
}

.def {
  font-weight: bold;
  margin-bottom: 10px;
}

.query {
  color: #36a5e6;
  text-decoration: none;
}

.query:hover {
  text-decoration: underline;
}

.ddef_b {
  margin-top: 10px;
}

.trans {
  color: #555;
  font-size: 16px;
}

section[expanded] {
  background-color: #f2f2f2;
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
}

.ca_h {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 10px;
  background-color: #e6e6e6;
  border-radius: 5px;
  cursor: pointer;
}

.i-plus::before {
  content: "+";
  font-size: 20px;
}

.ca_hi {
  margin-right: 10px;
}

.showmore,
.showless {
  font-size: 14px;
  font-weight: bold;
}

.showmore:hover,
.showless:hover {
  text-decoration: underline;
}

.showless {
  display: none;
}

.daccord_h:not([expanded]) ~ .daccord_b {
  display: none;
}

.hul-u {
  padding: 0;
  margin: 0;
  list-style: none;
}

.hul-u0 li {
  padding: 5px 10px;
  border-bottom: 1px solid #ccc;
}

.highlighter {
  background-color: yellow;
  font-weight: bold;
}

.query {
  text-decoration: none;
  border-bottom: 1px dotted blue;
}

.query:hover {
  background-color: #f2f2f2;
}

.examp {
  margin-top: 10px;
}

.eg {
  font-style: italic;
}

```
### 程式碼備份區-script.js
```javascript=
// 获取 ".showmore" 和 "hul-u" 元素
const showMoreBtn = document.querySelector(".showmore");
const showLessBtn = document.querySelector(".showless");
const hulU = document.querySelector(".hul-u");
const btn = document.querySelector(".getText");
const Input = document.querySelector(".searchText");
const html = document.querySelector("html");

// 给 ".showmore" 元素添加点击事件监听器
showMoreBtn.addEventListener("click", function () {
  // 显示 "hul-u" 元素
  hulU.style.display = "block";
  // 隐藏 ".showmore" 元素并显示 ".showless" 元素
  showMoreBtn.style.display = "none";
  showLessBtn.style.display = "block";
});

// 给 ".showless" 元素添加点击事件监听器
showLessBtn.addEventListener("click", function () {
  // 隐藏 "hul-u" 元素
  hulU.style.display = "none";
  // 隐藏 ".showless" 元素并显示 ".showmore" 元素
  showLessBtn.style.display = "none";
  showMoreBtn.style.display = "block";
});

btn.addEventListener("click", () => {
  const Text = document.querySelector(".searchText").value;
  const url = new URL("../search", location.href);
  if (url.hostname.search("local") > 0 || url.hostname.search("local") == 0) {
    window.location.href = "http://" + url.hostname + ":3000/search/" + Text;
  } else {
    window.location.href = "http://" + url.hostname + "/search/" + Text;
  }
  document.querySelector(".searchText").value = "";
  document.querySelector(".searchText").placeholder =
    "請稍等，正在搜尋資料庫...";
});

html.addEventListener("keydown", (e) => {
  if (e.which == 13) {
    btn.click();
  }
});

window.onload = () => {
  document.querySelectorAll("a[amp-access]").forEach(function (res) {
    res.remove();
  });
};

```


### 對網址做parse : hostname
```

const url = new URL("../cats", "http://www.example.com/dogs");
console.log(url.hostname); // "www.example.com"
console.log(url.pathname); // "/cats"
```

[MDN的教學](https://developer.mozilla.org/en-US/docs/Web/API/URL)

### ngrok 讓外網連線

![](https://i.imgur.com/IUQc8o2.png)
[社團討論在這邊](https://www.facebook.com/groups/node.js.tw/posts/3811821638843938/)

![](https://i.imgur.com/OzqHNMP.png)
[Ngrok - 把你的Demo推上網路世界的好幫手！](https://hsueh-jen.gitbooks.io/webcrawler/content/lesson-2-nodejs/fen-xiang-ni-de-wang-zhan.html)

### 下載頁面



[要下載的來官網這邊點](https://ngrok.com/download)

![](https://i.imgur.com/bPSTfQd.png)

![](https://i.imgur.com/hGvVJDe.png)

### 必須先創建帳號(可用google連接)  & 拿到APItoken
![](https://i.imgur.com/hWKAsrL.png)

[拿API的設定頁面](https://dashboard.ngrok.com/get-started/setup)


驗證後就可以使用了。

手機也能連線 (可以用縮網址給別人XD)

![](https://i.imgur.com/i2zgo73.jpg)


### 其實有npm可以用 - ngrok npm 

![](https://i.imgur.com/DyRr0LW.png)
[一周下載量18萬的好物](https://www.npmjs.com/package/ngrok)


![](https://i.imgur.com/j9obaW9.png)

[在本地端使用 Nodejs & Ngrok 進行 Line Bot 開發測試](https://codefun.tw/2017/2017060201-nodejs-line-bot-p1.html)


## 瀏覽器莫名沒畫面　手機ＯＫ才發現

好像是分頁太多沒記憶體，害我一直以為網站壞掉了。
用手機測試才發現其實網站還OK。
還以為有鬼XD




## 技術含量回顧分析

### 目前已有技術
html+CSS+JS
AJAX
模板語言 node.js express axios 爬蟲
ngrok設定外網連線

### 未來新增技術
swiper套件使用  埋類GA的觀察代碼
匯出匯入(資料庫概念引入)
功能新增:筆記單字 會員登入 等等
頁面修改(?

