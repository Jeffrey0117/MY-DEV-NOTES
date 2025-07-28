---
title: JS實戰-hahow下載影片程式開發(後端方法)
---

# JS 實戰-hahow 下載影片程式開發(後端方法)

## 前情提要

有兩種下載方法

一種純前端腳本執行(使用者自己登入)

一種純後端 nodeJS 下載(這就需要 cookie)

第三種前後配合 前端找到網址 後端下載

## 程式使用技術、修改 bug

- [x] 原本使用 fetch 改為使用 axios
- [x] 使用.env 環境變數設置
- [x] 修改檔名不得有特殊符號問題
- [x] 將課程單元依照章節設置資料夾去分門別類
- [x] 修改下載影片畫質問題(index 條件判斷)
- [x] 修改邏輯，每次下載一個檔案，改掉一次下載多個檔案(await、resolve)
- [x] 修改判斷條件，區分網址是指派作業 or 課程影片(data.type 從請求回傳的資料去讀寫)
- [x] 將 headers 改為變數寫入
- [x] 新增下載回家作業請求(透過 fs)
- [x] 新增 package(透過 npm init)
- [x] 打包:將檔案先以 ncc 打包成 index.js 再用 pkg 打包成 exe 檔案

## 未來新增功能

把所有的購買課程列表出來

## 後端版本 nodejs

### 完整原始碼(需要寫一個.env 去設定變數)

```
const axios = require("axios");
const fs = require("fs");
const fs2 = require("fs").promises;
require("dotenv").config();

const { createWriteStream } = require("fs");
const { join } = require("path");

ClassId = "63abcd63770e61779597e6a6";
authorization =
  "你的資料";
authorizationINM = '你的資料';

ClassId = process.env.ClassId;
authorization = process.env.authorization;
authorizationINM = process.env.authorizationINM;

let HahowClassUrl = "https://api.hahow.in/api/courses/" + ClassId; //組成API網址

async function downloadVideo(title, videoUrl, Path) {
  try {
    const fileName = `${title}`;
    const filePath = join("./" + Path, fileName);

    // 检查文件是否已存在
    try {
      await fs.promises.access(filePath);
      // 如果access没有抛出错误，说明文件已存在
      console.log(`Skipped: ${fileName} (File already exists)`);
      return filePath;
    } catch (error) {
      // 如果access抛出错误，说明文件不存在，可以继续下载
    }

    const response = await axios.get(videoUrl, { responseType: "stream" });
    const writeStream = createWriteStream(filePath);

    response.data.pipe(writeStream);

    return new Promise((resolve, reject) => {
      writeStream.on("finish", () => {
        console.log(`Downloaded: ${fileName}`);
        resolve(filePath);
      });

      writeStream.on("error", (error) => {
        console.error("Error writing to file:", error);
        reject(error);
      });
    });
  } catch (error) {
    console.error("Error downloading video:", error);
    throw error;
  }
}

async function createFolder(chapterTitle, check) {
  if (check == 0) {
    const folderPath = `./${chapterTitle}`;

    try {
      // 检查文件夹是否存在
      await fs.promises.access(folderPath);

      // 如果access没有抛出错误，说明文件夹已存在
      console.log(`Folder '${folderPath}' already exists!`);
    } catch (error) {
      // 如果access抛出错误，说明文件夹不存在，尝试创建
      try {
        await fs.promises.mkdir(folderPath);
        console.log(`Folder '${folderPath}' created successfully!`);
      } catch (error) {
        console.error(`Error creating folder '${folderPath}':`, error);
      }
    }
  } else {
    const folderPath = `./${ClassTitle}/${chapterTitle}`;

    try {
      // 检查文件夹是否存在
      await fs.promises.access(folderPath);

      // 如果access没有抛出错误，说明文件夹已存在
      console.log(`Folder '${folderPath}' already exists!`);
    } catch (error) {
      // 如果access抛出错误，说明文件夹不存在，尝试创建
      try {
        await fs.promises.mkdir(folderPath);
        console.log(`Folder '${folderPath}' created successfully!`);
      } catch (error) {
        console.error(`Error creating folder '${folderPath}':`, error);
      }
    }
  }
}

async function createHomework(fileName, fileContent, Path) {
  const filePath = join("./" + Path, fileName);

  try {
    await fs2.writeFile(filePath, fileContent);
    console.log(`Homework '${fileName}' created successfully!`);
  } catch (error) {
    console.error(`Error creating Homework '${fileName}':`, error);
  }
}

async function downloadChapterItems(chapterData, chapterTitle) {
  for (let j = 0; j < chapterData.items.length; j++) {
    const courseTitle = chapterData.items[j].content.title
      .replaceAll("：", "-")
      .replaceAll(":", "-")
      .replaceAll("*", "")
      .replaceAll("/", "或")
      .replaceAll(/[<>"\\|?]/g, "");

    const courseUrl = chapterData.items[j].content._id;
    const courseType = chapterData.items[j].type;

    console.log("正在下载: " + courseTitle);

    // 使用 await 等待下载完成
    await new Promise((resolve, reject) => {
      if (courseType == "LECTURE") {
        let fetchUrl =
          "https://api.hahow.in/api/lectures/" +
          courseUrl +
          "?requestBackup=false";
        //下載課程影片的部分
        customFetch(fetchUrl, async (data) => {
          try {
            const index = data.video.videos.findIndex(
              (item) => item.width === 1920 && item.height === 1080
            );
            const itemUrl = data.video.videos[index].link;
            const subtitle = data.video.subtitles[0].link;

            await downloadVideo(
              j + 1 + "." + courseTitle + ".mp4",
              itemUrl,
              ClassTitle + "/" + chapterTitle
            );
            console.log("成功下载影片: " + courseTitle);
            await downloadVideo(
              j + 1 + "." + courseTitle + "(字幕)" + ".srt",
              subtitle,
              ClassTitle + "/" + chapterTitle
            );
            console.log("字幕下载成功: " + courseTitle);
            resolve();
          } catch (error) {
            console.error("下载失败:", error);
            reject(error);
          }

          //---------------------------------
        });
      } else if (courseType == "ASSIGNMENT") {
        //下載指定作業的狀況

        fetchUrl =
          "https://api.hahow.in/api/assignments/" +
          courseUrl +
          "?requestBackup=false";

        customFetch(fetchUrl, async (data) => {
          let fileName = data.title;
          fileName = fileName
            .replaceAll("：", "-")
            .replaceAll(":", "-")
            .replaceAll("*", "")
            .replaceAll("/", "或")
            .replaceAll(/[<>"\\|?]/g, "");
          const fileContent = data.description;

          try {
            await createHomework(
              j + 1 + "." + fileName + "(回家作業)" + ".html",
              fileContent,
              ClassTitle + "/" + chapterTitle
            );
            console.log("成功下载回家作業: " + fileName);
            resolve();
          } catch (error) {
            console.error("下载失败:", error);
            reject(error);
          }
        });
      }
      //
    });
  }
}

let headers = {
  accept: "*/*",
  "accept-encoding": "gzip, deflate, br",
  "accept-language": "zh-TW",
  authorization: authorization,
  "content-type": "application/json; charset=utf-8",
  "if-none-match": authorizationINM,
  origin: "https://hahow.in",
  referer: "https://hahow.in/",
  "sec-ch-ua":
    '"Not A(Brand";v="99", "Google Chrome";v="121", "Chromium";v="121"',
  "sec-ch-ua-mobile": "?0",
  "sec-ch-ua-platform": '"Windows"',
  "sec-fetch-dest": "empty",
  "sec-fetch-mode": "cors",
  "sec-fetch-site": "same-site",
  "user-agent":
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36",
  "x-country-code": "TW",
  "x-hahow-platform": "web:unknown:1.0.0+1708670862-25712f225b",
  "x-referrer": HahowClassUrl,
};

async function customFetch(url, thenCallback) {
  try {
    const response = await fetch(url, {
      headers: headers,
      referrer: "https://hahow.in/",
      referrerPolicy: "strict-origin-when-cross-origin",
      method: "GET",
      mode: "cors",
      credentials: "include",
    });
    let data = await response.json();
    thenCallback(data);
  } catch (error) {
    console.error("Error:", error);
  }
}

let ClassTitle;
customFetch(HahowClassUrl, async (data) => {
  ClassTitle = data.title
    .replaceAll("：", "-")
    .replaceAll(":", "-")
    .replaceAll("*", "")
    .replaceAll("/", "或")
    .replaceAll(/[<>"\\|?]/g, "");

  createFolder(ClassTitle, 0);

  customFetch(HahowClassUrl + `/modules/items`, async (data) => {
    for (let k = 0; k < data.length; k++) {
      const chapterTitle =
        "[第" +
        (k + 1) +
        "章]" +
        data[k].title
          .replaceAll("：", "-")
          .replaceAll(":", "-")
          .replaceAll("*", "")
          .replaceAll("/", "或")
          .replaceAll(/[<>"\\|?]/g, "");
      createFolder(chapterTitle, 1);
      await downloadChapterItems(data[k], chapterTitle);
    }
    console.log("Hahow課程:「" + ClassTitle + "」已經全數下載完畢！");
  });
});

```

### .env 格式

```
# Hahow下載爬蟲 (會員登入設置)
## 原理:使用cookie
############################
## ClassId：你的課程ID
## authorization：登入後透過檢查，到network找post名稱為item的裡面有個authorization
## authorizationINM: 此為另一項是if not match的值
############################
## 以上三項填好即可

ClassId = "5f14aeabcad0d0afe5ea3898"

authorization = "你的資料"


authorizationINM = '你的資料'
```
