---
title: nodeJS筆記-接收ajax傳值方法以及細節

---

# nodeJS筆記-接收ajax傳值方法以及細節

## 學習影片

<iframe width="560" height="315" src="https://www.youtube.com/embed/7e1wQDBxxek?si=G2vTGRzIt_L9kmUC" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

我還沒看，寫完才想到說應該找一些影片來搭配。


## express是什麼

[網頁後端原理：http.createServer](https://ithelp.ithome.com.tw/articles/10186876)

[有輪堪用直須用：Express.js](https://ithelp.ithome.com.tw/articles/10186877)


先看懂這兩篇文章，講解了透過nodeJS開伺服器的兩種方式，第一種很累，第二種很爽。

也就是所謂express框架的使用。

至於怎麼用可以直接看下面這篇文章：


[簡明易懂的配置教學](https://expressjs.com/zh-tw/guide/routing.html)


### res.body是什麼

[What are "res" and "req" parameters in Express functions?](https://stackoverflow.com/questions/4696283/what-are-res-and-req-parameters-in-express-functions)
以下是你所提及主題中，尚未撰寫說明的區塊，我已根據內容幫你補上簡潔實用的說明，每一段皆為對應主題的補充 part：

---

### `app.post` 是什麼

`app.post` 是 Express 中用來處理「POST 請求」的函式，通常用於接收表單、JSON、上傳檔案等需要傳送資料給伺服器的情境。

```js
app.post('/submit', function(req, res) {
  const data = req.body;
  res.send('資料已收到：' + JSON.stringify(data));
});
```

它的第一個參數是路由（例如 `/submit`），第二個是 callback 函式，裡面可透過 `req.body` 取得傳來的資料，回應用 `res.send()` 等。

---

### `app.get` 是什麼

`app.get` 是 Express 中用來處理「GET 請求」的函式，通常用於從伺服器請求資料（例如載入頁面、取得查詢結果）。

```js
app.get('/hello', function(req, res) {
  res.send('Hello World!');
});
```

GET 請求中的參數常見於 URL 查詢字串中，可透過 `req.query` 取得，例如 `/search?keyword=node`。

---

### `body-parser` 是什麼

`body-parser` 是一個中介軟體（middleware），讓 Express 能夠讀取 POST 請求中的 body 資料（例如表單或 JSON）。

新版 Express 已內建類似功能，但早期要這樣寫：

```js
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
```

現在可以直接用：

```js
app.use(express.urlencoded({ extended: true }));
app.use(express.json());
```

這樣就能使用 `req.body` 取得傳來的資料。

---

### `MULTER` 是什麼

`multer` 是專門處理「檔案上傳」的中介軟體（middleware），可以接收表單中的檔案（例如圖片、PDF）並儲存到伺服器。

基本用法：

```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), function (req, res) {
  res.send('檔案上傳成功：' + req.file.originalname);
});
```

你可以控制檔案名稱、路徑、限制檔案大小等。

---

### Ajax POST 是怎樣

AJAX 的 POST 用來把資料送到伺服器，例如提交表單。

```js
$.ajax({
  url: '/submit',
  method: 'POST',
  data: { name: 'Jeff', age: 30 },
  success: function (res) {
    console.log('成功：', res);
  },
  error: function (err) {
    console.log('錯誤：', err);
  }
});
```

資料會被放在 `req.body` 中。

---

### Ajax GET 是怎樣

AJAX 的 GET 用來從伺服器請資料，適合查詢或載入東西。

```js
$.ajax({
  url: '/getUser?name=Jeff',
  method: 'GET',
  success: function (res) {
    console.log('成功取得資料：', res);
  },
  error: function (err) {
    console.log('錯誤：', err);
  }
});
```

資料會出現在 `req.query` 裡。


[[今晚我想來點 Express 佐 MVC 分層架構] DAY 06 - Express 與 body-parser](https://ithelp.ithome.com.tw/articles/10241083)

### MULTER是什麼

## ajax怎麼用


```
$.ajax({
  url:,
  method:,
  dataType:,
  data:
  
  success:function(res){console.log(res)},
  error:function(err){console.log(err)},
});
```

![image](https://hackmd.io/_uploads/H15-3xRh6.png)

[使用jQuery的Ajax技術和介接api吧!](https://ithelp.ithome.com.tw/articles/10226692)



[前端面试基础（四）Ajax & Node.js](https://zhuanlan.zhihu.com/p/618347413)



### POST跟GET差別

[http Post 和 Get 差異](https://totoroliu.medium.com/http-post-%E5%92%8C-get-%E5%B7%AE%E7%95%B0-928829d29914)

[淺談 HTTP Method：表單中的 GET 與 POST 有什麼差別？](https://blog.toright.com/posts/1203/%E6%B7%BA%E8%AB%87-http-method%EF%BC%9A%E8%A1%A8%E5%96%AE%E4%B8%AD%E7%9A%84-get-%E8%88%87-post-%E6%9C%89%E4%BB%80%E9%BA%BC%E5%B7%AE%E5%88%A5%EF%BC%9F)


## promise是什麼

[Promise实现原生JS的Ajax请求（后端使用Node.js）](https://cloud.tencent.com/developer/article/1376024)

## 解決ajax跨域的方法:

1.設定header檔案

2.建立在同個目錄底下

3.以app.get裡面模板字符送出


## node.js使用jquery

```
npm install jquery jsdom
// install the dependencies

var jsdom = require('jsdom');
const { JSDOM } = jsdom;
const { window } = new JSDOM();
const { document } = (new JSDOM('')).window;
global.document = document;

var $ = jQuery = require('jquery')(window);
```

[Can I use jQuery with Node.js?](https://stackoverflow.com/questions/1801160/can-i-use-jquery-with-node-js)

## JS寫法

### 箭頭函式

```
const materials = ['Hydrogen', 'Helium', 'Lithium', 'Beryllium'];
console.log(materials.map((material) => material.length));
```

[MDN文件:箭頭函式](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### jquery的dom方法怎麼寫

常常忘記XD
![image](https://hackmd.io/_uploads/SyllT4R2T.png)

[查文檔看這邊](https://api.jquery.com/category/manipulation/general-attributes/)

### JSON

```
{

  "Influencers" :  [ 
  {
   "name" : "Jaxon", 
   "age" : 42, 
   "Works At" : "Tech News"
  }
  
  {
   "name" : "Miller", 
   "age" : 35,
   "Works At" : "IT Day"
  }
  
  ] 
  }
```

[JSON 的定義](https://www.oracle.com/tw/database/what-is-json/)

### const 花括號寫法

如果需要聲明多個const變量的時候，很多時候我們都是變量一個個聲明的，
這樣的話，我們的代碼顯的非常難看。下面這種方式可以進行簡寫 

簡寫前： 
const name = app.name;
const version = app.version;
const type = app.type;

簡寫後： 
const { name, version, type } = app;

[const 花括號簡寫方式](https://blog.csdn.net/weixin_45172119/article/details/108534761)