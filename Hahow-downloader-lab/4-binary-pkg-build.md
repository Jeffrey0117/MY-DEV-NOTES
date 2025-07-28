---
title: JS筆記-關於打包pkg以及相關知識

---

# JS筆記-打包pkg教學以及相關知識(一)

## 前情提要

[JS筆記-解雷指南：pkg的技巧整理、坑點](https://hackmd.io/EB_W2KSmSim2V1Q6CFdW8A)


## 觀念說明

pkg打包是讓程式變成二進位，把原本是需要nodejs環境的程式，丟給外行人也可以跑。
這樣就不用花時間下載nodeJS以及更改環境變數等，
是一個還不錯的打包套件。

關於打包的概念，它就是把你用到的框架或第三方庫，都寫進來，
所以你最後只需要一支檔案就可以運作，不用去呼叫其他的程式、依賴別人。

## 另一個打包套件:rollup.js 

[阮一峰:打包工具 rollup.js 入门教程](https://www.ruanyifeng.com/blog/2022/05/rollup.html)

下次有空研究看看。

## 認識機緣

[把 Node.js 專案打包成執行檔，讓朋友無須安裝環境就能暢快體驗 — pkg的介紹以及實際範例分享](https://medium.com/dean-lin/%E6%8A%8A-node-js-%E5%B0%88%E6%A1%88%E6%89%93%E5%8C%85%E6%88%90%E5%9F%B7%E8%A1%8C%E6%AA%94-%E8%AE%93%E6%9C%8B%E5%8F%8B%E7%84%A1%E9%A0%88%E5%AE%89%E8%A3%9D%E7%92%B0%E5%A2%83%E5%B0%B1%E8%83%BD%E6%9A%A2%E5%BF%AB%E9%AB%94%E9%A9%97-pkg%E7%9A%84%E4%BB%8B%E7%B4%B9%E4%BB%A5%E5%8F%8A%E5%AF%A6%E9%9A%9B%E7%AF%84%E4%BE%8B%E5%88%86%E4%BA%AB-72a9a383942b)

![image](https://hackmd.io/_uploads/SJlfoyFn6.png)

這篇文章推坑的

網路上搜尋也會找到pkg這個東西，看來是滿火熱的：
[Node.js 项目打包为 exe 可执行文件](https://www.misterma.com/archives/936/)

## 打包的例子，具體說明保證看懂！

打包前：
```javascript
// 文件: main.js
import { add } from './math';  // 引入自定义模块

// 使用第三方库
import _ from 'lodash';

// 业务逻辑
const result = add(5, 3);
console.log(result);

// 使用 lodash 进行一些操作
const array = [1, 2, 3, 4, 5];
const sum = _.sum(array);
console.log(sum);
```

```javascript
// 文件: math.js
export function add(a, b) {
  return a + b;
}
```

這兩支檔案就可以打包成下面一支：

```javascript
// 打包后的文件
// 由工具自动生成，包含了 main.js 和 math.js 的代码
(function() {
  // 打包后的 main.js 代码
  // ...

  // 打包后的 lodash 代码
  // ...
})();
```

同時這也可以加速運作，因為減少加載別的依賴庫的時間。

## 使用方法
![image](https://hackmd.io/_uploads/HyA1nj_h6.png)

[使用 pkg 將 Node 專案打包成二進制執行檔
](https://blog.epoch.tw/2022/10/%E4%BD%BF%E7%94%A8-pkg-%E5%B0%87-Node-%E5%B0%88%E6%A1%88%E6%89%93%E5%8C%85%E6%88%90%E4%BA%8C%E9%80%B2%E5%88%B6%E5%9F%B7%E8%A1%8C%E6%AA%94/)


### 什麼是package


來自一個node系列文的教學：

![image](https://hackmd.io/_uploads/SJ-IhoO3T.png)

整理得還算不錯，有興趣要研究去看看應該很有幫助。

[使用 package.json 安裝、管理模組](https://ithelp.ithome.com.tw/articles/10158140)

基本上就是每次寫nodeJS的時候，先運作的npm init，
它會有一個json檔案把你所有需要依賴的套件記錄起來，有助於之後打包、發送給別人使用參考。

