---
title: JS的API筆記- "upload.cc"的上傳圖片

---

# JS的API筆記- "upload.cc"的上傳圖片

## 前情提要

最近在練習觀察網頁的服務，找到ajax送出的post，
然後自己寫出API，來使用他們的功能。
於是我就想到可以利用別人的上傳圖片功能，於是找到了upload這個網頁。

![](https://i.imgur.com/3Bqp1bi.png)
觀察出他是丟一個post，然後回來就是一串json。

## 觀念筆記

![](https://i.imgur.com/Yz9Rt7T.png)

### 純JS程式碼 (可以上傳多張圖)

因為他本來就允許多張上傳。

```
const html = `<form>
  <input type="file" id="fileInput" multiple>
  <button type="button" id="uploadButton">Upload</button>
</form>`

$('body').append(html)



const fileInput = document.getElementById("fileInput");
const uploadButton = document.getElementById("uploadButton");

uploadButton.addEventListener("click", () => {
  const formData = new FormData();
  for (let i = 0; i < fileInput.files.length; i++) {
    formData.append("uploaded_file[]", fileInput.files[i]);
  }

  fetch("https://upload.cc/image_upload", {
    method: "POST",
    body: formData,
  })
    .then(response => response.json())
    .then(data => {
      data.success_image.forEach(image => console.log("https://upload.cc/"+image.url));
    })
    .catch(error => console.error(error));
});

```

因為跨域限制，所以這段程式碼要在`https://upload.cc/#`中使用
也就是他們的[官網(點這邊進來吧)](https://upload.cc/#)

## 心得

原本想叫GPT幫我改成後端，他給我一個點子是：
先寫一個上傳的前端，然後利用後端把檔案上傳給uploadcc之後，
再傳回前端。
我發現這是很聰明的手段，可是問了老半天，就是會有報錯。

沒關係就先放棄這個吧( ´•̥̥̥ω•̥̥̥ )
已經整理很多惹！