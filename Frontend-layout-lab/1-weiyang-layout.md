---
title: Html 切版練習 - 未央網站

---

# Html 切版練習 - 未央網站

## 前情提要

這次練習的主題是「未央網站」的切版仿作，目標是透過實際模仿設計師完成過的商業網站，來練習 HTML/CSS 的排版技巧與設計細節的還原。

影片來自一位教學型 YouTuber，手把手示範整個排版與設計結構，從 banner 到內頁，包含圖片的使用、文字排版、顏色對比等，整體風格清新、有質感，適合新手實作練手感。

---

## 學習影片

<iframe width="560" height="315" src="https://www.youtube.com/embed/8u-nhouM9cw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

---

## 成果圖

首頁視覺（含輪播 / Hero 圖）
![](https://i.imgur.com/bgARRvS.png)

內頁布局（服務介紹）
![](https://i.imgur.com/2bF7i6G.png)

---

## 重點筆記

### 1. 主視覺區塊（Hero Banner）

* 背景圖使用 `background-size: cover;`
* 文字區域做 `absolute` 定位在中間位置
* 使用透明背景黑底提升字體可讀性

### 2. Section 區塊分割

* 每個 section 有自己的背景色或間距處理
* 使用 padding / margin 撐出閱讀節奏
* 注意多欄版面如何設計 RWD（ex: flex / grid）

### 3. 字型 & 色彩風格

* 採用淡雅配色（米色、灰藍）
* 標題使用 serif 字型，內文則用 sans-serif，呈現品牌感
* hover 效果簡潔，主要用於圖片或按鈕變暗、放大等

### 4. 圖片處理

* 圓角處理：`border-radius`
* 圖文比例一致，避免變形
* 圖片 hover 加上陰影或縮放效果，提升視覺感受

### 5. RWD 響應式設計

* 切換手機版時，`flex-direction: column`
* Menu 會改為漢堡選單（但影片未實作完整 js 行為，可後續補強）
* 使用 `@media` query 控制版面在不同裝置下的斷點

---

## 心得

這次練習看似簡單，但細節很多，是對切版功力的一次完整測驗。

從整體排版、元素擺放到細部的 hover 效果，處處講究「乾淨」、「一致性」與「層次感」。

過程中最有收穫的是對 section 分段的掌握，學會了使用 `padding` 來營造空氣感，也更注意到字型與行距的重要性。

這類設計公司作品，風格較生活美感導向，非常適合練習「品牌感」與「非工程師風格」的視覺詮釋。

---

## 他自己的成果（原站）

![](https://i.imgur.com/ELCrs2s.png)

網站連結：👉 [https://peaknotes.net/](https://peaknotes.net/)

這是影片作者最後切好的 demo 網站，可以參考他的版型還原度與細節處理，也能跟自己的作品對照比較學習。

---

## 它的設計廠商：米洛設計

官方網站：👉 [https://www.webtech.com.tw/project.php](https://www.webtech.com.tw/project.php)

這家公司承接了許多風格清新、有品牌力的中小企業網站，可以作為切版練習的靈感來源，也值得觀摩他們如何規劃區塊與設計細節。

