# (58) Semantic Elements

## HTML Semantic Tags

例如 div 可以知道他可能佔據一個block ， 但要詳細看才知道。

因此 HTML5 開始，新增了 

- \<article>

- \<aside>

- \<details>

- \<figcaption>

- \<figure>

- \<footer>

- \<header>

- \<main>

- \<mark>

- \<nav>

- \<section>

- \<summary>

- \<time> 等等 semantic tags。

> Semantic elements = elements with a meaning.
> 
> 以上標籤本質上跟 div 一樣 只是更具形象意義

> **正確使用可以很好告訴團隊每個標籤作用與功能。** 
> 
> [HTML Semantic Elements (w3schools.com)](https://www.w3schools.com/html/html5_semantic_elements.asp) 

![](../../../Images/2023-12-02-19-32-11-image.png)

# (59) RWD設計

## 響應式網頁 Responsive Web Design

以前使用需要針對不同裝置進行不同的設計，目前主流則是 : 

- Flexbox 自動排版 不用再去對不同螢幕寬度作個別設定

- 元素、圖片皆使用相對單位，例如 rem、%、vh、vw調整大小防止跑版

## Flexbox

主要思想是讓容器能夠改變其項目的寬高、順序，最好地填充可用空間，適應各種設備。彈性容器擴展項目可填充可用的空間、或縮小他們以防溢出。

> [A Complete Guide to Flexbox | CSS-Tricks - CSS-Tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 

### Properties for the Parent ( flex container)

- display : flex 是一種 `inner display type`。

- 任何元素被定義為 display : flex 其內部元素皆為 `flex item` 

- `flex item` 可以定義 display : flex 。
  
  - 因此，元素可同時為 `flex item` 兼 flex-container <容器>。
  
  - 對於該元素`outer display type` 是 flex item  然而
    
    `inner display type`是 flex 。  `身 兼 兩 職`  
  
  ```html
  <div class="container">
        <div class="box box1">box1</div>
        <div class="box box2">box2</div>
        <div class="box box3">box3</div>
        <div class="box box4">box4</div>
        <div class="box box5">box5</div>
  </div>
  ```
  
  ```css
  /* ------------------------------ (59) flex ----------------------------- */
  div.container {
    border: 3px solid black;
    display: flex;
  }
  .box {
    width: 250px;
    height: 250px;
  }
  .box1 {background-color: coral;}
  .box2 {background-color: aquamarine;}
  .box3 {background-color: burlywood;}
  .box4 {background-color: goldenrod;}
  .box5 {background-color: thistle;}
  ```
  
  ![](../../../Images/2023-12-02-20-10-45-image.png)
  
  ![](../../../Images/2023-12-02-20-07-18-image.png)

- 比較兩個 會發現 flex 讓 容器變成橫的 而且 div.container 空間有效利用
  
  ![](../../../Images/2023-12-02-20-13-02-image.png)
  
  很酷。

> **接下來 演示 \<a> 變成 flex item 改變原本 inline element**<mark>不能更改 w,h 的特性變成可以改w,h。 </mark> 

- 原因就是它變成了 flex item 
  
  ```html
  <div class="container2">
    <a href="">oni</a>
    <a href="">umi</a>
    <a href="">corn</a>
  </div>
  ```
  
  ```css
  div.container2 {
    display: flex;
  }
  /* 直屬子標籤用 > 選擇 */
  div.container2 > a {
    color: goldenrod;
    /* %依舊不能用在高 */
    height: 35px;
    width: 15%;
    background-color: cadetblue;
    border: salmon solid 2px;
  }
  ```
  
  會發現原本明明不能改w,h的 a 突然可以改 ~ 因為他變成flex item
  
  ![](../../../Images/2023-12-02-20-37-23-image.png)

- `套娃`  
  
  ```html
  <div class="container">
    <div class="box box1">box1</div>
    <div class="box box2">
      <div class="smallbox">smallbox1</div>
      <div class="smallbox">smallbox2</div>
      <div class="smallbox">smallbox2</div>
    </div>
    <div class="box box3">box3</div>
    <div class="box box4">box4</div>
    <div class="box box5">box5</div>
  </div>
  ```
  
  ```css
  /* ------------------------------ smallbox in flex item----------------------------- */
  /* 拿掉flex 或說改成block(div預設) 可知有什麼不同 *//
  .box2 {
    display: flex;
  }
  .smallbox {
    border: 3px solid violet;
    height: 70px;
    width: 70px;
    /* w 90也會有所感悟 */
  }
  ```
  
  ![](../../../Images/2023-12-02-20-46-18-image.png)
  
  可以看出 裡面的flex item 原本預設也是block 但是能改flex

# (60) flex direction,flex-wrap

## flex-direction

可以設置item的排序方向 (main axis)

<img title="" src="../../../Images/2023-12-02-21-05-18-image.png" alt="" width="260">

- row - flex 橫向 ( 預設 )

- column-flex  直向

- row-reverse-flex 逆橫

- column-reverse-flex 逆直
  
  ```css
  /* ------------------------------ (60) flex-direction ----------------------------- */
  div.container {
    border: 3px solid black;
    display: flex;
    flex-direction: row-reverse;
  }
  ```
  
  ![](../../../Images/2023-12-02-21-10-13-image.png)

- 下面是拿掉flex-item = `.box{}`的高跟寬，則預設由其內容填充。
  
  `寬度` : 文字寬。
  
  `高度` : 裡面 smallbox 設定高度所以撐高了 div 所以就變成這樣。
  
  ![](../../../Images/2023-12-02-21-13-12-image.png)

## flex-wrap

- 預設no wrap ， 可以壓縮直到內容極限， 不會換行。

- 使用wrap 可以幫助我們換行。
  
  ```css
  div.container {
    border: 3px solid black;
    display: flex;
    /* flex-direction: row-reverse; */
    /* wrap 預設=no wrap */
    flex-wrap: wrap;
  }
  ```
  
  ![](../../../Images/2023-12-02-21-24-01-image.png)

# (61) justify-content, align-items

## justify-content

定義瀏覽器如何沿著flex container的 main axis在 flex items 分配空間

<img title="" src="../../../Images/2023-12-02-21-47-32-image.png" alt="" width="250">

- flex-start ( 預設值 )

- flex-end

- center

- space-between

- space-around

- space-evenly
  
  ```css
  /* ------------------------------ (61) justify-content ----------------------------- */
  div.container {
    height: 1500px;
    border: 3px solid black;
    display: flex;
    /* flex-direction: row-reverse; */
    /* wrap 預設=no wrap */
    flex-wrap: wrap;
    /* 預設 flex-start */
    /* justify-content: flex-start;  */
    /* justify-content: flex-end; */
    /* justify-content: center; */
    /* justify-content: space-between; */
    /* justify-content: space-around; */
    /* evenly 平均分配 */
    justify-content: space-evenly;
    flex-direction: column;
  }
  ```
  
  ![](../../../Images/2023-12-02-21-57-33-image.png)
  
  可以看出來 即使垂直，有預留空間分配也能使用 `justify-content`

## align-items

與 main axis 軸 `垂直的`另一軸上的對齊方式

<img src="../../../Images/2023-12-02-22-09-37-image.png" title="" alt="" width="300">

- stretch ( 預設值 )
  
  由於 `.box{}`  有設定高 所以不會被拉伸 ，先取消才會拉伸。
  
  且要水平才會拉伸效果，也就是flex-direction 為 row 之流派。
  
  ```css
  /* ------------------------------ (61) justify-content ----------------------------- */
  div.container {
    height: 1500px;
    border: 3px solid black;
    display: flex;
    /* flex-direction: row-reverse; */
    /* wrap 預設=no wrap */
    flex-wrap: wrap;
    /* 預設 flex-start */
    /* justify-content: flex-start;  */
    /* justify-content: flex-end; */
    /* justify-content: center; */
    /* justify-content: space-between; */
    /* justify-content: space-around; */
    /* evenly 平均分配 */
    justify-content: space-evenly;
    flex-direction: row;
    align-items: stretch;
  }
  .box {
    width: 250px;
    /* height: 250px; */
  }
  ```
  
  ![](../../../Images/2023-12-02-22-12-56-image.png)

- flex-start
  
  ```css
    ....
    justify-content: space-evenly;
    flex-direction: row;
    align-items: flex-start;
  }
    .box {
    width: 250px;
    /* height: 250px; */
  }
  ```
  
  ![](../../../Images/2023-12-02-22-14-25-image.png)

- flex-end
  
  ```css
    ...
    justify-content: space-evenly;
    flex-direction: row;
    align-items: flex-end;
  }
    .box {
    width: 250px;
    /* height: 250px; */
  }
  ```
  
  ![](../../../Images/2023-12-02-22-15-32-image.png)

- center
  
  ```css
     ...
    justify-content: space-evenly;
    flex-direction: row;
    align-items: center;
  }
    .box {
    width: 250px;
    /* height: 250px; */
  }
  ```
  
  ![](../../../Images/2023-12-02-22-16-46-image.png)

- baseline 
  
  🔥 <mark>以上我都並沒有還它們 `.box{}` 的高 ! </mark> 🔥
  
  ![](../../../Images/2023-12-02-22-17-49-image.png)
  
  ```css
  /* ------------------------------ (61) justify-content ----------------------------- */
  div.container {
    height: 1500px;
    border: 3px solid black;
    display: flex;
    /* flex-direction: row-reverse; */
    /* wrap 預設=no wrap */
    flex-wrap: wrap;
    /* 預設 flex-start */
    /* justify-content: flex-start;  */
    /* justify-content: flex-end; */
    /* justify-content: center; */
    /* justify-content: space-between; */
    /* justify-content: space-around; */
    /* evenly 平均分配 */
    justify-content: space-evenly;
    flex-direction: row;
    align-items: baseline;
  }
  .box {
    width: 250px;
    /* height: 250px; */
  }
  ```
  
  效果可以說跟flex-start 好像一致 ?
  
  ![](../../../Images/2023-12-02-22-20-53-image.png)
  
  > 決定讓紫色框框文字置中 透過transfrom => 失敗了、不會影響
  > 
  > rotate也無效 scale 也是
  
  ```css
  .box2 {
    display: flex;
    transform: translateY(50px);
  }
  ```
  
  ![](../../../Images/2023-12-02-22-27-58-image.png)
  
  <img src="../../../Images/2023-12-02-22-26-19-image.png" title="" alt="" width="316">
  
  🔥實際做一下如下，<mark>主要是因為smallbox的margin讓文字基準線往下</mark>🔥
  
  ```css
  .box2 {
    display: flex;
    /* transform: rotateX(180deg); */
    height: 300px;
  }
  .smallbox {
    margin-top: 100px;
    border: 3px solid violet;
    height: 70px;
    width: 70px;
  
    /* w 90也會有所感悟 */
  }
  ```
  
  ![](../../../Images/2023-12-02-23-39-00-image.png)
  
  <img src="../../../Images/2023-12-02-23-42-11-image.png" title="" alt="" width="265">
  
  🔥上面另外也提供 flex-direction : column 🔥
