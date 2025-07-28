---
title: JS的API筆記-reurl.cc的API串接(縮網址)

---

# JS的API筆記-reurl.cc的API串接(縮網址)

## 前情提要
這篇筆記是針對台灣 reurl.cc 的短網址 API 實作教學，涵蓋後端（Node.js + axios）、前端（jQuery + fetch）三種方式。實際串接過程中，經過觀察 DevTool 的請求格式、header 結構與 API 文件對照，成功重現了官方頁面產生短網址的過程，並理解了需要 API key 與免 key 的使用差異。

透過這個過程，我也練習了如何用實際工具（DevTool、fetch、Postman）拆解別人網站的 AJAX 呼叫邏輯，再一步步用程式碼還原，從而建立對 Web API 串接更深刻的掌握。

## 串reurl.cc的API

[reurl網站點這](https://reurl.cc/info/api/tw)


![](https://i.imgur.com/effle2h.png)

## CRUD的格式說明
![](https://i.imgur.com/pmISXyy.png)
![](https://i.imgur.com/UuOxUc1.png)


程式碼就照著寫，對應上面的name值。

## 後端node.js完成檔


`npm install -g axios`


```javascript=

const axios = require("axios");
const readline = require("readline");

const apiUrl = "https://api.reurl.cc/shorten";
const apiKey =
  "4070ff49d794e13d12523b663c974755ecd1b531969e04df8a38b58d65165567c4f5d6";

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

rl.question("請輸入長網址：", (longUrl) => {
  const requestBody = {
    url: longUrl,
  };

  const config = {
    headers: {
      "Content-Type": "application/json",
      "reurl-api-key": apiKey,
    },
  };

  axios
    .post(apiUrl, requestBody, config)
    .then((response) => {
      const { res, msg, short_url } = response.data;
      if (res === "success") {
        console.log(`縮網址：${short_url}`);
      } else {
        console.error(`縮網址 API 回傳錯誤：${msg}`);
      }
    })
    .catch((error) => {
      console.error(`錯誤：${error}`);
    })
    .finally(() => {
      rl.close();
    });
});

```

## 前端版本使用APIkey (經過修正後可成功)

```javascript=
const apiUrl = "https://api.reurl.cc/shorten";
const apiKey =
  "4070ff49d794e13d12523b663c974755ecd1b531969e04df8a38b58d65165567c4f5d6";


    const longUrl = "https://github.com/asd8116/URL-shortener";

    const requestBody = {
      url: longUrl,
    };

    const config = {
      headers: {
        "Content-Type": "application/json",
        "reurl-api-key": apiKey,
      },
    };
$.post({
    url: apiUrl,
    data: JSON.stringify(requestBody),
    headers: {
        "Content-Type": "application/json",
        "reurl-api-key": apiKey,
    },
    dataType: 'json'
}).done((response) => {
        const { res, msg, short_url } = response;
        if (res === "success") {
          console.log(`縮網址：${short_url}`);
        } else {
          console.log(`縮網址 API 回傳錯誤：${msg}`);
        }
      })
      .fail((jqXHR, textStatus, errorThrown) => {
        console.log(`錯誤：${errorThrown}`);
      });
```

## 前端fetch版本(不使用APIkey)

在`https://reurl.cc/main/tw`裡執行

```javascript=
async function postJSON(data) {
  try {
    const response = await fetch("https://reurl.cc/webapi/shorten/v2", {
      headers: {
        accept: "application/json, text/plain, */*",
        "accept-language": "zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-CN;q=0.6",
        "content-type": "application/json;charset=UTF-8",
        "sec-ch-ua":
          '"Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Windows"',
        "sec-fetch-dest": "empty",
        "sec-fetch-mode": "cors",
        "sec-fetch-site": "same-origin",
      },
      referrer: "https://reurl.cc/main/tw",
      referrerPolicy: "strict-origin-when-cross-origin",
      body: JSON.stringify(data),
      method: "POST",
      mode: "cors",
      credentials: "include",
    });
    const result = await response.json();
    console.log("你的縮網址是:https://reurl.cc/" + result.url);
  } catch (error) {
    console.error("Error:", error);
  }
}
const data = { url: "要縮的長網址丟這邊" };
postJSON(data);


```

[MDN-fetch寫法教學](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#uploading_json_data)


## 從debuu devtool來研究AJAX

<iframe width="560" height="315" src="https://www.youtube.com/embed/9JzQPrGaMhw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## 延伸筆記


### 資料壹
![](https://i.imgur.com/rOR3fZZ.png)

[github-URL-shortener](https://github.com/asd8116/URL-shortener)

### 資料貳
![](https://i.imgur.com/UnxlGo8.png)

[使用 GitHub Pages 打造簡易短網址系統](https://blog.poychang.net/how-to-use-github-pages-build-a-short-url-app/)


## 心得
這次練習讓我徹底體會到：「API 串接不是照文件貼就好」，尤其是遇到文件不完整或行為跟預期不同的情況時，懂得打開 DevTool、分析 header 與 payload 格式，才是關鍵。

一開始照文件試了好幾次都報錯，直到我直接模仿官方網站的 AJAX 行為，才發現它其實有兩套 API：一套需要 API key（適合後端用），一套是內部網頁用的（其實不公開，但可以抓來用）。這種「逆向模仿」讓我對 fetch、header、CORS、request body 結構都有更清楚的理解。

總結一句話：
不是看懂 API 文件就會用，是能「駭進去模仿原站」才是真的會。
這種能力，才是未來應付陌生 API、快速開發自動化工具的關鍵武器。