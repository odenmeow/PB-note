# (106) JS 簡介

## 名稱由來

- 最初叫做LiveScript 因 Java流行所以才叫做 JavaScript 

## ECMA Script

- European computer manufactures association

## Vanilla JavaScript

- 純 JavaScript 而不需要 任何額外 library或框架。

- 常見的 JavaScript library 有 jQuery。

- 瀏覽器有各自的 JS Engine

## 查詢Caniuse...

> [Can I use... Support tables for HTML5, CSS3, etc](https://caniuse.com/) 

- 透過上述網站 查功能、那些瀏覽器支援
  
  ![](../../../Images/2023-12-09-20-40-10-image.png)

## <script>放哪

- 最下方、讓頁面最後才執行、用戶比較留得住。

# (107) Lexical Structure

## 常見 JS 函數

### console.log()

### window.alert()

- 指示瀏覽器顯示帶有可選消息的對話框、並等待用戶關閉對話框。

### window.prompt()

- 指示瀏覽器顯示帶有可選消息的對話框、等待用戶提交或關閉。

## 設定Snippet

```json
"console":{
        "prefix":"con",
        "body":[
            "console.log()"
        ],
        "description":"console"
}
```

- 方便以後快速打出console.log()

## Lexical Structure JS語法規則

### Case Sensitive

### 空白、換行會被忽略 ( minification)

- 減少 JS 大小。

- 不是針對字串所言 而是語言code本身 被讀取的時候 普通coding 會被minification ，除非使用字串之類。

- 也就是說console.log("Hello     a~"); 不會縮

### 註解使用 【//】    【/\*\*/】

### 變數開頭要是 _ 或 $ 或英文

- 其他符號 跟 0~9不能當開頭

### 關鍵字

null of if then in finally for while break continue switch try let const var ...不能當變數名稱。

### JS 使用 Unicode 字元

### Semicolons ; 分隔語句

- 使用 ; 分隔語句，(optional)

- 不然就使用換行。 替代。

# (108) 變數與賦值

x=5 , x=x+1;

## 語法糖 syntax sugar

- x=x+1  可寫 x+=1 。

## 變數 let const var 時機

### 值會變動、用let

### 不會變動、用const

### ⭐請勿使用var 進階JS解釋

## ⚠️特別注意的規則

### const 一定要賦予initializer

### let 可不用，但undefined。

### ⭐引擎允許 x=5 ; 但容易出錯

- 最好乖乖使用 let x=5;

### 不可以重複宣告 (let var const)

- let x=5;

- let x=6;  不可以 !  

### ⭐⭐const 不可以重複賦值

- 編譯器偷偷放行

- 但瀏覽器會報錯，雖然好像不會怎樣
  
  ![](../../../Images/2023-12-09-22-19-51-image.png)
  
  🔥還是我們的JAVA 比較好，final 會報錯QQ🔥

## 有GC 會自動刪除無法訪問的Object

# (109) 略

- 各位同學，這裡有個 Quick Fix 需要你們注意一下！下支影片中的 4:19 談到的 Number 範圍中，9,007,199,254,740,992 是 2 的 53 次方，不是 253 次方。編寫講義時沒有注意到多寫了一個 2，請在做筆記的時候更正成 53，謝謝！

# (110) 數字運算

## ⭐數據類型 DataType

| Number    | 整數與帶小數點的數字      |
| --------- | --------------- |
| BigInt    | 任意長度的整數         |
| String    | 字符串             |
| Boolean   | true 或 false    |
| null      | 故意代表不存在的值       |
| undefined | 沒被賦值就是undefined |

symbol - unique identifier 也是。

第八種數據類型 = object。

---

## JS整數範圍2^53 或2^-53次方

- 大概900億正負

## 運算符號

- \+ \- \* / 

- ** 次方
  
  ```js
  let x = 5;
  let y = 2;
  console.log(y ** x) ; 
  得32
  ```

- % 取餘數

- ++ 

- --

- +=   -=  /=   \*=

## ⭐++x 或 x++

- 區別自己去查，很常見。
  
  - 變數先+1，後執行其他、或帶入。
  
  - 先執行整行，之後變數+1。

# (110) String基本介紹

### " oni's world "

- 字串用法

### 串接 concatenation 使用 +

- 其他符號 -*/ 會導致NaN (Not a Number)

### 數字 串接 文字 會變成str+str

### \n 換行

- ```js
  /*         文字+數字 會怎樣?  版本2      */
  
  let n1 = 20;
  let n2 = 30;
  let name = "json";
  let n3 = 10;
  let n4 = 15;
  //猜看看?
  console.log(n1 + n2 + name + n3 + n4);
  // 結果1 50json25
  // 結果2 50json1015   >>>正確
  ```

# (111) Number Methods

## js 是物件導向、數字也是物件 !

## toString() / typeof

- 回傳數字的字串得到的是string

- age.toString 就能變成 25 ，"25"。
  
  ```js
  console.log(typeof age.toString());
  ```

## toFixed(n)

- 回傳轉換後的數字，到小數點後第n位，得String

- ![](../../../Images/2023-12-09-23-27-10-image.png)
  
  ```js
  const pi = 3.1415926;
  console.log(pi.toFixed(3));
  console.log(typeof pi.toFixed(3));
  ```
  
  ##### 🔥之後才會說明怎麼變回num

## 🔥🔥神奇的問題🔥🔥

```js
/*           function也是物件           */
let r = 11;
let s = r.toString;
console.log(s);
// 無法使用s()，除非是r.toString() 才有得到11 但是如果function就GG。
console.log(s.call(5));
  上面這是 GPT 說的方式 
```

## 0.1+0.2===0.3 會是false

- ```js
  if (0.1 + 0.2 != 0.3) {
    console.log("不等於哦");
    console.log(0.1 + 0.2);
    //得到 0.30000000000000004
  }
  ```


