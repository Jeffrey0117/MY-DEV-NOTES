---
title: JS實戰-hahow下載影片程式開發(前端方法)

---

#  JS實戰-hahow下載影片程式開發(前端方法)


## 前端JS方法

https://hahow.in/courses/63abcd63770e61779597e6a6/discussions


https://hahow.in/courses/這個是課程的ID/這個是課程內容的ID


第一步

先抓到所有課程的ID，包含標題以及url

![image](https://hackmd.io/_uploads/HyzONvL3a.png)


```
var videos = [];

$('a.list-content.flex').each(function() {
    var hrefValue = $(this).attr('href');
    var lastSegment = hrefValue.split('/').pop();

    let secondElementText = $(this).children(':eq(1)').contents().filter(function() {
        return this.nodeType === 3; // 過濾掉非文字節點
    }).text().trim();

    // 創建一個物件，並將其添加到 videos 陣列中
    var videoObj = {
        "title": secondElementText,
        "url": lastSegment
    };

    videos.push(videoObj);
});

// 現在 videos 陣列包含所有影片的資料
console.log(videos);

```





第二步

針對此頁面的檔案進行下載

找到影片位置以及字幕檔案


```
var sourceSrc = $('source[size="1080"]').attr('src');
downloadMp4(sourceSrc);
var trackSrc = $('track').attr('src');
downloadTxt(trackSrc);
```


```
function downloadTxt(src) {
    fetch(src)
        .then(response => response.blob())
        .then(blob => {
            // 創建一個可下載的連結
            var downloadLink = document.createElement("a");
            downloadLink.href = URL.createObjectURL(blob);

            // 取得檔案名稱
            var fileName = src.split('/').pop();
            downloadLink.download = fileName + '.txt'; // 加上 .txt 作為檔案的附檔名

            // 模擬點擊連結觸發下載
            downloadLink.click();

            // 釋放 URL 物件
            URL.revokeObjectURL(downloadLink.href);
        })
        .catch(error => console.error('下載失敗：', error));
}



function downloadMp4(src) {
    fetch(src)
        .then(response => response.blob())
        .then(blob => {
            // 創建一個可下載的連結
            var downloadLink = document.createElement("a");
            downloadLink.href = URL.createObjectURL(blob);

            // 取得檔案名稱
            var fileName = src.split('/').pop();
            downloadLink.download = fileName + '.mp4'; // 加上 .txt 作為檔案的附檔名

            // 模擬點擊連結觸發下載
            downloadLink.click();

            // 釋放 URL 物件
            URL.revokeObjectURL(downloadLink.href);
        })
        .catch(error => console.error('下載失敗：', error));
}


```


