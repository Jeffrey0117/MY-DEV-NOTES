---
title: nodeJS實戰-寫一個上傳圖片網站，利用uploadCC的API

---

# nodeJS實戰-寫一個上傳圖片網站，利用uploadCC的API

## 前情提要

[JS的API筆記- "upload.cc"的上傳圖片](https://hackmd.io/eJ2b5PIQSJW6gsIm4PzGmA#JS%E7%9A%84API%E7%AD%86%E8%A8%98--uploadcc%E7%9A%84%E4%B8%8A%E5%82%B3%E5%9C%96%E7%89%87)

## 觀念說明

![image](https://hackmd.io/_uploads/Hyqrhjo36.png)

它網站根本沒加密，好好花時間逆向。
https://upload.cc/


## 目前效果

![image](https://hackmd.io/_uploads/rkFCGss36.png)

成功收到前端丟過來的圖片了。



## 網頁程式碼

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>文件上传示例</title>
  </head>
  <body>
    <h1>文件上传示例</h1>
    <form id="uploadForm" enctype="multipart/form-data">
      <input type="file" name="image" multiple />
      <button type="submit">上传</button>
    </form>
    <div id="result"></div>
    <script>
      const uploadForm = document.getElementById("uploadForm");
      const resultEl = document.getElementById("result");

      uploadForm.addEventListener("submit", async (event) => {
        event.preventDefault();

        const formData = new FormData(uploadForm);

        try {
          const response = await fetch("http://localhost:3000/upload", {
            method: "POST",
            body: formData,
          });

          const urls = await response.json();

          urls.forEach((url) => {
            const imgEl = document.createElement("img");
            imgEl.src = url;
            resultEl.appendChild(imgEl);
          });
        } catch (error) {
          console.error(error);
        }
      });
    </script>
  </body>
</html>

```


## 後端伺服器


```
const express = require("express");
const axios = require("axios");
const FormData = require("form-data");
const bodyParser = require("body-parser");
const multer = require("multer");
const cors = require("cors"); // 引入 cors 模塊
const path = require("path");
const app = express();
const port = 3000;

// 设置静态文件夹
app.use(express.static(path.join(__dirname, "public")));

// 使用 bodyParser 中间件解析请求体
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

// 设置 Multer 中间件处理文件上传
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "uploads/"); // 上传文件保存的目录
  },
  filename: (req, file, cb) => {
    cb(
      null,
      file.fieldname + "-" + Date.now() + path.extname(file.originalname)
    );
  },
});

const upload = multer({ storage: storage });

// 处理 POST 请求，接收图片
app.post("/upload", upload.single("image"), (req, res) => {
  if (req.file) {
    // 图片上传成功
    res.json({ success: true, message: "Image uploaded successfully" });
  } else {
    // 图片上传失败
    res.json({ success: false, message: "Image upload failed" });
  }
});

// 启动服务器
app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

```