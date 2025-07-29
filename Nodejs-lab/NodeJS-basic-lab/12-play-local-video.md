---
title: node.js筆記-播放本機中的影片

---

# node.js筆記-播放本機中的影片

## 效果
![](https://hackmd.io/_uploads/HkKVvxgS2.png)

## 專案項目安排
![](https://hackmd.io/_uploads/HkgLwegSh.png)

需要express的套件安裝。

## 程式碼


```
const express = require("express");
const app = express();
const fs = require("fs");
const path = require("path");

app.use(express.static(path.join(__dirname, "public")));

app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "public/index.html"));
});

app.get("/video", (req, res) => {
  const range = req.headers.range;
  const videoPath = path.join(__dirname, "public/video.mp4");
  const videoSize = fs.statSync(videoPath).size;

  if (range) {
    const CHUNK_SIZE = 10 ** 6;
    const start = Number(range.replace(/\D/g, ""));
    const end = Math.min(start + CHUNK_SIZE, videoSize - 1);
    const contentLength = end - start + 1;
    const headers = {
      "Content-Range": `bytes ${start}-${end}/${videoSize}`,
      "Accept-Ranges": "bytes",
      "Content-Length": contentLength,
      "Content-Type": "video/mp4",
    };

    res.writeHead(206, headers);
    const videoStream = fs.createReadStream(videoPath, { start, end });
    videoStream.pipe(res);
  } else {
    const headers = {
      "Content-Length": videoSize,
      "Content-Type": "video/mp4",
    };
    res.writeHead(200, headers);
    const videoStream = fs.createReadStream(videoPath);
    videoStream.pipe(res);
  }
});

app.listen(3000, () => {
  console.log("http://localhost:3000/video");
});

```

## 心得

超級酷的耶(๑•́ ₃ •̀๑)
延伸的疑問是：那這樣使用者有辦法把這個影片下載起來嗎？
剛剛看了一下右下角播放器是可以下載的。

滿有趣的耶！未來想要更深入理解這跟blob之類的有什麼差別。
不過如今為止我已經更了解HTTP通訊之間、request、伺服器、本機這些名詞背後運作的機制了。

很多課程都會在一開始提到，但真的實際去操作才有感覺！
超棒ʕ´• ᴥ•̥`ʔ