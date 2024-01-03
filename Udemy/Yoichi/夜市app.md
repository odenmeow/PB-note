# 設計時應注意: ( 想到就補充 )

訂單資料不應該依賴 會受編輯而變動的 localStorage 的 yoichiProducts資料 

( 使用者更改、歷史才不會跟著變動)

因此頁面應該要 創建新的 ( 依賴yoichiProducts給的資料創建、但是獨立於yoichiProducts )

儲存到另一個kv 叫做 yoichiOrders 

生成之後就是獨立的個體戶

訂單修改的話 則依賴已經生成的內容yoichiOrders 內部資訊去改。

`yoichiOrders` 應該包含

- `order`
  
  - `raw_products_info`
    
    - 香腸 : 40
    
    - 蔥肉串 : 35
    
    - 豬肉串 : 40
    
    - 雞肉串 : 50
    
    - 特殊規則 : 蔥肉串 3隻 則 -5元 ，類推  ( 隨時可以取消 )
  
  - `details`
    
    - ( 產品名稱 ) : ( 數量 )
    
    - 香腸 : 3
    
    - 蔥肉串 : 2
    
    - 雞肉串 : 1
    
    - 總價 : 120+70+50=240 元
    
    - 日期 : 2024/1/2  18:20:00
    
    - 

---

# Section 1

## 關於解構、stringify

這邊使用了 destructuring assignment來快速解構

- 使用的方式如下

```js
  static historyRetrieve() {
    const data = JSON.parse(localStorage.getItem("yoichiProducts"));
    data.map(({ name, price }) => {
          new Product(name, Number(price));
    });
  }
  static historyUpdate() {
    localStorage.setItem("yoichiProducts", JSON.stringify(Product.products));
  }
  }
```

- 對應的舊方法

```js
data.map((obj) => {
      console.log(obj.name, obj.price);
    });
```

## 提醒未來要注意一下

1. 由於 historyRetrieve 有使用 new Product(name, Number(price));
   
   之後使用 變數創建new Product帶入的時候要小心 使用者輸入非數字、需要再那之前就先判斷他是否偷偷傳文字 而非數字。

# Section 2

## 小心事件監聽的重複附加

因為 updateDisplay 會更新並且重複執行 之前執行過的addEvenListener到 新和舊element

所以會導致重複附加

- 如下

- updateDisplay 內有以下
  
  - synchronizeEditModalContent
  
  - editProductDelete  ------------只需要跟modalEdit綁定一次就夠
  
  - editProductSavebtn  -----------只需要跟modalEdit綁定一次就夠
  
  - EditAppendFunctions

### modalEdit 內刪除跟保存   只需要綁定一次就好

這兩按鈕     不隨    畫面更新    而改變

```js
 // 針對畫面 all btEdits 增加監聽功能 ，modalEdit才能知道是誰被點取。
  function btnEditAppendFunctions(btnEdits) {
    btnEdits.forEach((btn, index) => {
      btn.addEventListener("click", (e) => {
        let parentElement = e.target.parentElement;
        // 跟sync 合併使用
        synchronizeEditModalContent(parentElement, index);
      });
    });
    // #yoichi-p-delete  這是modalEdit 刪除按鈕，只會有一個，欲知who被刪除直接參照modal內名稱跟價錢
    // 不要這樣用 updateDisplay()會重複呼叫!!!!!!!!!!!!!
    let btnDelete = document.querySelector("#yoichi-p-delete");
    editProductDelete(btnDelete);
    let btnSave = document.querySelector("#yoichi-p-editSave");
    editProductSave(btnSave);
  }
  let btnEdits = document.querySelectorAll(".yoichi-p-show-edit");
  btnEditAppendFunctions(btnEdits);
}
```

## 解決方法 兩個:

### 1. 先移除listener、將arrow function改為命名fn。

先移除 不會報錯、反而可以保證只會有一人負責監聽!

```js
  function editProductSave(btnSave) {
    // 請保持良好的實踐、每次先移除 (避免重複附加到既有element身上)
    btnSave.removeEventListener("click", editSaveListener);
    btnSave.addEventListener("click", function editSaveListener(e) {
      let modalEdit = document.querySelector("#yoichi-product-edit");

      modalEdit.classList.forEach((c) => {
```

### 2. IIFE

## 我決定給IIFE機會試試

section2 的版本是刷新show呈現會重複 綁定listener的、我不保留上面解決方法1的code、有興趣自己做，但是每次刷新都重新呼叫 這樣很吃資源!    

![](../../Images/2023-12-31-15-34-06-image.png)

## 結論:失敗、除非放最外層只執行一次

IIFE 會被執行多次 ( 如果放在其他函數內 )  updateDisplay多次調用所以還是出bug

# Section3

其實2 那邊 有些應該寫到三來

## let var 區別⭐⭐⭐⭐⭐

> [JavaScript 立即調用函式 IIFE (Immediately Invoked Function Expression) 是什麼？優缺點是什麼？｜ExplainThis](https://www.explainthis.io/zh-hant/swe/what-is-iife) 

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function () {
    console.log(i);   // 全都是6
  }, 0);
}
for (let i = 1; i <= 5; i++) {
  setTimeout(function () {
    console.log(i);
  }, 0);    // 1 2 3 4 5 
}
```

### GPT :

在 JavaScript 中，使用 `var` 關鍵字聲明的變數具有函式作用域。在你的例子中，`setTimeout` 的回調函式不會立即執行，而是在循環結束後才會被調用。當它們被調用時，它們都會引用循環結束後的 `i` 變數。JavaScript 函式會保留它們創建時的作用域，因此所有的 `setTimeout` 共享相同的作用域和 `i` 變數。因此，當這些回調函式被執行時，它們引用的都是循環結束後的 `i`，導致輸出結果都是 6。使用 `let` 關鍵字可以避免這個問題，因為 `let` 声明的变量是具有块级作用域的，每次迭代都会创建一个新的 `i` 变量。

### IIFE 破解var

```js
for (var i = 1; i <= 5; i++) {
  (function (i) {
    setTimeout(function () {
      console.log(i);
    }, i * 1000);
  })(i);
}
```

## 關於animationend我又再度忘了開watchSaas

e04 !

## 關於刪除後怪怪的: 不能只是移除

對於show-product 也就是display Area 的div form 區塊 那邊的edit-0  1 2 3要同步!

- 記得更改其他人的標籤  因為 [0,1,2] 如果 1拿走 則 畫面上會是edit-0,edit-2

- 2想再拿走 會失敗 因為 products[0,1]  編號是1 他找不到2 !!!!

## 改好了，完成功能了。 直接看commit就知。

![](../../Images/2024-01-01-19-34-08-image.png)

## 還有一小bug 新增的商品 無法刪除

# section 4

- 新增功能  禁止現有商品名稱重複 

## splice bug 如果undefined🔥🔥🔥🔥

splice(i,1) 如果i undefined則會刪除

### 會被視為0 直接刪除索引0 明明未定義!

### 直接操作物件

```js
if (this.pickedNumber == "") {
      let i;
      PickedProduct.pickedProducts.forEach((e, index) => {
        if (e.pickedName == this.pickedName) {
          i = index;
        }
      });
      // 下面會直接刪除 第i個物件
      console.log("i=", i);
      // 下面如果 i undefined的時候則 直接刪除0
      PickedProduct.pickedProducts.splice(i, 1);
      console.log("要刪除了", this.pickedName, this.pickedNumber);
    }
```

## arr.map( ) 記得回傳否則無效💡💡💡

```js
  static historyRetrieve() {
    const data = JSON.parse(localStorage.getItem("yoichiProducts"));

    if (data == null || data.includes(null)) {
      console.log("沒歷史紀錄或短缺");

      console.log("localData=", data);
      return "沒歷史紀錄或短缺";
    }
    Product.products = [];
    data.map(({ name, price }) => {
      new Product(name, Number(price));
      // 這邊直接改變了所以才不用回傳! 💡💡💡💡💡💡💡
    });
  }
```
