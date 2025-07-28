# gsap 套件練習 & js 練習-車子網頁

## 前情提要

這次練習的目標是熟悉使用 GSAP（GreenSock Animation Platform）這套動畫函式庫，透過一個「車子主題」的網頁動畫專案進行練習，學習如何透過 `timeline` 控制動畫流程，並搭配 HTML/CSS 進行視覺呈現。

這支教學影片提供了具體範例，包含圖層進場動畫、圖片滑入、LOGO 和選單淡入等效果，對初學者來說是一個很好的切入點。

這類動畫效果常應用在品牌頁、Landing Page、作品集網站等，可以讓網站更具吸引力與動態感。

---

## 影片教學

<iframe width="560" height="315" src="https://www.youtube.com/embed/FiZpZOrWn4s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

---

## 效果圖

![](https://i.imgur.com/VQcRGSV.png)
畫面會出現一台車的主視覺動畫，滑入感順暢，動畫節奏掌握得滿好。整體看起來簡潔又有質感。

---

## codepen

🔗 [Demo 練習連結](https://codepen.io/flwrtykd-the-scripter/pen/poOxGNz)
可以直接 fork 一份來玩看看，也能透過 dev tools 看實際 DOM 結構怎麼寫。

---

## GSAP 相關資源包

- [使用 GSAP 來搞定動畫與特效](https://www.astralweb.com.tw/use-gsap-to-create-animations-and-effects/)
- [Getting Started with GSAP](https://greensock.com/get-started/)
- [JS Library 學習筆記：嘿！有聽過 GSAP 嗎？ (一)](https://ithelp.ithome.com.tw/articles/10272580?sc=iThomeR)

這些資源包含了 GSAP 的起手式、基本動畫語法、timeline 與 scrolltrigger 等延伸功能。

---

## 觀念筆記

### menu 的使用

動畫中會用到一個「menu 圖片」，是透過圖片素材來模擬 menu icon：

```
https://raw.githubusercontent.com/Shin-sibainu/animation-website-youtube/main/images/menu.png
```

之後如果不想自己畫，可以直接複製這種素材來用，或是改用 SVG / font icon（像 FontAwesome）來做會更彈性。

---

## GSAP 的使用教學(API)

- [官方 API 清單](https://greensock.com/docs/v3/GSAP/Timeline)

![](https://i.imgur.com/McKhQoW.png)

GSAP 的 `.fromTo()` 方法是這次的主要用法，它可以設定動畫的起點與終點，並且搭配 `ease` 控制速度變化的曲線，讓動畫更有節奏感。

---

## JS 程式碼部分

```javascript
const tl = gsap.timeline();

tl.fromTo(hero, 1, { height: "0%" }, { height: "80%", ease: Power2.easeInOut })
  .fromTo(
    hero,
    1.2,
    { width: "100%" },
    { width: "80%", ease: Power2.easeInOut }
  )
  .fromTo(
    slider,
    1.2,
    { y: "-100%" },
    { y: "0%", ease: Power2.easeInOut },
    "-=1.2"
  )
  .fromTo(logo, 0.5, { opacity: 0, x: 30 }, { opacity: 1, x: 0 }, "-=0.5")
  .fromTo(menu, 0.5, { opacity: 0, x: 30 }, { opacity: 1, x: 0 }, "-=0.5")
  .fromTo(headline, 0.5, { opacity: 0, x: 0 }, { opacity: 1, x: 30 }, "-=0.5");
```

這段 code 使用 `timeline` 的好處是可以串接動畫，每一段可以設定相對延遲時間（例如 `"-=0.5"` 表示前一段還沒結束就開始下一段），整體過程會更流暢。

---

## 心得

雖然一開始會被 `.fromTo()` 的語法搞混，但實際玩幾次後就很直覺。比起用 CSS 做複雜動畫，GSAP 更有結構與彈性，也可以搭配 scroll、click 等事件做出互動效果。

像以前玩 swiper.js 是做滑動 UI，GSAP 則是更自由的動畫控制。兩者搭配起來會很強大。

這種工具熟練之後，能在作品集中做出「有感」的動畫效果，對求職或接案都有加分。
