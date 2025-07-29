---
title: 'JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (下)'

---

# JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (下)

## 前情提要

上篇製作的單字圖片是使用puppeteer爬蟲套件去抓的，
速度狗幹慢，因此這篇全改魔改了程式碼讓爬蟲部分消失，
改用前端JS的方法fetch去得到圖片。

為了改這個費盡心思，因為fetch請求不斷fail。
最後我才突然想通是因為node.js的JS是運作伺服器的功能；
而我要fetch的code其實是要在網頁上執行。


![](https://i.imgur.com/ps67dcb.png)

所以最後的解決方法是把code寫在script.js裡面，
然後渲染網頁本身html架構的時候把script給src寫進去！
這樣就能夠執行了。

超屌的我。


## 效果圖
![](https://i.imgur.com/MCkD9du.jpg)

找了codepen的作品風格效果來套用，
圖片也新增到六張，
好看超級多，而且會隨機給圖，超紓壓的看喵咪！！

## 觀念筆記

### 專案架構
![](https://i.imgur.com/OJPUV0m.png)

## 使用圖庫

https://pikwizard.com/search/

![](https://i.imgur.com/mrHCykE.jpg)

可以fetch跨域，而且圖庫超大，有些單字都百來張。
非常推薦好用。

### 新版程式碼備份區-node.js
```javascript=
const express = require("express");
const axios = require("axios");
const { JSDOM } = require("jsdom");
const fs = require("fs");
//const Pu = require("./test.js");

const app = express();

app.get("/search/:word", async (req, res) => {
  const { word } = req.params;
  console.log(word);
  const url = `https://dictionary.cambridge.org/zht/%E8%A9%9E%E5%85%B8/%E8%8B%B1%E8%AA%9E-%E6%BC%A2%E8%AA%9E-%E7%B9%81%E9%AB%94/${word}`;

  //   await Pu(word).then((res) => {
  //     urlPngOrJpg = res;
  //   });

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
    <div class="pic"></div>
      ${targetElement.innerHTML}

      <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.3/jquery.min.js'></script>
      <script>${jsContent}</script>
      <script>
      window.onload = () => {
        try {
          MakeImgShow('${word}');
        } catch(error) {
          console.error(error);
          return 0;
        }
      };
      
    </script>
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
### 程式碼備份區-test.js (原本爬蟲用已被捨棄)
```javascript=
none
```
### 新版程式碼備份區-style.css

```Css=
/* https://codepen.io/ehsanshahbazii/pen/eYLbPjm */

body {
  max-width: 100vw;
}

html {
  box-sizing: border-box;
  background: var(--greyLight-1);
}

.pic {
  margin-top: 30px;
  text-align: center;
  margin: 0 auto;
}

.pic > * {
  margin-right: 15px;
  margin-bottom: 15px;
}

.i.i-plus.ca_hi {
  display: none;
}

.centerDiv {
  display: flex;
  justify-content: center;
}

:root {
  --primary-light: #8abdff;
  --primary: #6d5dfc;
  --primary-dark: #5b0eeb;
  --white: #ffffff;
  --greyLight-1: #e4ebf5;
  --greyLight-2: #c8d0e7;
  --greyLight-3: #bec8e4;
  --greyDark: #9baacf;
}

.searchText {
  margin-top: 30px;
  margin-bottom: 30px;
  margin-left: 180px;
  margin-right: 40px;
  width: 30rem;
  height: 4rem;
  border: none;
  border-radius: 1rem;
  font-size: 1.5rem;
  text-align: center;
  box-shadow: inset 0.2rem 0.2rem 0.5rem var(--greyDark),
    inset -0.2rem -0.2rem 0.5rem var(--white);
  background: none;
  font-family: inherit;
  color: var(--greyDark);
}

.getText {
  margin: 2rem;
  width: 5rem;
  height: 4rem;
  border-radius: 1rem;
  box-shadow: 0.3rem 0.3rem 0.6rem var(--greyLight-2),
    -0.2rem -0.2rem 0.5rem var(--white);
  justify-self: center;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: 0.3s ease;
  border: none;
  background: none;
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
  background: var(--greyLight-1);
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
  box-shadow: 0.8rem 0.5rem 1.4rem #cfd4e2, -0.04rem -0.2rem 1.8rem var(--white);
}

.ca_h {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 10px;
  background: none;
  border: none;
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
### 新版程式碼備份區-script.js (此版有bug會卡搜尋)
```javascript=
try {
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
    //MakeImgShow(Text);

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
} catch {
  console.log("Error");
}

let swiperImg = []; // 輪播圖選五張

function MakeImgShow(Text) {
  swiperImg = [];

  fetch("https://api.pikwizard.com/api/photo/search", {
    headers: {
      accept: "application/json",
      "accept-language": "zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-CN;q=0.6",
      "content-type": "application/json",
      "sec-ch-ua":
        '"Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"',
      "sec-ch-ua-mobile": "?0",
      "sec-ch-ua-platform": '"Windows"',
      "sec-fetch-dest": "empty",
      "sec-fetch-mode": "cors",
      "sec-fetch-site": "same-site",
      Referer: "https://pikwizard.com/",
      "Referrer-Policy": "strict-origin-when-cross-origin",
    },
    body: `{\"searchterm\":\"${Text}\",\"pagenum\":1,\"size\":96}`,
    method: "POST",
  })
    .then((response) => response.json())
    .then((data) => {
      let j = 0;
      let ControlNum = data.length - 13;
      let Random = Math.floor(Math.random() * ControlNum) + 1;

      if (Random < 0) {
        Random = 1;
      }
      console.log(Random);

      for (i = Random; i < Random + 7; i++) {
        let ResponseData = data[i].url_medium;
        if (ResponseData.search("http") < 0) {
          let Reimg = "https://pikwizard.com" + data[i].url_medium;
          swiperImg.push(Reimg);
        } else {
          let Reimg = data[i].url_medium;
          swiperImg.push(Reimg);
        }
      }

      console.log(swiperImg);
      for (i = 0; i < 6; i++) {
        $("<img>")
          .attr("src", swiperImg[i])
          .css("width", "400px")
          .css("height", "225px")
          .css("borderRadius", "1rem")
          .appendTo(".pic");
      }

      document.querySelectorAll("a[amp-access]").forEach(function (res) {
        res.remove();
      });

      $(".xref-title").remove();
      $(".item.lc.lc1.lpb-10.lpr-10").remove();
    })
    .catch((error) => {
      console.error(error);
    });
}


```


## 未來規劃

引入vue

輪播圖
收藏單字功能
閃卡功能
會員功能
匯出筆記功能



https://www.freecollocation.com/search?word=rate

https://www.macmillandictionary.com/collocations/british/abandon
## BUG

~~Complacent 後無法查~~


## 學校debug版本: (解決卡搜尋+使用相對路徑)

```javascript=
try {
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
    //MakeImgShow(Text);

    //相對路徑
    window.location.href = "/search/" + Text;

    document.querySelector(".searchText").value = "";
    document.querySelector(".searchText").placeholder =
      "請稍等，正在搜尋資料庫...";
  });

  html.addEventListener("keydown", (e) => {
    if (e.which == 13) {
      btn.click();
    }
  });
} catch {
  console.log("Error");
  Tryagain();
}

function Tryagain() {
  const showMoreBtn = document.querySelector(".showmore");
  const showLessBtn = document.querySelector(".showless");
  const hulU = document.querySelector(".hul-u");
  const btn = document.querySelector(".getText");
  const Input = document.querySelector(".searchText");
  const html = document.querySelector("html");
  btn.addEventListener("click", () => {
    const Text = document.querySelector(".searchText").value;
    const url = new URL("../search", location.href);
    //MakeImgShow(Text);

    window.location.href = "/search/" + Text;

    document.querySelector(".searchText").value = "";
    document.querySelector(".searchText").placeholder =
      "請稍等，正在搜尋資料庫...";
  });

  html.addEventListener("keydown", (e) => {
    if (e.which == 13) {
      btn.click();
    }
  });
}

let swiperImg = []; // 輪播圖選五張

function MakeImgShow(Text) {
  swiperImg = [];

  fetch("https://api.pikwizard.com/api/photo/search", {
    headers: {
      accept: "application/json",
      "accept-language": "zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-CN;q=0.6",
      "content-type": "application/json",
      "sec-ch-ua":
        '"Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"',
      "sec-ch-ua-mobile": "?0",
      "sec-ch-ua-platform": '"Windows"',
      "sec-fetch-dest": "empty",
      "sec-fetch-mode": "cors",
      "sec-fetch-site": "same-site",
      Referer: "https://pikwizard.com/",
      "Referrer-Policy": "strict-origin-when-cross-origin",
    },
    body: `{\"searchterm\":\"${Text}\",\"pagenum\":1,\"size\":96}`,
    method: "POST",
  })
    .then((response) => response.json())
    .then((data) => {
      let j = 0;
      let ControlNum = data.length - 13;
      let Random = Math.floor(Math.random() * ControlNum) + 1;

      if (Random < 0) {
        Random = 1;
      }
      console.log(Random);

      for (i = Random; i < Random + 7; i++) {
        let ResponseData = data[i].url_medium;
        if (ResponseData.search("http") < 0) {
          let Reimg = "https://pikwizard.com" + data[i].url_medium;
          swiperImg.push(Reimg);
        } else {
          let Reimg = data[i].url_medium;
          swiperImg.push(Reimg);
        }
      }

      console.log(swiperImg);
      for (i = 0; i < 6; i++) {
        $("<img>")
          .attr("src", swiperImg[i])
          .css("width", "400px")
          .css("height", "225px")
          .css("borderRadius", "1rem")
          .appendTo(".pic");
      }

      document.querySelectorAll("a[amp-access]").forEach(function (res) {
        res.remove();
      });

      $(".xref-title").remove();
      $(".item.lc.lc1.lpb-10.lpr-10").remove();
    })
    .catch((error) => {
      console.error(error);
    });
}

```

## 我自己早就debug好的版本:

```javascript=
// 获取 ".showmore" 和 "hul-u" 元素
const showMoreBtn = document.querySelector(".showmore");
const showLessBtn = document.querySelector(".showless");
const hulU = document.querySelector(".hul-u");
const btn = document.querySelector(".getText");
const Input = document.querySelector(".searchText");
const html = document.querySelector("html");

try {
  // 给 ".showmore" 元素添加点击事件监听器
  showMoreBtn.addEventListener("click", function () {
    // 显示 "hul-u" 元素
    hulU.style.display = "block";
    // 隐藏 ".showmore" 元素并显示 ".showless" 元素
    showMoreBtn.style.display = "none";
    showLessBtn.style.display = "block";
  });
} catch {
  console.log("Error");
}

try {
  // 给 ".showless" 元素添加点击事件监听器
  showLessBtn.addEventListener("click", function () {
    // 隐藏 "hul-u" 元素
    hulU.style.display = "none";
    // 隐藏 ".showless" 元素并显示 ".showmore" 元素
    showLessBtn.style.display = "none";
    showMoreBtn.style.display = "block";
  });
} catch {
  console.log("Error");
}

btn.addEventListener("click", () => {
  const Text = document.querySelector(".searchText").value;
  const url = new URL("../search", location.href);
  //MakeImgShow(Text);

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

let swiperImg = []; // 輪播圖選五張
function MakeImgShow(Text) {
  swiperImg = [];

  fetch("https://api.pikwizard.com/api/photo/search", {
    headers: {
      accept: "application/json",
      "accept-language": "zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-CN;q=0.6",
      "content-type": "application/json",
      "sec-ch-ua":
        '"Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"',
      "sec-ch-ua-mobile": "?0",
      "sec-ch-ua-platform": '"Windows"',
      "sec-fetch-dest": "empty",
      "sec-fetch-mode": "cors",
      "sec-fetch-site": "same-site",
      Referer: "https://pikwizard.com/",
      "Referrer-Policy": "strict-origin-when-cross-origin",
    },
    body: `{\"searchterm\":\"${Text}\",\"pagenum\":1,\"size\":96}`,
    method: "POST",
  })
    .then((response) => response.json())
    .then((data) => {
      let j = 0;
      let ControlNum = data.length - 13;
      let Random = Math.floor(Math.random() * ControlNum) + 1;

      if (Random < 0) {
        Random = 1;
      }
      console.log(Random);

      for (i = Random; i < Random + 7; i++) {
        let ResponseData = data[i].url_medium;
        if (ResponseData.search("http") < 0) {
          let Reimg = "https://pikwizard.com" + data[i].url_medium;
          swiperImg.push(Reimg);
        } else {
          let Reimg = data[i].url_medium;
          swiperImg.push(Reimg);
        }
      }

      console.log(swiperImg);
      for (i = 0; i < 6; i++) {
        $("<img>")
          .attr("src", swiperImg[i])
          .css("width", "400px")
          .css("height", "225px")
          .css("borderRadius", "1rem")
          .appendTo(".pic");
      }

      document.querySelectorAll("a[amp-access]").forEach(function (res) {
        res.remove();
      });

      $(".xref-title").remove();
      $(".item.lc.lc1.lpb-10.lpr-10").remove();
    })
    .catch((error) => {
      console.error(error);
    });
}

```