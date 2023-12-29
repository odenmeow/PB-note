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






