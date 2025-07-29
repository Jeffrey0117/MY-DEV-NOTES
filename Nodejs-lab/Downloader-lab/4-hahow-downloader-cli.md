---
title: 'nodeJS實戰:hahow下載器(cmd介面更新)+選擇課程/章節功能'

---

# nodeJS實戰:hahow下載器(cmd介面更新)+選擇課程/章節功能


## 前情提要

製作了hahow下載器，原因是因為它品牌大，卻又很好下載。
檢查之後很快能找到mp4的網址，詳細原理就看之前文章：

[JS教學-hahow下載器的製作與使用說明](https://hackmd.io/H_uEYf3fTlSsqCFz0SUGTQ)
[JS實戰-hahow下載影片程式開發(前端方法)](https://hackmd.io/04yZGQNkQeK3RVoxIhCF7Q)
[JS實戰-hahow下載影片程式開發(後端方法)](https://hackmd.io/6DAbJOydRf65WldrrZz2xw)


[JS筆記-解雷指南：pkg的技巧整理、坑點](https://hackmd.io/EB_W2KSmSim2V1Q6CFdW8A)
[JS筆記-打包pkg教學以及相關知識(一)](https://hackmd.io/aw6DFGZoQG2KJp--fcbc9A)

簡單介紹一下，主要使用到axios、cheerio、還有用到fs。
透過ncc跟pkg打包成exe檔案，並附上env給使用者自行定義參數(登入用)，
整套下載器就開發完成，可以分享給芸芸眾生！

## github

[原始碼點這邊](https://github.com/Jeffrey0117/hahow-collecter/tree/main)


## 完成效果
![image](https://hackmd.io/_uploads/H1E_VVj2a.png)

把條件判斷全部都寫了個去，也就是當使用者輸入不合法的文字，要馬是超出課程數字，或是亂打，
都不會噴錯，而是console出來並且請使用者好好反省(?
這過程意外的複雜又麻煩，但寫完總是完整了它，硬是不寫也很奇怪。

另外請GPT幫忙修正一下程式碼的風格，邏輯上挺好的。
之後未來想要養成先把規則思路寫清楚在備註上，然後再去完成，
不然直接動手寫，最後要理清邏輯的時候就要把code重看好幾遍、修改也容易出錯。


## 功能新增

### cmd介面的console
![image](https://hackmd.io/_uploads/SJJZVCq2p.png)

想要做出這樣的圖案效果，搜尋了一下這個叫做ASNI藝術。以前剛玩PTT有學過XD
知道名稱後google相關的生產器，
要稍微選一下好看的、能使用的，或注意把不能用的符號修正。
![image](https://hackmd.io/_uploads/S1oRVR5np.png)

[生產器連結點我](https://patorjk.com/software/taag/#p=display&f=Doh&t=hahow%20%0A)

再把它用字符存取進入變數中，console出來就有效果了！
![image](https://hackmd.io/_uploads/BJ3sVAq3a.png)

會需要寫這個是因為，這整套會打包成exe檔案，讓使用者用起來舒服(?

### 文章推薦: chalk套件

![image](https://hackmd.io/_uploads/SJZOt9j26.png)

[[node.js] 打造美觀的互動式 CLI 介面](https://fullstackladder.dev/blog/2022/03/06/node-packages-to-create-beauty-cli/)



### 相關網頁

[看板asciiart 文章列表- 批踢踢實業坊](https://www.ptt.cc/bbs/asciiart/index.html)

[「Ascii Art Generator」輸入英文字、載入圖片就能自動生成字元畫](https://briian.com/50692/)
裡面的網站已經死了，看看就好。


### 從 NodeJS 控制檯獲取使用者輸入

有兩種方法，分別是兩個不同的套件。

[文章教學點這](https://www.delftstack.com/zh-tw/howto/javascript/get-user-input-in-javascript/#%E5%BE%9E-nodejs-%E6%8E%A7%E5%88%B6%E6%AA%AF%E7%8D%B2%E5%8F%96%E4%BD%BF%E7%94%A8%E8%80%85%E8%BC%B8%E5%85%A5)

第一種：readline。

```
const readline = require('readline');

const q1 = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

q1.question("Where do you live? ", function (answer) {
  console.log(`Oh, so you live in ${answer}`);
  console.log("Interface Closed");
  q1.close();
});
```
比想像中的還要麻煩欸，前端就用prompt就好了，不過這也是能夠運作的，那就好。

有個重點問題要注意，這似乎只能寫一次，不能重複使用==
如果宣告兩個q1這種東西，user在終端機打字的時候就會一次輸入兩個：如按下a變成aa這樣。

所以我後來也用上了文章中第二種方法。

```
const prompt = require('prompt-sync')();
```

這個的話要先npm：
```
npm install prompt-sync
```
剩下的看文章怎麼用，其實就跟prompt差不多了，挺好用的！爽。

## 使用者輸入bug

要把user給的數字拿去迴圈裡面當索引，要先數字化
使用parseInt方法即可。
```
parseInt(string)
```

## "判斷使用者輸入"的方法

我的目的是判斷使用者是否輸入all，如果是大寫也可以，這樣子的條件式。
透過GPT我整理了五種方法，最直覺的想法就是透過「或」。
或正則好像也滿直覺(?


[JS筆記-五種方法判斷input是否為"ALL"？](https://ithelp.ithome.com.tw/articles/10340342)


## 程式碼優化

### 函數拆分、封裝

![image](https://hackmd.io/_uploads/By4KHNs3a.png)

這些部分有相同重複的地方，原來可以把它們都拆分成function，一個再套一個。
真的乾淨很多！

另一個地方的函數封裝也是差不多的邏輯：
![image](https://hackmd.io/_uploads/BkSl8Vj2a.png)

我猜這樣寫應該也不是最乾淨最優雅，不過從我的code去改已經進展很多了XD

### 透過類別、原型鏈、或函數去封裝字串處理

原本的程式碼裡面有大概五個這個：
```
                      .replaceAll("：", "-")
                      .replaceAll(":", "-")
                      .replaceAll("*", "")
                      .replaceAll("/", "或")
                      .replaceAll(/[<>"\\|?]/g, "");
```

我知道可以包裝起來，直覺上我最喜歡的方式是使用原型鏈去處理。

1.原型鏈寫法：
```
 String.prototype.myFunction = function () {
  return this
    .replaceAll("：", "-")
    .replaceAll(":", "-")
    .replaceAll("*", "")
    .replaceAll("/", "或")
    .replaceAll(/[<>"\\|?]/g, "");
};

//使用方法：
const processedString = originalString.myFunction();
```

2.函數工廠
```
function createCustomReplacer(replaceConfig) {
  return function(input) {
    return replaceConfig.reduce((result, [search, replaceWith]) => {
      return result.replaceAll(search, replaceWith);
    }, input);
  };
}

const customReplace = createCustomReplacer([
  ["：", "-"],
  [":", "-"],
  ["*", ""],
  ["/", "或"],
  [/[<>"\\|?]/g, ""]
]);

//使用方法：
const processedString = customReplace(originalString);
```

這個滿神奇的還使用到redutce去跑不同字串取代。

3.類別


這我就不舉例了，再放文章裡面感覺太多，總之跟函數工廠是差不多的。

只是變成用class 、  constructor、    this    、new這些東西來做。

## ASNI文字藝術跑掉

使用trim修正。

![image](https://hackmd.io/_uploads/S1jDtVonT.png)




## 心得體悟

完成了，程式碼硬生生多了近兩百行@@，其中也修正的很辛苦。

中間處理"選擇下載章節"因為沒有把字串數字化，導致卡關一陣子，但由於過去被這種坑搞過很多次，
馬上就能猜到是沒有整數化，這種程度就是進步吧！
沒想到已經完成的程式，只是改寫一個小細節跟小新功能，就折騰一陣子，
這軟體屬實應該收費個五千XD 好啦我慢慢加強自己的功力

也要好好地把這些心得發表、分享出去，獲得更大影響力，也許更有助於學習成長呢。
