---
title: 切版JS練習-Animated Portfolio Website (個人網站+小動畫)

---

# 切版JS練習-Animated Portfolio Website (個人網站+小動畫)


## 前情提要

一樣是Online Tutorials的頻道教學，拿來練一下。

## 效果圖

![](https://i.imgur.com/kJ0IcyH.png)

## codepen link

https://codepen.io/flwrtykd-the-scripter/pen/qBMyabq


## 教學影片

<iframe width="560" height="315" src="https://www.youtube.com/embed/CI5vvYjRCVc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## 觀念筆記


### 夜間模式的設定


這是使用ionicons網站的圖示的，很好看。
```htmlmixed=
        <div class="dayNight">
            <ion-icon name="sunny-outline"></ion-icon>
            <ion-icon name="moon-outline"></ion-icon>        
        </div>
```

```css=
.banner.night .banner.night header .logo,
.banner.night .content h2,
.banner.night .content p,
.banner.night .content button.chat,
.banner.night .sci li a,
.banner.night  a{
    color: #fff;
}

```
就是選取所有文字給他變成白色，然後背景搞嘿。

### @keyframes-動畫效果的使用

設定關鍵字然後用@keyframes  以及幾%就可以調整CSS動畫。


```
    animation:  animateColor 6.5s ease-in infinite;

```


```css=
@keyframes animateColor{
    0%,100%{
        transform: translateX(0px);
    }
    50%{
        transform: translateX(-50px);
    }
}
```

### 元素選擇與層次管理

* 網頁結構中大量使用 `.content`、`.banner` 等 class 分層管理
* 利用 `position: absolute` + `z-index` 控制各元素出現的順序
* 背景圖、文字與按鈕皆有自己的動畫進場時間與透明度設定，營造簡約而有層次的視覺感受

---

### 延伸觀念：如何自行加入 RWD 響應式設計

* 原教學未包含完整 RWD，可手動加入：

  * `@media` queries：根據螢幕寬度調整 `font-size`、padding、flex 排版等
  * 文字避免使用絕對單位（如 `px`），可改用 `%`、`em`、`vw` 等相對單位
  * 用 `flex-wrap` 或 `grid` 重構排版，避免在小螢幕出現元素溢出

 
## 心得

效果還不錯，整體寫起來也滿簡單的，
延伸的重點應該是怎麼自己加上RWD效果( ิ◕㉨◕ ิ)。
未來可以加強這部分。


