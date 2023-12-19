# (190) Execution Context

## 執行環境

- 執行腳本時 創建兩種執行環境

### Global Execution Context

- 執行的時候 會創造第一種 execution context
  
  其內部會進入`creation phase`
  
  1. 創建 global object
  
  2. 建立scope 
  
  3. 創建this關鍵字 ，並被綁定至global object
  
  4. variables、class、function 分配至記憶體 (hoisting步驟)
  
  結束後 進入 execution phase

### Function Execution Context

- 執行的時候也會創造第二種 execution context 也就是function execution context，非常類似，一樣有creation / execution phase。

- 差別在於 函數執行環境 不創建global object，而是創建argument object。

- Argument Object包含 放入此函數的 parameters的參照值
  
  ( a reference to all the parameters passed into the function )

- `creation phase` 
  
  1. 創建 argument object
  
  2. 建立 scope 依照closure 準則
  
  3. 創建this 關鍵字 
     
     - 可能指向window 
     
     - 也可能指向object  若忘記可以翻一下筆記。
  
  4. 將 variables、class、function 分配至記憶體 (hoisting步驟)

### 各自包含兩種階段:

#### creation phase

- 創建 global /argument  object

- 建立scope

- 創建this關鍵字 ，並被綁定至global object

- variables、class、function 分配至記憶體 (hoisting步驟)

#### execution phase

- 逐行 line by line 執行程式碼

- 遇到遞迴時，使用call stack 來排定工作順序。

## 主要是這張圖 😕

![](../../../Images/2023-12-19-22-56-25-image.png)

# 

# (191) Hoisting

## 解釋作用

JS Engine 執行 code之前，將 function variables class的宣告移動到其範圍頂部的過程。

- 可先執行就是因為有hoisting
  
  ```js
  hello();
  function hello() {
    console.log("hello");
  }
  ```

## 適用函數 、變數

只有declaration 被提升，不包含initialization ! 

let x=10 ; 只有 let x 會被 hoisting 

所以會被警告需要初始化 。

## Hoisting時期

### var 比較特殊，有預設undefined

它 會有預設 undefined 而不會被警告要初始化 所以要小心  

### let const沒有預設undefined

### 但是 如果不是hoisting時期⚠️

- 先宣告w 在印出w 會得到undefined⚠️
  
  ```js
  // let w
  let w;
  console.log(w); //並不是hoisting 階段產生 而是executionPhase happens in the execution phase)
  ```




