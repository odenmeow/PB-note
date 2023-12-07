# (84-85) 跳過 簡介而已

# (86) 網站導覽列製作

製作的時候需要用到 nav 的 圖案 去 font awesome

> [font-awesome - Libraries - cdnjs - The #1 free and open source CDN built to make life easier for developers](https://cdnjs.com/libraries/font-awesome) 

💡 貼在自己的 stylesheet 上方 避免覆蓋我們寫的

```html
<link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"
    integrity="sha512-DTOQO9RWCH3ppGqcWaEA1BIZOC6xxalwEsw9c2QQeAIftl+Vegovlnee1c9QX4TctnWMn13TZye+giMm8e2LwA=="
    crossorigin="anonymous"
    referrerpolicy="no-referrer"
/>
<link rel="stylesheet" href="./styles/style.css" />
```

去網站找自己喜歡的小圖來用

> [Find Icons with the Perfect Look & Feel | Font Awesome](https://fontawesome.com/search?q=play&o=r) 

```html
<i class="fa-solid fa-plane"></i> 得到這個飛機圖
```

- book

- address book 之類?   自己找喜歡的就對了 

接下來只記錄有意思的地方。 基本上就是開始用運 sass 的方便性來寫code 

`align-items` : center  讓小圖跟文字在同一個水平線上

![](../../../Images/2023-12-07-16-59-31-image.png)

header 忘了使用 align item  所以右邊文字沒有對齊中間 也就是說

header  包起來的section logo 跟 nav 沒有在 container header 的中間 

左邊負責撐大 右邊如果沒對齊 自然會往上跑 就不好看了。 

# (87) 圖片轉換功能

- ```scss
  main {
    section.background-img {
      //設定背景圖片
      min-height: 70vh;
      background-image: url(../images/日本櫻花.jpg);
      background-size: cover;
      background-position: center;
      transition: all 0.75s ease;
    }
  }
  ```

- 💡 `center` 差異如下 也就是使用圖片正中間進行縮放而非等比例放大之類
  
  ![](../../../Images/2023-12-07-18-42-12-image.png)
  
  ![](../../../Images/2023-12-07-18-42-30-image.png)

- ```scss
  position: relative;
  z-index: 0; 🔥relative且 z-index 非auto🔥
               => 形成stacking context
  ```

- 請參考之前CH3 - CSS
  
  ![](../../../Images/2023-12-07-18-48-48-image.png)

- ⚠️重點在於 
  
  div.filter 使用 position: absolute ；
  
  section.backgorund-img 使用 relative。
  
  ```scss
  position: relative;
      z-index: 0; //relative+index非auto => 形成stacking context
      div.filter {
        background-color: rgba(0, 0, 0, 0.5);
        width: 100%;
        min-height: 70vh;
        position: absolute;
        top: 0;
        left: 0;
        z-index: -1;
      }
  ```
  
  所以寬高才能跟 外圍標籤 section 一樣。

- 接著是輪播圖  但是我一直失敗 🔥原因如下
  
  ```html
  <div id="myDiv" style="background-image: url(./images/北海道雪景.jpg)">
    aaaaaaa
  </div>
  <script>
    console.log("測試測試");
    let kaiddo = document.getElementById("myDiv");
    if (kaiddo.style.backgroundImage == `url("./images/北海道雪景.jpg")`) {
      console.log("雪耶");
      kaiddo.style.backgroundImage = "url(./images/京都古城.jpg)";
    }
    console.log(document.getElementById("myDiv").style.backgroundImage);
  ```
  
  ![](../../../Images/2023-12-07-19-44-12-image.png) 
  
  發現 雖然我圖片是css設定的 路徑是 ../images/日本櫻花.jpg 
  
  💡這邊跟我說的卻是  ./  ，因此我可以直接比較文字 (src) ，不用考慮相對路徑問題!

- 不管使用的是 
  
  ```html
  ` 或者 '  都可以 但是 url ("   ") 一定要是 "" 
  ```
  
  ```html
  <script>
        setInterval(() => {
          let topBackgorund = document.querySelector("section.background-img");
  
          if (
            topBackgorund.style.backgroundImage == 'url("./images/日本櫻花.jpg")'
          ) {
            topBackgorund.style.backgroundImage = 'url("./images/京都古城.jpg")';
          } else if (
            topBackgorund.style.backgroundImage == 'url("./images/京都古城.jpg")'
          ) {
            topBackgorund.style.backgroundImage = 'url("./images/大阪街頭.jpg")';
          } else {
            topBackgorund.style.backgroundImage = 'url("./images/日本櫻花.jpg")';
          }
        }, 3000);
  </script>
  ```

# (88) 圓圈圖片 效果

- 只提幾個遇到個小bug 

```css
align-items: center;
justify-content: center;
img {
     width: 80%;
}
```

align-content 會無法垂直於main aixs 搞置中。

不要跟上面搞混囉。

# (89) 透明背景設定與Google地圖

- 放在 header 上面， \<body> 正下方 富士山2.jpg
  
  ![](../../../Images/2023-12-07-21-48-46-image.png)
  
  又是 specificity在搞我，因為
  
  ```scss
  @media screen and (max-width: 1024px) {
    body {
      header {
        flex-wrap: wrap;
        section.logo {
          flex: 2 1 600px;
        }
        nav {
          flex: 5 1 500px;
          ul {
            flex-direction: column;
  
            li {
            }
          }
        }
      }
    }
  }
  ```
  
  💡 原本寫的特異度不夠，上面寫的時候有body @media screen 沒寫到就無法覆蓋🙄

# (90) Footer設定




