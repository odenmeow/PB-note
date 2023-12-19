# (182) JS 引擎、無限大

- `C++` 是一種直接編譯的語言，與像 `Java`或 `JavaScript` 這樣需要`虛擬機`或`引擎`來執行不同。

- 當你編寫並編譯一個 `C++` 程式時，編譯器會將 `C++` 代碼編譯成可執行的二進制文件。這個二進制文件包含了機器碼，可以直接在相容的硬體上運行，無需中間的虛擬機或解釋器。因此，`C++` 程式是直接在計算機上執行的，不需要像 `Java` 或 `JavaScript` 那樣的引擎或虛擬機。

## ECMA 制定標準，引擎則遵循標準

- `ECMA` 歐洲電腦製造協會

- 最有名標準在2015年更新，`ECMA2015`  或 `ES6`

## NaN、Infinity

> `data type`都是`number`

### NaN  `number`

- not a number ，用String 或其他資料類型進行數學計算，若無法計算就會出現NaN。

### Infinity `number`

- 正無窮大，負的會有負號
  
  - 規則
    
    - 任何 正整數 * infinity 都是 infinity
    
    - 任何 正整數 * infinity 都是 0
  
  - 案例
    
    - `5/0`  = `Infinity`   

## 陣列 concatenate

### 使用 +  (預期外效果)

- [1,2,3] + [4,5,6]
  
  ```js
  let num1 = [1, 2, 3];
  let num2 = [4, 5, 6];
  console.log(num1 + num2); // 1,2,34,5,6   括號也不見了
  console.log(typeof (num1 + num2)); // string 
  ```

### 使用 concat()

- [1,2,3].concat([4,5,6])
  
  ```js
  console.log(num1.concat(num2));
  // [ 1, 2, 3, 4, 5, 6 ]
  ```

# (183) Spread Syntax & Rest Parameter

## Spread Syntax🔥沒見過的奇葩🔥

### 時機: array||function invocation

#### 自動迭代，帶入 array 元素。

- 允許需要0個 ~ 多個參數的地方 直接接入可迭代物件內容
  
  ```js
  /*                Spread Syntax                  */
  const parts = ["手", "腳"];
  const otherParts = ["頭", "身體", ...parts];
  console.log(otherParts); //[ '頭', '身體', '手', '腳' ]
  ```

### 可用來複製array

- 作法如下
  
  ```js
  // 可用來複製array
  // copy by reference
  const arr = [1, 2, 3];
  const arr2 = [...arr];
  console.log(arr2); // [1,2,3]
  arr2.pop();
  console.log(arr2); // [1,2]
  console.log(arr); // [1,2,3]
  ```

- 也可以這樣
  
  ```js
  /* 使用Spread Syntax 串聯 */
  let nums1 = [1, 2, 3];
  let nums2 = [4, 5, 6];
  console.log([...nums1, ...nums2]);
  ```

### 屬於shallow copy⭐

- 另外小心內容如果非primitve，複製 ref 而已 屬於`shallow copy`

### function 應用

#### 陣列大小跟參數一致

```js
/*                 Spread Syntax 應用在function                          */
function sum(x, y, z) {
  return x + y + z;
}
let array = [1, 2, 3];
console.log(sum(array[0], array[1], array[2]));
// 或者
console.log(sum(...array));  ⭐⭐
```

#### 陣列大於參數

##### 1. 不手動卡位，則0~最後自動帶入，剩下捨棄。

##### 2. 手動卡位 ，可以卡前面  。

##### 3. 手動卡位 ，卡後面無效，因為前面參數填滿了。

#### 陣列小於參數

##### 1. 剩下沒填入，自動"" ，得到NaN

- 如果內部做 `加法`，可能導致得到 `NaN`

##### 2. 找空位填入...array ，剩下自己手動加

- sum( 5,...array,6,7) 之類

## Rest Parameter

### 時機 : function definition

#### 自動壓縮剩餘參數arg到  (自訂名稱)的array

```js
function sum2(a,...args) {
  let sum = 0;
  sum+=a;
  console.log("a: ", a); //100
  args.forEach((e) => {
    sum += e;
  });
  return sum;
}

console.log(sum2(100,1, 2, 3, 4, 5, 6, 7, 8, 9, 10));
//155
```

## 特別注意事項 :

### 小心覆蓋function

```js
function sum(x, y, z, a, b, c) {
  return x + y + z + a + b + c;
}
let array = [1, 2, 3, 10, 10, 10, 20];
console.log(sum(array[0], array[1], array[2], 1, 1, 1));
// 或者
console.log(sum(...array, 222)); 🔥

/*                RestParameter 應用在function                          */
// 壓縮剩餘的參數到某一個array變數 (param)

function sum(...args) {
  let sum = 0;
  args.forEach((e) => {
```

#### 如上，如果兩個function名稱一樣，🔥的地方使用的會是最後定義的同名稱function!!!

# (184)Primitive Reference Data Types

## Primitive

### 先說結論，沒有自己的attr、methods

### 乘載 value，而不僅僅ref

### Primitive Coercion

- 自動裝箱到wrapper object ，改為訪問該物件上的屬性...等。

- 例子
  
  - "foo".includes("f") >>> 比較像
    
    把 "foo" 放到new String ("foo") 
    
    然後執行從String繼承的 String.prototype.includes()。

## Reference

### 範例對比

```js
let myNamePrimitive = "Oni";
console.log(typeof myNamePrimitive); //string 原始型態

let myNameObject = new String("Oni");
console.log(typeof myNameObject); // object 物件!!!!
```

### Mdn不推薦用new 故意做成object，耗資源!

#### 提供實際測量時間、其實ch6有用過 !

```js
/* 開始講超綱的東西 測量 */
const { performance } = require("perf_hooks"); //node.js
let startTime = performance.now();
for (let i = 0; i < 10000000; i++) {
  // let a = new String("asdasdl;kjklgjdgklvbnm,x.as");
  let a = "asdasdl;kjklgjdgklvbnm,x.as";
}
console.log(performance.now() - startTime);
```

- new 要花 93ms   但是 a = "asdasdl;kjklgjdgklvbnm" 只要4.9ms

# (185) JavaScript String Comparison

## compared lexicographically 字典式比較⭐

- 按順序，所以  `' Z '  >   ' A '`   &  `' 9 ' > ' 1 '`
  
  由於 開頭`'b' > 'a'` 所以不管`'a'`後面增加多少字 ，比大小都輸
  
  ```js
  console.log("b" > "a000202023b"); //true 
  ```

- ##### "12" < "2"  true    ` ' 1 ' < ' 2 '`  所以 `'1'` 增加後面也輸
  
  ```js
  console.log("12" < "2");//true 
  ```

- 總之最後要注意 跟數字結合後 : ⭐⭐⭐
  
  --- 
  
  1. 唯有 * 這個 operator 讓 `"字串"` 包含`""`
     乘      `[ "" ,'', false, null,]` = 0 數字
     乘      `[ NaN , undefined ]`         = [NaN,undefined]
  
  2. 字串 + 數字 一律得到 concat 而已
  
  --- 

## 可以參考project4 找字串行為

# (186) 進階array methods⭐

## 參數都放callbackFn

### arr.map()

- 變大寫 
  
  ```js
  let languages = ["Java", "C++", "Python", "JavaScript"];
  let result = languages.map(function (lang) {
    return lang.toUpperCase();
  });
  console.log(result);
  // [ 'JAVA', 'C++', 'PYTHON', 'JAVASCRIPT' ]
  ```

- 使用arrow 自動回傳 
  
  ```js
  let result = languages.map((lang) => lang.toUpperCase());
  console.log(result);
  ```

### arr.find()

- 找得到 就回傳第一個找到的元素，找不到就undefined
  
  ```js
  // 資源包
  const languagesRes = [
    { name: "Python", rating: 9.5, popularity: 9.7, trending: "super hot" },
    { name: "Java", rating: 9.4, popularity: 8.5, trending: "hot" },
    { name: "C++", rating: 9.2, popularity: 7.7, trending: "hot" },
    { name: "PHP", rating: 9.0, popularity: 5.7, trending: "decreasing" },
    { name: "JS", rating: 8.5, popularity: 8.7, trending: "hot" },
  ];let resultFind = languagesRes.find((lang) => {
    return lang.popularity > 9.5;
  });
  console.log(resultFind);
  // { name: 'Python', rating: 9.5, popularity: 9.7, trending: 'super hot' }
  ```

### arr.filter()

#### 小心比較、使用==

- 過濾滿足的元素。
  
  ```js
  let resultFilter = languagesRes.filter((lang) => {
    return lang.trending == "hot";
  });
  console.log(resultFilter);
  
  [
    { name: 'Java', rating: 9.4, popularity: 8.5, trending: 'hot' },
    { name: 'C++', rating: 9.2, popularity: 7.7, trending: 'hot' },
    { name: 'JS', rating: 8.5, popularity: 8.7, trending: 'hot' }
  ]
  ```

### arr.some()

- 是否至少一個通過callbackFn 條件? ....return true 代表通過 
  
  ```js
  let resultSome = languagesRes.some((lang) => {
    return lang.popularity < 5;
  });
  console.log(resultSome); //false   
  ```
  
  <6 才有  <5找不到

### arr.every()

- 所有元素都通過callbackFn 條件?   ....return true 代表通過
  
  ```js
  let resultEvery = languagesRes.every((lang) => {
    return lang.popularity > 5;
  });
  console.log(resultEvery); //true
  ```

 

# (187) map,forEach 差別比較

## map

### 映射回傳值到新陣列

- 回傳平方
  
  ```js
  let arrMap = [1, 2, 3, 4, 5, 6];
  let newArrMap = arrMap.map((i) => i ** 2);
  console.log(newArrMap); 
  // [ 1, 4, 9, 16, 25, 36 ]
  ```

- 或者固定值
  
  ```js
  newArrMap = arrMap.map((i) => 1);
  console.log(newArrMap);
  //[ 1, 1, 1, 1, 1, 1 ]
  ```

### 重要實驗:

```js
/*      私下實驗          */

let Oni = {
  name: "Oni",
  age: 25,
};

let Umi = {
  name: "Umi",
  age: 16,
};

let arr = [1, 2, 3, Oni, Umi];
let newArr = arr.map((obj) => {
  if (typeof obj == "number") {
    return obj ** 2;
  } else {
    obj.age = 100;
    return obj;
  }
});
console.log(newArr);
[ 1, 4, 9, { name: 'Oni', age: 100 }, { name: 'Umi', age: 100 } ]
console.log("原始arr:", arr);
//原始arr: 
[ 1, 2, 3, { name: 'Oni', age: 100 }, { name: 'Umi', age: 100 } ]
```

會發現map 如果有物件，則需要小心否則依舊會改變原始陣列 !

## forEach

### 僅返回undefined，僅訪問元素

- 回傳預設undefined 要自己回傳
  
  ```js
  let arrForEach = [1, 2, 3, 4, 5, 6];
  let newArrForEach = arrForEach.forEach((i) => i ** 2);
  console.log(newArrForEach); //undefined
  ```

### 更明顯的例子

不會受理回傳值 i ，也不整合為陣列 ， 只是`單純協助遍歷`。

永遠返回 undefined

```js
newArrForEach = arrForEach.forEach((i) => {
  return i;
});
console.log(newArrForEach); //undefined
```
