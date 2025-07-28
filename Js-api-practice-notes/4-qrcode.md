---
title: JS練習API筆記-QRcode生成器

---

# JS練習API筆記-QRcode生成器

## QR Code Generator的生成原理

一般來說，QR Code Generator 可以使用以下方式生成 QR 碼：

1. 資料輸入：使用者提供所需編碼的資料，例如文本、URL 或其他數據。

2. 編碼處理：QR Code Generator API 接收到輸入資料後，將其編碼成 QR 碼內容。這通常涉及到將資料轉換為適當的二進制格式，並添加必要的錯誤修正碼。

3. 圖像生成：一旦 QR 碼的內容編碼完成，API 將使用圖像處理技術將其轉換為可視的圖像。這可能包括在矩陣上將黑白像素填充，生成特定的模式和定位標記，以及添加必要的邊界。

4. 輸出提供：API 將生成的 QR 碼圖像作為輸出提供給使用者。這可以是圖像文件、網址或其他適當的形式，以便使用者能夠顯示、下載或嵌入 QR 碼。

需要注意的是，每個 QR 碼生成器的實現方式可能會有所不同，具體取決於所使用的 API。我建議查閱相關的文檔或官方資源，以獲得更具體的技術細節和操作指南。

QR Code 的生成和編碼過程非常複雜。如果需要更高級的功能，例如錯誤修正碼、定位標記等，建議使用現有的 QR Code 庫或 API。

## QR code API

我們使用Google的QR Code Generator API來生成QR Code。在點擊生成QRcode的按鈕時，我們會構造一個URL，其中包含要生成QR Code的內容，然後通過創建一個img元素，設置它的src屬性為這個URL，並添加到容器中顯示QR Code。


## 程式碼

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>QRcode Generator</title>
  </head>
  <body>
    <h1>QRcode Generator</h1>
    <label for="url-input">URL:</label>
    <input type="text" id="url-input">
    <button id="generate-button">Generate QRcode</button>
    <div id="qrcode"></div>
    <script src="qrcode-generator.js"></script>
  </body>
      <script>
      const urlInput = document.getElementById('url-input');
const generateButton = document.getElementById('generate-button');
const qrcodeContainer = document.getElementById('qrcode');

generateButton.addEventListener('click', () => {
  const url = urlInput.value;
  if (url) {
    qrcodeContainer.innerHTML = '';
    const apiEndpoint = `https://chart.googleapis.com/chart?chs=256x256&cht=qr&chl=${url}`;
    const imgElement = document.createElement('img');
    imgElement.src = apiEndpoint;
    qrcodeContainer.appendChild(imgElement);
  }
});

      </script>
</html>

```


## github-node-qrcode

![](https://hackmd.io/_uploads/H1J5_CgS3.png)
https://github.com/soldair/node-qrcode

是個不錯的套件，有需要可以拿來用。

## 心得

原本是想要自己寫一個生產QRcode的，但問了GPT，
這似乎是超級高難度、高技術的過程。
因此最後也妥協了就改學用人家的API就好了。

這樣也不用浪費時間刻輪子٩(๑•̀ω•́๑)۶
