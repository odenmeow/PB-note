# (171) 資源包-圖片下載

# (172) Project介紹

## 需要添加兩個連結link

### font-awesome 圖片Icon

> font-awesome cdn 

- 選擇all
  
  ![](../../../Images/2023-12-16-13-45-35-image.png)

### google 字體

> [Baloo 2 - Google Fonts](https://fonts.google.com/specimen/Baloo+2?query=balo+2) 

- 改變區域 從大陸改為 美國
  
  <img title="" src="../../../Images/2023-12-16-13-58-26-image.png" alt="" width="301">

- 選擇400的那一個，然後selected family 這邊記得把之前的 ( 之前上課的瀏覽器自己默默記住)，刪除，用不到Noto Sans 。
  
  <img title="" src="../../../Images/2023-12-16-13-59-51-image.png" alt="" width="182">

- 留下 Baloo2 就好
  
  <img src="../../../Images/2023-12-16-14-01-43-image.png" title="" alt="" width="214">

## 製作外觀

- `datalist`去已完成的檔案那邊複製就好。
  
  ```html
  ....
  </main>
      <datalist id="opt">
        <option value="ACCT">Accounting</option>
        <option value="ASL">American Sign Language</option>
        <option value="ANTH">Anthropology</option>
  ```
  
  然後再去把input 修改一下
  
  ```html
  <form>
       <div class="grader">
           <input
              type="text"
              class="class-type"
              placeholder="class category"
              list="opt"   ------------------> 修改這一個🔥
           />
  ```

- `credit` input 後面再加上`select` ，也可以複製 `選單`  【A 沒A+ 、F只有一個F】
  
  ```html
  <select name="select" class="select">
                  <option value=""></option>
                  <option value="A">A</option>
                  <option value="A-">A-</option>
                  <option value="B+">B+</option>
                  <option value="B">B</option>
                                  ...略
  
  </select>
  <button class="trash-btn">
                  <i class="fas fa-trash"></i>
  </button>
  ```

### 關於對齊他使用 text-align...?

```scss
main {
    section.main-part {
      padding: 1rem;
      display: flex;
      flex-direction: column;
      text-align: center;
    }
  }
```

- 比起 justify-content 和 align-items 他選擇 上面這樣 why?

### pointer-events:none💡

- 讓 滑鼠懸浮可以被得知 ( 雖然z-index 遮住了 但底層可以感知上面有滑鼠經過 )
  
     🔥如果上面有人遮擋住(透明 mask)、一樣可以點垃圾桶 🔥
  
  ```scss
  button {
      border: none;
      cursor: pointer;
      background-color: #272727;
      i {
      font-size: 1.25rem;
      pointer-events: none; //允許滑鼠穿透到下面元素
      // 如果上面有人遮擋住(透明 mask)、一樣可以點垃圾桶 !
      color: white;
      }
  }
  ```

### div.all-input下 input.class-credit 15%問題

- 如果使用edge 則會導致跑版
  
  明明有空間
  
  ![](../../../Images/2023-12-16-16-00-16-image.png)

- code
  
  ```scss
  div.all-inputs {
   width: 100%;----------------->加上100%就可以
          input,
          select {
            font-size: 1.05rem;
            padding: 0.5rem;
            border: 0.25px solid rgb(39, 39, 21);
            margin: 0.35rem;
            border-radius: 0.25rem;
          }
          input.class-credit {
            width: 15%;
          }
  ```

- 有人發問，解答是 瀏覽器計算空間量不同 所導致
  
  ![](../../../Images/2023-12-16-15-56-43-image.png)
  
  ![](../../../Images/2023-12-16-16-00-43-image.png)
  
  #### 改好100%就沒事了@^@ 。😕

- 記得justify align text-align 都要弄一下各有優點
  
  ```scss
  main {
      section.main-part {
        padding: 1rem;
        display: flex;
        flex-direction: column;
        justify-content: center;   --------> 加入這個
        align-items: center;       --------> 這個
        text-align: center;        --------> 這個
  ```

### 回憶動畫製作

- animation
  
  ```scss
  div.result {
          width: 200px;
          height: 200px;
          border: 3px solid red;
          border-radius: 50%;
          text-align: center;
          animation-name: border_color;
          animation-duration: 8s;
          animation-iteration-count: infinite;
          }
  ..
  ...
  @keyframes border_color {
    0% {
      border-color: red;
    }
    33% {
      border-color: limegreen;
    }
    66% {
      border-color: yellow;
    }
  }
  ```

```
### 回憶selector同時兩個條件

- 選擇器滿足兩個條件的話

```scss
h2#result-gpa {
          font-size: 3.5rem;
          font-weight: bold;
        }
```

  不需要空格，直接連著寫就行 !

## 不用太在意咖啡色點點

- 資料夾旁邊那個 git 說Contains Emphasized items 。
  
  <img title="" src="../../../Images/2023-12-16-22-51-40-image.png" alt="" width="266">

- 是因為我scss留白沒填東西 被警告而已
  
  <img src="../../../Images/2023-12-16-22-52-09-image.png" title="" alt="" width="280">

# (173) 開場動畫製作

## gsap介紹+cdnjs

> [gsap - Libraries - cdnjs - The #1 free and open source CDN built to make life easier for developers](https://cdnjs.com/libraries/gsap/2.1.3) 

- 選擇 TimelineMax跟TweenMax
  
  ![](../../../Images/2023-12-16-23-27-24-image.png)

- 講解 HTML 設計理念，看影片7:00或者實操比較能了解。
  
  ```html
    <section class="animation-wrapper">
        <section class="animation">
          <div class="hero">
            <img src="./images/math.jpg" alt="math image" />
          </div>
        </section>
        <div class="slider"></div>
      </section>
  ```

## 💡小提醒，永遠記得Watch Sass 以及tag層級。

## 幹勒，一直忘記= =

## 關於圖片的部分

### object-Fit mdn

> [object-fit - CSS：层叠样式表 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 

- 選cover
  
  ![](../../../Images/2023-12-16-23-52-10-image.png)
  
  ```scss
  .hero {
          // border: 5px solid blue;
          width: 100%;
          height: 0%;
          img {
            width: 100%;
            height: 100%;
            object-fit: cover;
          }
        }
  ```

## slider使用了linear-gradient()💡😕😕

- ```scss
  .slider {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100vh;
        background-color: linear-gradient(to right, rgb(144, 144, 144), black);
        z-index: -1;
      }
  ```

- #### 不是 bg color 是 background
  
  ```js
  background: linear-gradient(to right, rgb(144, 144, 144), black);
  ```

## JavaScript

### 1. 套件提供的功能

- 動畫套件
  
  ```js
  let hero = document.querySelector(".hero");
  let slider = document.querySelector(".slider");
  let animation = document.querySelector("section.animation-wrapper");
  const time_line = new TimelineMax();
  //param 1是要控制的對象
  //param 2是duration
  //param 3是控制對象的原始狀態
  //param 4是控制對象的動畫結束後狀態
  time_line.fromTo(
    hero,
    1,
    { height: "0%" },
    { height: "100%", ease: Power2.easeInOut } // 套件提供的功能
  );
  ```

### 2. 比較有意思的⭐⭐⭐

#### setTimeout

#### animation.style.pointerEvents="none" 點擊後面

#### 可以參考前面167💡

- 結束長這樣
  
  ```js
  let hero = document.querySelector(".hero");
  let slider = document.querySelector(".slider");
  let animation = document.querySelector("section.animation-wrapper");
  
  const time_line = new TimelineMax();
  
  //param 1是要控制的對象
  //param 2是duration
  //param 3是控制對象的原始狀態
  //param 4是控制對象的動畫結束後狀態
  //param 5提早進場
  time_line
    .fromTo(
      hero,
      1,
      { height: "0%" },
      { height: "100%", ease: Power2.easeInOut } // 套件提供的功能
    )
    .fromTo(hero, 0.8, { width: "80%" }, { width: "100%" })
    .fromTo(
      slider,
      1,
      { x: "-100%" },
      { x: "0%", ease: Power2.easeInOut },
      "-=0.8"
    )
    .fromTo(animation, 0.3, { opacity: 1 }, { opacity: 0 });//透明化
  
  window.setTimeout(() => {
    animation.style.pointerEvents = "none"; //點擊後面
  }, 2100);
  ```
