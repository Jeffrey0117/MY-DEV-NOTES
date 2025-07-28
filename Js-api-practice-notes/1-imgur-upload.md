---
title: JS實戰-API使用imgur上傳圖片

---

# JS實戰-API使用imgur上傳圖片(米特API)


```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>圖片上傳網站</title>
</head>
<body>
    <h1>上傳圖片</h1>
    <input type="file" id="fileInput">
    <button id="uploadButton">上傳</button>
    <div id="result"></div>

    <script>
        const fileInput = document.getElementById('fileInput');
        const uploadButton = document.getElementById('uploadButton');
        const resultDiv = document.getElementById('result');

        // 監聽文件選擇事件
        fileInput.addEventListener('change', function() {
            // 確保至少選擇了一個文件
            if (fileInput.files.length > 0) {
                uploadButton.disabled = false; // 啟用上傳按鈕
            } else {
                uploadButton.disabled = true; // 禁用上傳按鈕
            }
        });

        // 監聽上傳按鈕點擊事件
        uploadButton.addEventListener('click', function() {
            const file = fileInput.files[0]; // 獲取選擇的文件

            if (file) {
                const formData = new FormData();
                formData.append('image', file); // 將文件添加到 FormData

                // 使用 fetch 上傳文件
                fetch('https://api.imgur.com/3/image', {
                    method: 'POST',
                    headers: {
                        'Authorization': 'Client-ID 3c9f126f31de44c',
                    },
                    body: formData,
                })
                .then(response => response.json())
                .then(data => {
                    // 顯示上傳結果
                    resultDiv.innerHTML = `<strong>上傳成功：</strong><br>圖片鏈接：${data.data.link}`;
                })
                .catch(error => {
                    // 顯示錯誤信息
                    resultDiv.innerHTML = `<strong>上傳失敗：</strong><br>${error}`;
                });
            }
        });
    </script>
</body>
</html>

```



2025/07/27 更新:

他們API改了...QQQ

以下是完整對比 Meteor 舊版與新版圖片上傳 API 寫法，並說明為什麼 **手動寫 boundary 會炸掉**，該怎麼寫才是正確方式。

---

# 🔄 圖片上傳 API 更新筆記：Meteor 新舊版本差異對比（前端觀點）

## 1️⃣ 舊版（Imgur API 風格）

```js
// 舊 API：類似 Imgur，用 client-id 授權，匿名即可
fetch('https://api.imgur.com/3/image', {
  method: 'POST',
  headers: {
    'Authorization': 'Client-ID xxx',
  },
  body: formData, // 使用 FormData，自動處理 Content-Type
});
```

🔧 使用技巧：

* `FormData` 可自動處理 `Content-Type`（**會自動加入 boundary**）
* 無需登入、無需 cookie，前端直接可用
* 上傳圖片只需設定 `image` 欄位名稱

🧠 適合用在：

* 需要開放給所有使用者上傳的匿名圖床服務
* 輕量級快速整合第三方 API（如：Imgur、ImageBB）

---

## 2️⃣ 新版（Meteor 自家 API）

```js
fetch('https://meteor.today/upload/upload_general_image', {
  method: 'POST',
  credentials: 'include', // 需要登入 cookie
  body: formData, // 一樣用 FormData 上傳圖片
});
```

🚨 變化重點：

* `headers` 不要手動加 `Content-Type`！
* 欄位名稱是 `file`（不是 image）
* **必須登入後使用**（cookie 驗證）

---

## 3️⃣ ❌ 為什麼不能手動寫 `Content-Type` + boundary？

錯誤範例：

```js
fetch(url, {
  method: 'POST',
  headers: {
    'Content-Type': 'multipart/form-data; boundary=------WebKitFormBoundaryxxxxxx'
  },
  body: '------WebKitFormBoundaryxxxxxx\r\nContent-Disposition: ...'
});
```

### ❌ 錯誤原因：

* 你手動寫的 body 是一整串字串，**不是真正的二進位圖檔內容**
* 若圖片是從 `<input type="file">` 取得，該圖是 binary，不能轉字串硬塞進 body
* `Content-Type` 的 boundary 必須要跟 body 的分隔線完全一致，一不對就炸

🔧 **正確做法**：交給瀏覽器自動處理！

```js
const formData = new FormData();
formData.append('file', fileInput.files[0]);

fetch('https://meteor.today/upload/upload_general_image', {
  method: 'POST',
  credentials: 'include',
  body: formData // 不加 headers，瀏覽器自動處理
});
```

---

## ✅ 正確對應表

| 項目             | 舊版 API（Imgur）                  | 新版 API（Meteor）              |
| -------------- | ------------------------------ | --------------------------- |
| 權限             | 匿名可用（Client-ID）                | 需要登入（cookie）                |
| 欄位名稱           | `image`                        | `file`                      |
| Content-Type   | 不需手動設定                         | 不需手動設定                      |
| 是否能手寫 boundary | ❌ 不行                           | ❌ 不行                        |
| 授權方式           | `Authorization: Client-ID xxx` | `credentials: include`（需登入） |
| 實作方式           | `FormData()`                   | `FormData()`                |

---

## 🧪 小提醒：怎麼測試上傳失敗？

如果你這樣寫就一定失敗：

```js
headers: {
  'Content-Type': 'multipart/form-data; boundary=...'
},
body: "------WebKitFormBoundary...\r\nContent-Disposition: ..."
```

失敗原因：

* 你給的 boundary 不會與圖片 binary 對應
* body 是純文字，不是 `Blob`、`File`
* 伺服器無法 parse

---

## 🧾 小結：前端工程師該怎麼改？

* ✅ 用 `FormData()` 裝圖片（別自己拼 body）
* ✅ 不要設定 `Content-Type`，交給瀏覽器處理
* ✅ 注意欄位名稱變成 `file`
* ✅ 若有加登入系統，要記得加 `credentials: include`
* ❌ 別想手動寫 boundary 或 multipart，太容易炸

---

需要我幫你包一份可重用的上傳元件（例如用 React、純 JS、Vue）？還是你想封裝成工具函式？我可以直接幫你寫好。
