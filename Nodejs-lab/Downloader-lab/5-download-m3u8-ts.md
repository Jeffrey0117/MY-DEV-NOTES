---
title: node.js筆記-下載m3u8裡的ts檔案

---

# node.js筆記-下載m3u8裡的ts檔案

## 前情提要

有些網站的影片播放是透過 HLS（HTTP Live Streaming）技術來實現的，這類影片不是直接一個 .mp4 檔，而是以 .m3u8 播放清單搭配一堆 .ts 小片段來串流播放。

簡單來說：

.m3u8 就像播放清單（playlist），裡面記錄了很多 .ts 檔案的路徑。

.ts 是實際的影片片段，把這些片段按順序下載回來並串接起來，就等於整部影片。

這篇筆記的重點，就是用 Node.js 幫我們「自動化下載 .m3u8 裡的所有 .ts 檔案」，以便之後可以進一步合併、處理，甚至製作下載器。

這是實作資源爬蟲、後端影音處理的基礎步驟之一，也是進攻 HLS 協議解析的第一步。

## 效果
![](https://hackmd.io/_uploads/r1BjWgxBn.png)



## 專案資料夾

首先要先安裝axios套件
然後放置一個videos的資料夾放置ts檔案

## 程式碼

```
const fs = require("fs");
const axios = require("axios");
const { parse: parseUrl } = require("url");
const { join: joinPath } = require("path");
const { pipeline } = require("stream");
const { promisify } = require("util");

const pipelineAsync = promisify(pipeline);

// 下載檔案
async function downloadFile(url, path) {
  const response = await axios({
    url,
    method: "GET",
    responseType: "stream",
  });

  // 使用 pipeline 來實現資料流的下載
  await pipelineAsync(response.data, fs.createWriteStream(path));
}

// 解析 m3u8 檔案，並下載 ts 檔案
async function downloadM3u8File(url, directory) {
  const response = await axios.get(url);
  const lines = response.data.split("\n");

  // 遍歷 m3u8 檔案中的每一行，找到 ts 檔案的 URL 並下載
  for (const line of lines) {
    if (line.startsWith("#")) continue;
    const tsUrl = new URL(line, url).href;
    const tsFilename = parseUrl(tsUrl).pathname.split("/").pop();
    const tsPath = joinPath(directory, tsFilename);
    await downloadFile(tsUrl, tsPath);
  }
}

// 測試用的程式碼
async function test() {
  const url =
    "https://video-lmo.xhcdn.com/token=nva=1684180800~dirs=5~hash=099fe8eeac0335f41409b/media=hls4/multi=256x144:144p,426x240:240p,854x480:480p,1280x720:720p/013/215/124/720p.h264.mp4.m3u8";
  const directory = "./videos";
  await downloadM3u8File(url, directory);
}

test();

```


## 心得

後端果然就是牛掰！
輕鬆把檔案下載下來，全部的ts都透過m3u8裡面的解析都出來惹。
之後想要進階延伸的就是，
如何不靠開發者工具找到影片m3u8的request url？

這樣就能更自動化了呢٩(｡・ω・｡)و