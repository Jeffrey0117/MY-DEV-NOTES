---
title: 'nodeJS實戰-劍橋查單字:更新與修正問題'

---

# nodeJS實戰-劍橋查單字:更新與修正問題

## 前情提要

這個專案是學習發送請求，也就是檢查network裡面的API呼叫，並且學會實現。
會用到的工具有axios以及jsdom(或是cheerio)這兩個套件。
在nodeJS架上伺服器使用的是express，方法滿簡單的。

技術上還用到了express以及模板字符，幾乎是把最好用的套件全部集中起來了！


## 回顧文章

[JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (上)](https://hackmd.io/LUTkSCaNShaxIsNTHbKXaA)
[JS打ajax練習-劍橋英文字典查詢:配合node.js製作 (下)](https://hackmd.io/K-2aupiPTfiIz5UcYZLIQQ)

### github


[原始碼連結點我](https://github.com/Jeffrey0117/CambrigeImgAjax)

舊的筆記有備份原始碼，我這邊就直接把新增的丟到githun上備份。
另外寫了一份教學筆記！講解怎麼修改已經上傳過的倉庫檔案。

[git教學-如何修改已上傳檔案？pull的功能揭密！](https://hackmd.io/4DkJgcKBR7m3x3yw6zT5tg)

## 功能新增

### 查不到的時候查error給他們看XD
![image](https://hackmd.io/_uploads/BJSXZFs3a.png)


## 把出現的bug修正

### XSS資安問題

![image](https://hackmd.io/_uploads/Sy6XPYin6.png)

```
><img src=x onerror=prompt(5)>
```

修正方法，使用lodash套件，又學到新東西了：

![image](https://hackmd.io/_uploads/rkfeOKo2p.png)

```
const { escape } = require("lodash"); // 引入 lodash 中的 escape 函数
```

原來這麼爽，真的都不用自己造輪子XD 拿人家現成的工具又強又好用。
雖然網頁還是會噴錯，但我也懶得再加強了，先降子就OK

![image](https://hackmd.io/_uploads/SJjCKtj2p.png)

至少不會被簡單XSS打出來，意識有到！

[補充一個payload大全](https://github.com/imnarendrabhati/testing-new/blob/master/%3CIMG%20SRC%3DX%20ONERROR:JAVASCRIPT:ALERT(1)%3B%3E)

提供滿多有料的payload，之後有機會都可以拿來玩一玩。



### 劍橋單字API有問題之修正

`Error: socket hang up in Node.js `

[Error: socket hang up in Node.js [Solved]](https://bobbyhadz.com/blog/error-socket-hang-up-in-node-js)

稍微想了一下，猜到可能是因為axios沒有加上header，過去劍橋網頁沒有設防，
然而最近剛好看到steam教育網的"反爬蟲教學"，
裡面也有提到一個最基本最簡單的手段，就是確認這個請求發送是否來自瀏覽器。

於是我就想有可能，過去沒有檢驗，現在需要確認是瀏覽器。
果真加上header之後這個錯誤就消失了，成功爬取到英文單字的內容。

寫法如下：
```
const headers = {
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3',
  'Referer': 'https://dictionary.cambridge.org/'
};

  const { data } = await axios.get(
 'https://dictionary.cambridge.org/zht/%E8%A9%9E%E5%85%B8/%E8%8B%B1%E8%AA%9E-%E6%BC%A2%E8%AA%9E-%E7%B9%81%E9%AB%94/test',
    { headers }
  );

```

### 圖片沒辦法顯示之修正

重新測過一遍，發現data裡面的位置有變：
![image](https://hackmd.io/_uploads/SJ-wZKshT.png)

稍微修正一下圖片又重新復活了。




## 觀念筆記

### express架設伺服器用

很好用，基本上就是這樣寫

```
const app = express();
```

然後透過get跟listen之類的去架構網站：

```
app.listen(3000, () => {
  console.log("http://localhost:3000/search/dog");
});
```
這邊開在3000的port上。
下面是分頁的寫法，會搭配try，以免網址使用者想導去不存在的頁面：

```javascript=
app.get("/透過網址去分頁", async (req, res) => {
    try{
        res.send(content);  //content這個變數可以用模板字符放html代碼

        res.end();
    }catch{

        //res.status(500).send("無此頁面");

    }
}
依照此原理，多寫幾個分頁出來就把網站搞定囉！

```

### 載入CSS與JS的有趣方法：fs讀取

透過fs這個套件，讀一下目錄底下的檔案：
```
  const cssContent = fs.readFileSync("./style.css", "utf8");

  const jsContent = fs.readFileSync("./script.js", "utf8");
```

然後根據上面說的content模板字符，裡面在用${}這個方法把變數丟進去！
非常的聰明有趣。
```
        <style>${cssContent}</style>
        <script>${jsContent}</script>
```
類似的東西都可以用相同原理完成唷XD


### axios & JSDOM

axios 是發送請求用的套件，新增header的寫法很方便，接收也很好寫。
一般來說也會用try與catch去確保當回應403之類的怎麼辦，才不會暴力的噴錯XD

JSDOM 則是操縱dom的，基本上就是名副其實！

通常後端都是這兩個強力武器搭配，用 axios 發送 GET 請求，
然後使用 JSDOM 將 HTML 內容解析成 DOM，就可以針對頁面上的東西操作、存取等。


示範：

```
app.get('/test', (req, res) => {
  const url = "https://www.macmillandictionary.com/dictionary/british/change_1";

  axios
    .get(url)
    .then((response) => {
      const dom = new JSDOM(response.data);
      const targetElements = dom.window.document.querySelectorAll(
        "div.SENSE-CONTENT"
      );
      const contents = Array.from(targetElements).map((element, index) => {
        const title = `<h1>${index + 1}</h1>`; // 添加帶有序號的標題
        return title + element.innerHTML; // 返回帶有標題的內容
      });
      const content = contents.join(''); // 將所有內容拼接成一個字符串
      res.send(content); // 將內容作為響應發送給客戶端
    })
    .catch((error) => {
      console.error(error);
      res.send('出現錯誤'); // 如果發生錯誤，則返回一個錯誤提示
    });
});

```
