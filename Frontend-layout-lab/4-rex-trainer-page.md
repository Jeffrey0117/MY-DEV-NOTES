---
title: '#JS實戰-Rex教練個人頁面'

---

# JS實戰-Rex教練個人頁面
## 前情提要

主動增加作品集的第二個素材，第一個是陳七星按摩，已經做得差不多也已經聯絡店家了。

我想要幫初心體能的REX教練作網站，屬於個人網頁。

這次我的想法是我切大概十來個版面給他選，而不是只有給一個版面然後討論洽談，感覺這樣子會更專業，也練習更多。
當然我不一定要攤開底牌說已經都做好做出來了，
可以預留一些手牌，拿出效果圖，然後自己成品不要先秀出也行。

未來練習目標，我想試著切版也結合vue，並且導入關於FB與line的社群的功能。

甚至是關於會員的後端，目前則是要先專注於切版：
設計好看程度也就是抄襲的能力XD，還有抄襲的速度，以及跟人洽談的能力等等……
以及把RWD的功力練到熟捻，游刃有餘不能讓人抓到跑版！

再來也有機會想觸碰到架站購買網域、下關鍵字、SEO等。
也許有機會我真的能成立工作室，或是進入到工作室當作前端切版師。


## 布局目標

swiper使用

修改多餘的程式碼

把名稱改對 放上連結等等



### IG下載工具
[IG下載影片圖片點這邊](https://snapinsta.app/tw-tw)


![](https://hackmd.io/_uploads/Byn85Ie92.png)


### CSS文字邊線:text-stroke

![](https://hackmd.io/_uploads/S1y9cIg5h.png)
```
h2 {
-webkit-text-stroke: 1px red;
color: blue;
}
```

### CSS圖片黑白:grayscale

![](https://hackmd.io/_uploads/HkxTcUe5n.png)
```
img {
  -webkit-filter: grayscale(100%); /* Safari 6.0 - 9.0 */
  filter: grayscale(100%);
}
```

### CSS高度用變數調整:calc


> There is a way to set max-height in %, but is here any way to set DIV max-height, so it would be 100px smaller than window height with only CSS?
> 
> It must not be a fixed layout, user must be able to scroll page vertically, but DIV always should be resized to window height - 100px. Is this possible, or will I have to use JS for that?


![](https://hackmd.io/_uploads/HJMgsLgch.png)

[問答頁面](https://stackoverflow.com/questions/29211998/make-div-max-height-equal-to-window-height-100px)

### bootstrap背景顏色:bg-danger

![](https://hackmd.io/_uploads/Hk9Hs8e52.png)
```
<div class="p-3 mb-2 bg-success text-white">.bg-success</div>
```


### CSS螢光筆效果

[點這邊看教學文章](https://shannonpayne.com.au/how-to-create-a-low-highlight-text-effect-using-css/)

![](https://hackmd.io/_uploads/BJEHBPxch.png)

![](https://hackmd.io/_uploads/SJlUVWbc3.png)

[超詳細螢光筆寫法教學 7種](https://www.coding-dude.com/wp/css/highlight-text-css/)

### CSS更粗的字體效果

![](https://hackmd.io/_uploads/S1xeNXWcn.png)


```
.extra-bold {
  text-shadow: 0px 1px, 1px 0px, 1px 1px;
}
```

### 真正好用的color-picker
![](https://hackmd.io/_uploads/BkFMNm-ch.png)


[從這邊下載](https://chrome.google.com/webstore/detail/snap-color-picker/nbpljhppefmpifoffhhmllmacfdckokh/related)

其他的有的沒有效果或是不好用==
這個超屌超讚。


### 好評推薦-Testimonials

![](https://hackmd.io/_uploads/By-X9Ebcn.png)

[W3C的頁面點這邊進去，雖然寫的超醜根本不需要看XD](https://www.w3schools.com/howto/howto_css_testimonials.asp)


![](https://hackmd.io/_uploads/Hy9wqVWc2.png)


[整理的超棒的文章點這邊看，裡面範例可以下載跟查看code](https://freefrontend.com/css-testimonials/)

![](https://hackmd.io/_uploads/SkPp5VZ93.png)


真的run out of idea的時候可以直接開codepen去搜
找Testimonials就可以

不過我覺得還是用google 配上html或layout關鍵字去找
會有一堆整理網站 資源更多  有些可以用看的
然後拷貝忍者卡卡西 自己幹一個出來XD

### CSS用陰影做出底線效果(純bottom的陰影)


![](https://hackmd.io/_uploads/r1YImLZ93.png)

```
box-shadow: 0 4px 2px -2px gray;
```

[stackoverflow問答文章點這邊](https://stackoverflow.com/questions/4561097/css-box-shadow-bottom-only)
### CSS的a tag不要有顏色

```
a { color: inherit; } 
```
> How do I remove the default link color of the html hyperlink 'a' tag?







## 等等影片要練的東西在這邊
![](https://hackmd.io/_uploads/S1fz3Nbqn.png)
去骨歌testimonal html


## 這個可以練
![](https://hackmd.io/_uploads/r1ifM8b53.png)

https://freefrontend.com/