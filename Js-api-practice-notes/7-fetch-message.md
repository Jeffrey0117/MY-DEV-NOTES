---
title: JS的API筆記-fetch敲敲看 發送訊息

---

# JS的API筆記-fetch敲敲看 發送訊息

## 前情提要
自動發話寫法其實滿多種的，可以藉由DOM操作，
事實上也可以打API來進行發送，
之前寫過DOM的自動化，
這次來練習用ajax的post技術打API

## 效果
![](https://i.imgur.com/A68jgbh.png)



## fetch的程式

```javascript=

fetch("https://asia-east2-knocktalk-prod.cloudfunctions.net/addMessage", {
  headers: {
    accept: "*/*",
    "accept-language": "zh-TW,zh;q=0.9",
    authorization:
      "Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IjE2ZGE4NmU4MWJkNTllMGE4Y2YzNTgwNTJiYjUzYjUzYjE4MzA3NzMiLCJ0eXAiOiJKV1QifQ.eyJrbm9ja3RhbGsiOnRydWUsInByb3ZpZGVyX2lkIjoiYW5vbnltb3VzIiwiaXNzIjoiaHR0cHM6Ly9zZWN1cmV0b2tlbi5nb29nbGUuY29tL2tub2NrdGFsay1wcm9kIiwiYXVkIjoia25vY2t0YWxrLXByb2QiLCJhdXRoX3RpbWUiOjE2ODE4NDMwMzcsInVzZXJfaWQiOiJERVdES2dnSTFtZ0FUVWtDTjRvVjBUTmpIbGEyIiwic3ViIjoiREVXREtnZ0kxbWdBVFVrQ040b1YwVE5qSGxhMiIsImlhdCI6MTY4MTg0NjMzNywiZXhwIjoxNjgxODQ5OTM3LCJmaXJlYmFzZSI6eyJpZGVudGl0aWVzIjp7fSwic2lnbl9pbl9wcm92aWRlciI6ImFub255bW91cyJ9fQ.F8yXt2xPUdaFDPSrkblyPpmzi_CvwPkeuMuudXzi3WijbYaZUZUz-75eBeaQ8dUtu66JLhYMIr1THJ_CvkledxJxNVWD1GTgdSRpISHWLXmg_LvpryMqu0gHN2VaQabjuuc8rZ6bOmRmnkmj6WuEgO4uONu2nsadkYQ8cXFUsOtQ_R1ZaOz45YKjo37cjgVFJimHYIKfR9WcdBnISo21FVeM3XQNRqoF7uPkHeU4sYZK-88ZUHzkLbUlNu3rOIt-qHlfJ7mlxldGPG0KZ4RozHqzAvg5ovCv3gKPdHvVw-1uLWfpZvKSYdJU3UTXE0ZrxyRbPkBUkNLH8yUW9m8oKA",
    "content-type": "application/json",
    "sec-ch-ua":
      '"Chromium";v="112", "Google Chrome";v="112", "Not:A-Brand";v="99"',
    "sec-ch-ua-mobile": "?0",
    "sec-ch-ua-platform": '"Windows"',
    Referer: "https://knock.tw/",
    "Referrer-Policy": "strict-origin-when-cross-origin",
  },
  body: '{"data":{"mappingId":"這串是房間名稱吧","channelId":"應該是產生一組暫時ID","text":"這就是訊息內容"}}',
  method: "POST",
});

```


## 找到這串API的方法

![](https://i.imgur.com/oE3750S.png)

藉由devtool原本想用看的，可是寫出來的data送出就是錯誤，
還不太明白怎麼看，
後來想到右鍵複製的卑鄙招數就成功了。


## 


[JQuery.Ajax()的data参数类型三种写法](https://blog.csdn.net/ethan_10/article/details/80666277)

當然可以，以下是補完的兩個部分：

---

## fetch跟ajax的差別？

`fetch` 與 `jQuery.ajax` 雖然都是用來發送 HTTP 請求，但其實是兩種不同世代的技術：

### 1. 原生 vs 函式庫

* **fetch** 是原生 JavaScript API，現代瀏覽器內建，不需要載入任何函式庫。
* **ajax (jQuery.ajax)** 是 jQuery 提供的方法，需引入 jQuery 函式庫才能使用。

### 2. 語法風格

* **fetch** 使用 Promise，語法現代、清爽，支援 `async/await`。
* **ajax** 是 callback 為主，結構較為冗長，也較難進行錯誤攔截與鏈式操作。

### 3. 預設行為差異

* **fetch** 預設不會 reject HTTP 錯誤（例如 404 仍算成功），要手動檢查 `response.ok`。
* **ajax** 遇到 HTTP 錯誤會進入 `error` callback。

### 4. 支援度與轉換

* **fetch** 不支援舊版 IE（需要 polyfill）。
* **ajax** 可支援更舊的瀏覽器，適合需要廣泛兼容性的專案。

### 5. 資料處理方式

* **fetch** 回傳的是 `Response` 物件，需要手動 `.json()`、`.text()` 解析。
* **ajax** 會根據 `dataType` 自動解析回傳內容。

**簡單總結一句話：**

> `fetch` 是現代標準，適合新手與中高階開發者使用；
> `ajax` 是舊有系統與老前輩的遺產，在某些專案還是能見到。

---

## 心得

這次的實驗很像在打遊戲開外掛。
原本不知道怎麼傳訊息，以為要用 DOM 一行行模擬點擊、輸入，結果發現可以直接打 API 後完全不一樣。
等於是直接打到伺服器核心，繞過 UI 操作，效率快、自由度高，也比較不怕 UI 改版導致腳本壞掉。

但反過來也發現，API 的格式要求更精準，一個錯字就炸掉。
尤其是 header 裡的 `authorization`、`content-type`、body 結構，全都要照樣子做。
過程中光看 DevTool 看不懂，最後靠「右鍵複製 as fetch」才打通，這也提醒我，有時候真的不要硬撐，會抄就贏一半。

總結一句話：
**網頁的畫面只是表象，真正控制世界的是底層的 API。
學會 fetch，不只是會打資料，是學會「如何跟網站說話」。**
