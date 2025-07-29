---
title: node.js筆記-爬蟲測試(抓PTT圖片)

---

# node.js筆記-爬蟲測試(抓PTT圖片)

## 前情提要

叫GPT教我用node.js寫爬蟲，跟語言模型學語言(◍•ᴗ•◍)

## 範例網站

`https://www.example.com/`
![](https://i.imgur.com/J1y9CzY.png)

## 程式碼
```
const request = require('request');
const cheerio = require('cheerio');

const url = 'https://www.example.com';

request(url, (error, response, body) => {
  if (!error && response.statusCode === 200) {
    const $ = cheerio.load(body);
    const title = $('title').text();
    console.log(`Title: ${title}`);
  }
});
```
這個示範程式會向`https://www.example.com`發送請求，並使用cheerio模組將HTML代碼轉換為DOM樹，然後使用jQuery風格的語法選擇title標籤，並打印出標題的文字。

當然，這僅僅是一個簡單的示範。實際的爬蟲需要更複雜的邏輯，例如分析網站的結構、使用正則表達式匹配需要的數據、處理動態生成的內容等等。但是這個示範應該可以讓你對Node.js爬蟲的基本操作有一個初步的了解。





## 觀念筆記

### cheerio是幹嘛的 

Cheerio是一個類似jQuery的Node.js模組，它能夠解析HTML文檔，並提供類似jQuery的API來遍歷、操作和修改文檔中的元素。

![](https://i.imgur.com/MUhcF19.png)

### 英文單字的Cheerio

![](https://i.imgur.com/XboowZ4.png)
![](https://i.imgur.com/I4SdRyH.png)

道別的意思。

## PTT爬蟲程式測試一 (針對單篇文章抓圖)
![](https://i.imgur.com/ZeJP53h.png)


```
const cheerio = require("cheerio");
const request = require("request");
const https = require("https");
const url = require("url");
const fs = require("fs");

//const imageUrl = "https://i.imgur.com/OYa0AuY.jpg";
const dir = "./image"; // 儲存圖片的資料夾路徑
const cookies = {
  over18: "1",
};

const options = {
  url: "https://www.ptt.cc/bbs/Beauty/M.1681949436.A.350.html",
  headers: {
    Cookie: Object.entries(cookies)
      .map(([key, value]) => `${key}=${value}`)
      .join("; "),
  },
};
request(options, (error, response, html) => {
  if (!error && response.statusCode == 200) {
    const $ = cheerio.load(html);
    $("a").each((i, el) => {
      //const title = $(el).text();
      const url = $(el).attr("href");
      console.log(url);
      download(url);
    });
  }
});

let item = 0;

function download(imageUrl) {
  x = url.parse(imageUrl).pathname.split(".").pop();
  if (x === "jpg" || x === "png") {
    https
      .get(imageUrl, (res) => {
        let chunks = [];

        res.on("data", (chunk) => {
          chunks.push(chunk);
        });

        res.on("end", () => {
          const imageData = Buffer.concat(chunks);

          // 获取文件后缀名
          const extension = url.parse(imageUrl).pathname.split(".").pop();

          // 如果后缀名为jpg或png，则保存图片
          if (extension === "jpg" || extension === "png") {
            // 建立資料夾
            fs.mkdir(dir, { recursive: true }, (err) => {
              if (err) throw err;

              // 儲存圖片到指定資料夾
              fs.writeFile(
                `${dir}/image${item}.${extension}`,
                imageData,
                (err) => {
                  if (err) throw err;
                  console.log("Image saved successfully.");
                }
              );
              item++;
            });
          } else {
            console.log("Image format not supported.");
          }
        });
      })
      .on("error", (err) => {
        console.log("Error:", err.message);
      });
  }
}

```

## PTT爬蟲程式測試二 (針對多篇文章抓圖)

```
const cheerio = require("cheerio");
const request = require("request");
const https = require("https");
const url = require("url");
const fs = require("fs");

const cookies = {
  over18: "1",
};

const options = {
  url: "https://www.ptt.cc/bbs/Beauty/index4003.html",
  headers: {
    Cookie: Object.entries(cookies)
      .map(([key, value]) => `${key}=${value}`)
      .join("; "),
  },
};
request(options, (error, response, html) => {
  if (!error && response.statusCode == 200) {
    const $ = cheerio.load(html);
    $("a").each((i, el) => {
      //const title = $(el).text();
      const url = $(el).attr("href");
      if (url.search("thread") > 0) {
        NewUrl = "https://www.ptt.cc/" + url;
        console.log(NewUrl);
      }
      //download(url);
    });
  }
});

let item = 0;

function download(imageUrl) {
  x = url.parse(imageUrl).pathname.split(".").pop();
  if (x === "jpg" || x === "png") {
    https
      .get(imageUrl, (res) => {
        let chunks = [];

        res.on("data", (chunk) => {
          chunks.push(chunk);
        });

        res.on("end", () => {
          const imageData = Buffer.concat(chunks);

          // 获取文件后缀名
          const extension = url.parse(imageUrl).pathname.split(".").pop();

          // 如果后缀名为jpg或png，则保存图片
          if (extension === "jpg" || extension === "png") {
            fs.writeFile("image" + item + "." + extension, imageData, (err) => {
              if (err) throw err;
              console.log("Image saved successfully.");
              item++;
            });
          } else {
            console.log("Image format not supported.");
          }
        });
      })
      .on("error", (err) => {
        console.log("Error:", err.message);
      });
  }
}

```