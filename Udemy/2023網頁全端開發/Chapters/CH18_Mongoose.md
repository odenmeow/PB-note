# (291) 特別注意！！版本差異要怎麼處理？

同學們，往後的多個課程中，我們會透過 npm 安裝多個套件。錄影當下版本與你觀看影片時的最新版本會有差別，因此，為了確保你在操作上可以完全跟著影片實作，我建議你可以：

1. 每個單元最後都會有 Final Code。下載Final Code後，找到 package.json 文件，**放到自己的專案資料夾內，在 command line prompt 或 terminal 執行**`**npm install**`**指令，就可以自動下載所有正確版本的dependencies**。這是最容易也是我最推薦的方式。

2. 你也可以根據影片中看到的套件版本，直接使用 `npm install <package>@<version>`這樣的指令來下載與影片相同版本的套件。<package> 的部分要改成套件名稱，<version> 的部分要改成版本數字。

3. 如果你想要使用全部都是最新的套件的話，你也可以選擇自己閱讀documentation，選擇自學最新版本的寫法。這樣也是可以的。如果 Documentation 都是英文看不懂的話，也可善用 Google 或是 DeepL 翻譯功能。

# (292) Mongoose介紹

## ODM (object document mapping)

## ODM好處:

1. 資料結構能被追蹤、通常的話若結構改變 很難退回，但ODM可以把結構寫在程式碼內，方便追蹤、更改。

2. ORM/ODM 會內建保護機制 防止SQL Injection

3. 讓Project更符合 MVC模型，View=EJS Controller=app.js Model=Mongoose

![](../../../Images/2024-01-04-22-55-01-image.png)

# (293) 遇到MongooseServerSelectionError: connect ECONNREFUSED ::1:27017怎麼辦？

下支影片中，如果遇到MongooseServerSelectionError: connect ECONNREFUSED ::1:27017的錯誤的話，將程式碼中的localhost的部分改成：

.connect("mongodb://127.0.0.1/exampleDB")

這樣的形式就可以了。localhost這個字在電腦內的DNS會被轉換成127.0.0.1，所以一般的情況來說，都是可以轉換的。會發生上面的錯誤是因為，電腦的編碼無法將localhost轉換成127.0.0.1，所以才會發生寫localhost但卻無法連線的情況。(但本質上來說，localhost與127.0.0.1是一樣的)

# (294) Mongoose套件下載

## 我有寫Readme.md

```js
# 自己安裝

記得先初始化
PS C:\CodeSForGit\2023WebFullStack\Chapter18-Mongoose> npm init

npm install ejs@3.1.8
npm install express@4.18.2
npm install mongoose@6.6.5
```

> npmjs  去找到 mongoose

```js
const express = require("express");
const app = express();
const mongoose = require("mongoose");

// 會回傳成功或失敗 (promise)
mongoose
  .connect("mongodb://127.0.0.1:27017/exampleDB")
  .then(() => {
    console.log("mongodb連接成功");
  })
  .catch((e) => {
    console.log("連線失敗", e);
  });

app.set("view engine", "ejs");
app.listen(3000, () => {
  console.log("正在聽port 3000");
});
```

# (295) Model and Schema

## Schema

映射到 mongodb的 collection 並且定義架構

包含 默認值、最大長度、最大、小值之類。

## Model

包裝Schema的容器，作為接口，Collection 透過他 可以CRUD 。

> Model 像是SQL table 然後 Schema = create table的部分

稍微提到了 import mongoose from mongoose ;

他說REACT 才會學這用法

基本上=  const mongoose=require("mongoose")

![](../../../Images/2024-01-04-23-58-51-image.png)

mongoose schema type 基本上就是 SQL 的 data type的部分 

  提示一下解構

```js
class People {
  constructor(name, age, height) {
    this.name = name;
    this.age = age;
    this.height = height;
  }
}
let umi = new People("Umi", 16, 155);
let { age, height } = umi;
console.log(age); //得到16
console.log(name); // ReferenceError = undefined
```

![](../../../Images/2024-01-05-00-10-01-image.png)

> **上圖說明只要我們提供英文名稱後 會被自動轉換為單數形式**

# ⚠️總之小心被自動轉單數

```js
const { Schema } = mongoose;
const studentSchema = new Schema({
  name: String,
  // name:{type:String}  也可以
  age: Number,
  major: String,
  scholarship: {
    merit: Number,
    other: Number,
  },
});const Student = mongoose.model("Student", studentSchema);
const newObject = new Student({
  name: "Esther",
  age: 27,
  major: "Mathematics",
  scholarship: {
    merit: 6000,
    other: 7000,
  },
});
```

### document.save()儲存物件 會是promise 物件!

doc.save().then(savedDoc=>{savedDoc===doc; //true}); 

物件自動被套用進去後續的then，如果成功還可以繼續往下執行

```js
const Student = mongoose.model("Student", studentSchema);

const newObject = new Student({
  name: "UmiOOO",
  age: 17,
  major: "Mathematics",
  scholarship: {
    merit: 1000,
    other: 2000,
  },
});
console.log("儲存之前");
console.log(newObject);//建立的時候就已經有id了
newObject
  .save()
  .then((savedObject) => {
    console.log("資料成功儲存");
    console.log(savedObject);
  })
  .catch((e) => {
    console.log(e);
  });
```

Ch18 section - 295 model and schema ，實際上使用了const Student=mongoose.model(collectionName,collectionsSchema)、new Student得到的物件 自帶id，透過.save().then(savedObj=>{} ).catch()可以更好連攜

# (296) Mongoose資料查詢

## Query

許多方法的回傳型態都是Query ，為mongoose特有的class

根據doc 可知道 `Query is thenable object`但不是`Promise`

提供 find update delete 等操作能玩 chaining 。

如果要讓它變成Promise則使用 Query 物件的 .exec()

## Model  (對應collection)

### Model.find(filter) 找所有符合的物件

```js
/***比較弱的作法 */
Student.find()
  .exec()
  .then((data) => {
    console.log(data);
  })
  .catch((e) => {
    console.log(e);
  });
```

```js
app.get("/", async (req, res) => {
  try {
    let data = await Student.find().exec();
    //會直接得到 或者透過try catch拋出錯誤
    res.send(data);
  } catch (e) {
    console.log(e);
  }
});
```

### Model.findOne(filter) 找第一個符合的物件

```js
app.get("/", async (req, res) => {
  try {
    // let data = await Student.find().exec();
    let data = await Student.findOne({ name: "UmiOOO" }).exec();
    //會直接得到 或者透過try catch拋出錯誤
    res.send(data);
  } catch (e) {
    console.log(e);
  }
});
```

# (297) Query Object與Promise比較補充

關於Query Object與Promise的比較，我用文字在這裡補充。Query Object 以及 Promise兩者非常相像：

1. Query Object本身是一種 thenable object，代表後面可以串接 .then() 以及 .catch()。在上支影片當中的 find() 以及findOne() 兩個 method 的 return 值都是Query Object。因此，如果你把上支影片範例中的 .exec() 全部刪掉，會發現程式碼還是能夠照常運作的。

2. Promise 語法我想你應該很熟悉了。也是可以使用 .then() 以及 .catch()。

這裡可以看出，在Query Object後面加上.exec()，轉變成Promise，這個步驟，似乎不是必要的。那兩者有何不同？或者，哪些情況該用哪個呢？

答案是，不管在哪種情況，在 Query Object 後面加上 .exec()，讓它變成 Promise 都是比較好的。原因在於，使用Promise的話，JavaScript的 try... catch... 語法中，catch 可以顯示更好的錯誤追蹤訊息。詳細的例子可以參考mongoose的documentation：https://mongoosejs.com/docs/promises.html 。使用 Promise 的話，錯誤追蹤訊息會顯示出問題的 .exec() 是在哪一行程式碼。因此，加上 .exec() 會比不加來得更好。

# (298) 更新資料

## Model.updateOne(filter,update,options)

### 重要! 沒保證順序⭐⭐⭐⭐⭐

> 資料找的方向不是固定，隨機找到人就更改所以 小心使用!

```js
            "merit": 1000,
            "other": 2000
        },
        "_id": "6596dad97f39ed4103ef0880",
        "name": "UmiOAO",
        "age": 2,
        "major": "Mathematics",
        "__v": 0
    },
    {
        "scholarship": {
            "merit": 1000,
            "other": 2000
        },
        "_id": "6596db2351d06ae964028c8d",
        "name": "UmiOAO",
        "age": 17,
        "major": "Mathematics",
        "__v": 0
    },
    {
        "scholarship": {
            "merit": 1000,
            "other": 2000
        },
        "_id": "6596db2b521098ba2165f87d",
        "name": "UmiOAO",
        "age": 17,
        "major": "Mathematics",
        "__v": 0
    },
    {
        "scholarship": {
            "merit": 1000,
            "other": 2000
        },
        "_id": "6596db6bb5e7fe695d08ca78",
        "name": "UmiOOO",
        "age": 15,
        "major": "Mathematics",
        "__v": 0
    },
```

找到第一個符合的，更新值，前兩個參數都是obj。

.then內部帶入的是更新操作的訊息:

    acknowledged , modifiedCount,upsertedId 之類 下

---

```js
{
  acknowledged: true,
  modifiedCount: 1,
  upsertedId: null,
  upsertedCount: 0,
  matchedCount: 1
}
```

---

`Option` 物件可以設定`run Validators` 如果值 不符合`Schema`

則出現`error`。

### update使用上要小心⭐⭐⭐⭐⭐

#### $set 可以避免全體更新，而是更新關注的部分

```js
Student.updateOne({ name: "UmiOOO" }, 
{$set:{ name: "UmiOAO" }})
```

```js
Student.updateOne({ name: "UmiOOO" }, { name: "UmiOAO" })
  .exec()
  .then((msg) => {
    console.log(msg);
  })
  .catch((e) => {
    console.log(e);
  });
----------------------------------

正在聽port 3000
mongodb連接成功
{
  acknowledged: true,
  modifiedCount: 1,
  upsertedId: null,
  upsertedCount: 0,
  matchedCount: 1
}

-----------------------------------------
Student.find()
  .exec()
  .then((data) => {
    console.log(data);
  })
  .catch((e) => {
    console.log(e);
  }); 
===========================
...
...

 },
  {
    scholarship: { merit: 1000, other: 2000 },
    _id: new ObjectId("6596dad97f39ed4103ef0880"),
    name: 'UmiOAO',
    age: 17,
    major: 'Mathematics',
    __v: 0
  },
```

---

## 搭配schema 限制

```js
const studentSchema = new Schema({
  name: String,
  // name:{type:String}  也可以
  // age: Number,
  age: { type: Number, min: [0, "年齡不能小於0"] },
  major: String,
  scholarship: {
    merit: Number,
    other: Number,
  },
});
```

### schema可限制<創建>的時候不能亂創

關於min 也是 條件不符合

```js
let newStudent = new Student({
  name: "Oni",
  age: -10,
  major: "CS",
  scholarship: {
    merit: 10,
    other: 0,
  },
});
newStudent.save().then((data) => {
  console.log(data);
});

---------------------------------------------------    


    this.$__.validationError = new ValidationError(this);
                               ^

ValidationError: Student validation failed: age: 年齡不能小於0
```

## runValidators 可以強制update符合schema

如果省略，則不管schema長怎樣

```js
Student.updateOne({ name: "UmiOOO" }, { age: -5 })
--------------------------------------
Student.updateOne({ name: "UmiOOO" },
 { age: -5 }, { runValidators: true })



------------------------------------------------------------
     properties: [Object],
      kind: 'min',
      path: 'age',
      value: -5,
      reason: undefined,
      [Symbol(mongoose:validatorError)]: true
    }
  },
  _message: 'Validation failed'
}
mongodb連接成功
```

### updateOne第三個參數中的new

new:true 對updateOne無效

```js
Student.updateOne({ name: "UmiOOO" }, 
{ age: 15 }, { runValidators: true,new:true })
```

![](../../../Images/2024-01-05-01-37-31-image.png)

## Model.updateMany(filter,update,options)

略過說明

## Model.findOneAndUpdate(condition,update,options)

找到第一個符合的，更新。

參數都是objecct型態

.then ()內部的 callbackFn 

被執行帶入的parameter會是更新完成的document。

如果沒有表明new 是 true ，false (預設)，則parameter會是更新前的document。

```js
Student.findOneAndUpdate(
  { name: "Oni" },
  { age: 255 },
  { runValidators: true, new: true } //得到的then data 會是更新完成的資料
)
  .then((data) => {
    console.log("找到並且更新了");
    console.log(data);
  })
  .catch((e) => {
    console.log(e);
  });
```

> 如果沒有找到 會印出null   !

⚠️沒找到怎麼不是丟錯誤呢 蠻詭異 ㄏ

# (299) 刪除資料

# (300) Schema Validators

# (301) Static method, instance method

# (302) Mongoose Middleware

# (303) Final Code
