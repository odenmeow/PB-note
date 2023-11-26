# DOM Tree

![](C:\MyNote\Images\2023-11-26-18-02-18-image.png)

# CSS簡介

- Cascading Style Sheet 階層式樣式表

- [CSS reference - CSS: Cascading Style Sheets | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)
  
  不用全部屬性都知道 常用跟實用就夠

- <font style="color:lightgreen"> 還是直接拿上次故宮的例子接下去做@@ "</font> 

- `我決定新開CH3 複製品 、比較值觀方便` ，依舊會做Git 版控 : )

- comment 為 `/*....*/` 

## 取名方式不同 我叫GuGon 它叫index

- 自己注意 雖然我不知道會不會有問題、留意應該就ok !

# CSS放置位置

- <font style="color:lightgreen"> 查看方式F12 點選要查看物件 > 樣式 ~~blue~~ 的可知效果、順序</font>

- `inline styling`
  
   <font style="color:lightgreen"> 優先最高</font>但只能對特定標籤設定 最優先
  
  ```html
  <body>
      <h1 style="color: rgb(0, 118, 118)">國立故宮博物院</h1>
      <style>
        h1 , h2{
          color: blueviolet;   
        }
      </style>
  ```
  
  這邊 inline 不會被下面的style影響 但 沒寫=原本套用head style
  的h2藍色標題們 會變成紫羅色 !

- `internal styling`
  
  方便 但是難維護
  
  CSS 直接放在 HTML 中 
  
  ```html
  <style>
  h1 {
          color: red;
  }
  h2 {
          color: blue;
  }
  </style>
  ```

- `external styling`
  
  最常見、容易維護
  
  CSS 放外部 可以讓別的 HTML 也使用 !
  
  基本上都命名為 `style.css` 
  
  ```html
       ....
       <link rel="stylesheet" href="./style.css" />
  
   </head>
  ```
  
  使用外部CSS  連結的方式

# CSS顏色設定

- `<named-color>`[ CSS: Cascading Style Sheets | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/CSS/named-color) 

- Color Keyword: red、 black、coral....etc.

- RGB 
  
  - 0~255 共256 ^3   
  
  - 顏色通道 channel 每個顏色   `1byte` 儲存
  
  - ```css
    h1 {
      color: rgb(22, 355, 19);
    }
    ```

- RGBA
  
  - 同RGB但多了alpha 儲存透明度  `0-1`  表示 
  
  - 如下，接近亮綠
  
  - ```css
    h1 {
      color: rgba(22, 355, 19,0.5);
    }
    ```

- HEX
  
  - 使用十六進制  0-F 
  
  - 如下，淺灰
    
    ```css
    h1 {
      color: #AABBCC;
    }
    ```

- HSL 
  
  - Hue 色相
  
  - Saturation 飽和度
  
  - Brightness 亮度

# CSS Selectors 1

- Universal Selector (*) 匹配任何類型的HTML element。
  
  - ```css
    h1 {
      color: #aabbcc;
    }
    h2 {
      color: blue;
    }
    * {
      color: yellowgreen;
    }
    ```
  
  - 除h1,h2,以及`inlineStyle`，其他 HTML element都變黃綠色。

- Element Selector 可選擇特定的 HTML element。
  
  - ```css
    h2 {
      color: blue;
    }
    ```

- ID Selector可選擇有特定ID屬性的HTML element。
  
  - ```css
    #first-paragraph {
      color: coral;
    }
    ```

- Class Selector可選擇所有有特定class屬性的HTML element。
  
  - ```css
    .b-text {
      color: blue;
    }
    ```
  
  - 跟ID最大不同就是可以重複寫上去
  
  - space-separated "A-1 B-2"  代表有兩個類別 `A-1` `B-2` 
  
  - ```html
     <p id="first-paragraph" class="b-text large-text" onclick="alert('你按了<p>')">
    ```

- 使用標籤+類別如下
  
  - ```html
     <a class="large-text" href="https://www.npm.gov.tw/"> 故宮網站連結</a>
    ```
  
  - ```css
    a.large-text {
      font-size: 35px;
    }
    ```
  
  - 滑鼠移上去 就可以知道是不是真的套用到`<a class="large-text">`

# CSS Selectors 2

- Grouping Selector 可一次選擇所有數個 HTML 元素,並以逗號分隔。

- Descendant Selector 由兩個或多個用空格分隔的選擇器組成。

- Attribute Selector 選擇所有具有相同屬性的HTML 元素。
