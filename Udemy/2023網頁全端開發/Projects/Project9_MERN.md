# (367) 專案介紹

# (368) Model設定

## MERN

`MongoDB` 、`Express` 、`React` 、`Node` 

有註冊 登入 登出 、學生系統、老師系統

## Work Flow

<img title="" src="../../../Images/37983c035dddd3530ab55071298159b5e636dc47.png" alt="修改json先用js" width="508">

- 先不要讓它當作react js。

製作 `index.js` ， MERN > index.js

屬於server的服務範疇

然後製作 `models` / `user-model.js`， MERN > models  > user-model.js

然後製作`models` / `course-model.js` 

然後製作 `models` / `index.js` 使用8080 port，因為REACT 3000預設 要改麻煩

## index.js

MERN > index.js

```js
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");
dotenv.config();

mongoose
  .connect("mongodb://localhost:27017/MernDB")
  .then(() => {
    console.log("成功連接到MongoDB..");
  })
  .catch((e) => {
    console.log(e);
  });
// middleWares
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.listen(8080, () => {
  console.log("Backend on port 8080");
});
```

## user-model.js

userSchema.method

是文件推薦的寫法 透過 methods 加上 方法，可以讓我們更好的判斷

`isStudent` 、`isInstructor` ，`comparePassword` 三個方法

compare那邊 用 `this` 也要注意function / arrow fn 

另外 還寫 `userSchema.pre` 

也要留意使用 function / arrow fn 

傳統`function`才能 `dynamic binding`!  💡💡💡

> 原因是 為了動態綁定 ! 

### 為了動態綁定this!⭐⭐⭐⭐⭐

如果使用 arrow fn 則會看創造的當下context 

但該this就不會是 document 

如果使用 function 傳統方式，則會🔥動態綁定🔥

也就是對方如果呼叫我傳入的function是透過 this.myfunction()

則this 就會幫我指向document了

不需要特地 bind (document)  !!!🔥🔥🔥🔥🔥🔥

```js
const mongoose = require("mongoose");
const { Schema } = mongoose;
const bcrypt = require("bcrypt");
const userSchema = new Schema({
  username: {
    type: String,
    required: true,
    minlength: 3,
    maxlength: 50,
  },
  email: {
    type: String,
    required: true,
    minlength: 6,
    maxlength: 50,
  },
  password: {
    type: String,
    required: true,
  },
  role: {
    type: String,
    enum: ["student", "teacher"],
    required: true,
  },
  date: {
    type: Date,
    default: Date.now,
  },
});

// instance methods
userSchema.methods.isStudent = function () {
  return this.role == "student";
};
userSchema.methods.isInstructor = function () {
  return this.role == "instructor";
};
// mongoose middlewares

userSchema.methods.comparePassword = async function (password, cb) {
  let result = await bcrypt.compare(password, this.password);
  return cb(null, result);
};

// if new user 或者 edit password 則 hash password
userSchema.pre("save", async function (next) {
  // this delegate document in mogodb
  // 不能使用 arrow fn 否則 this不會代表mongodb的document
  if (this.isNew || this.isModified("password")) {
    const hashValue = await bcrypt.hash(this.password, 10);
    this.password = hashValue;
  }
  next();
});

module.exports = mongoose.model("User", userSchema);
```

### methods直接輔助判斷的例子如下

```js
const User = mongoose.model("User", userSchema);

// 創建一個用戶實例
const user = new User({
  username: "john_doe",
  email: "john@example.com",
  password: "hashed_password", // 實際上應該是經過 bcrypt 處理的密碼
  role: "student",
});

// 使用實例方法檢查角色
console.log(user.isStudent()); // true
console.log(user.isInstructor()); // false
```

## course-model.js

值得注意的是

instructor那邊的方式

```js
const mongoose = require("mongoose");
const { Schema } = mongoose;
const courseSchema = new Schema({
  id: { type: String },
  title: {
    type: String,
    required: true,
  },
  description: {
    tpye: String,
    required: true,
  },
  price: {
    type: Number,
    required: true,
  },
  instructor: {
    type: mongoose.Schema.Types.ObjectId, //primary key
    ref: "User",
  },
  student: {
    type: [String],
    default: [],
  },
});
module.exports = mongoose.model("Course", courseSchema);
```

# (369) 註冊使用者

## Work Flow

建立 路由 資料夾  ---  auth + index

MERN > `routes` 資料夾 > `auth.js`、 `index.js`

然後套用在 MERN `index.js`

接下來使用者註冊功能 

> 以前是自己寫

現在使用 `joi` package🔥

MERN > `validation.js` 

寫好之後回到 `auth.js` `v2` ，引用joi驗證器~~~~

有了以後，就可以顯示出錯誤原因了 (註冊需要6碼之類)

---

`auth.js` `v3` 

💡發現下面這個 沒寫index ，但沒寫她就自動去找index.js 來用了 !💡

`const User = require("../models").user;` 

寫好之後會發現有

帳號註冊過濾功能，不需要一個一個自己寫清楚

然後也能註冊帳號了，重複也能揪出了 `信箱已被註冊過了`

也發現 密碼有被bcrypt了!

## auth.js

### v1

設置一個middleware，通過的話會觸發

測試API

```js
const router = require("express").Router();
router.use((req, res, next) => {
  console.log("正在接受auth有關的請求");
  next();
});

router.get("/testAPI", (req, res) => {
  return res.send("成功連接auth route");
});
module.exports = router;
```

### v2 引用joi驗證器 ( 使用者能看到錯誤訊息 )⭐⭐⭐

> 就不需要我們一個一個去寫 ， 省很多麻煩⭐⭐

```js
const router = require("express").Router();
const registerValidation = require("../validation").registerValidation;
const loginValidation = require("../validation").loginValidation;
router.use((req, res, next) => {
  console.log("正在接受auth有關的請求");
  next();
});

router.get("/testAPI", (req, res) => {
  return res.send("成功連接auth route");
});

router.post("/register", (req, res) => {
  //   console.log(req.body);
  //   console.log(registerValidation(req.body));
  let { error } = registerValidation(req.body);
  //   console.log(error);
  if (error) {
    return res.status(400).send(error.details[0].message);
  }
});

module.exports = router;
e.exports = router;
```

<img src="../../../Images/2024-01-20-21-35-56-image.png" title="" alt="" width="414">

#### 驗證發現錯誤 v1

> 驗證結果，如果有錯誤

`  console.log(registerValidation(req.body));`  

```batch
正在接受auth有關的請求
{
  username: 'onisan',
  email: 'oni@gmail.com',
  password: 'oni',
  role: 'student'
}
{
  value: {
    username: 'onisan',
    email: 'oni@gmail.com',
    password: 'oni',
    role: 'student'
  },
  error: [Error [ValidationError]: "password" length must be at least 6 characters long] {
    _original: {
      username: 'onisan',
      email: 'oni@gmail.com',
      password: 'oni',
      role: 'student'
    },
    details: [ [Object] ]
  }
}
```

#### 驗證發現錯誤 v2

  `let { error } = registerValidation(req.body);` 

得到以下

```js
[Error [ValidationError]: "password" length must be at least 6 characters long] {
  _original: {
    username: 'onisan',
    email: 'oni@gmail.com',
    password: 'oni',
    role: 'student'
  },
  details: [
    {
      message: '"password" length must be at least 6 characters long',
      path: [Array],
      type: 'string.min',
      context: [Object]
    }
  ]
}
```

#### 驗證 通過則

> 如果正確

```batch
{
  value: {
    username: 'onisan',
    email: 'oni@gmail.com',
    password: 'onioni',
    role: 'student'
  }
}
```

### v3 建立帳號+驗證

製作新用戶、並且有過濾 驗證功能! 

也發現 密碼有被bcrypt了!

```js
const router = require("express").Router();
const registerValidation = require("../validation").registerValidation;
const loginValidation = require("../validation").loginValidation;
const User = require("../models").user;
router.use((req, res, next) => {
  console.log("正在接受auth有關的請求");
  next();
});

router.get("/testAPI", (req, res) => {
  return res.send("成功連接auth route");
});

router.post("/register", async (req, res) => {
  // 確認傳入的資料 ， 是否符合規範
  let { error } = registerValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // 確認信箱是否已註冊過
  const emailExist = await User.findOne({ email: req.body.email });
  if (emailExist) return res.status(400).send("信箱已被註冊過了");
  // 製作新用戶
  let { email, username, password, role } = req.body;
  let newUser = new User({ email, username, password, role });
  try {
    let savedUser = await newUser.save();
    return res.send({
      msg: "成功建立",
      savedUser: savedUser,
    });
  } catch (error) {
    return res.status(500).send("無法儲存使用者");
  }
});

module.exports = router;
```

```json
{
    "msg": "成功建立",
    "savedUser": {
        "username": "onisan",
        "email": "oni@gmail.com",
        "password": "$2b$10$Zt3ZmiXN4P8vHp5tGmROduSXvEpomDrfzRSSWRjLJbu4U43Sqjq.u",
        "role": "student",
        "_id": "65abd04dabd34622e6fcf4f4",
        "date": "2024-01-20T13:53:17.388Z",
        "__v": 0
    }
}## index.js (routes)
```

```js
module.exports = {
  auth: require("./auth"),
};
```

## index.js (MERN)

```js
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const dotenv = require("dotenv");
dotenv.config();
const authRoute = require("./routes").auth;

mongoose
  .connect("mongodb://127.0.0.1:27017/MernDB")
  .then(() => {
    console.log("成功連接到MongoDB..");
  })
  .catch((e) => {
    console.log(e);
  });
// middleWares
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.use("/api/user", authRoute);

app.listen(8080, () => {
  console.log("Backend on port 8080");
});
```

## npm i joi

## validation.js

MERN>validation.js

註冊的時候幫我們快速  建立簡單驗證格式

不需要慢慢手打 ( model 建立也可能被略過 或不熟悉mongoose用法 而略過)

```js
const Joi = require("joi");

const registerValidation = (data) => {
  const schema = Joi.object({
    username: Joi.string().min(3).max(50).required(),
    email: Joi.string().min(6).max(50).required().email(),
    password: Joi.string().min(6).max(255).required(),
    role: Joi.string().required().valid("student", "instructor"),
  });
  return schema.validate(data);
};

const loginValidation = (data) => {
  const schema = Joi.object({
    email: Joi.string().min(6).max(50).required().email(),
    password: Joi.string().min(6).max(255).required(),
  });
  return schema.validate(data);
};

const courseValidation = (data) => {
  const schema = Joi.object({
    title: Joi.string().min(6).max(50).required(),
    description: Joi.string().min(6).max(50).required(),
    price: Joi.number().min(10).max(9999).required(),
  });
  return schema.validate(data);
};

module.exports.registerValidation = registerValidation;
module.exports.loginValidation = loginValidation;
module.exports.courseValidation = courseValidation;
data);
};
```

# (370) 登入使用者並製作JWT

## Work Flow

`介紹`

HTTP is a stateless protocol  

server 無須保留每個用戶的狀態 

簡化客戶端跟伺服器所需要溝通的次數跟數據量

---

`JWT` 需要一些工具

`jsonwebtoken` 、`passport-jwt` ( 跟 jsonwebtoken 合作 )、`passport-local`

npm i `jsonwebtoken` `passport` `passport-jwt` `passport-local` 

`.env` 

建立密碼 PASSPORT_SECRET="secret"

---

`auth.js`

MERN > routes > auth.js

開始做一些登入功能

`user-model.js` 

稍微改一下 comparePassword 內部

---

基本上只有改寫auth + user-model而已

就可以實現登入效果 ( 然後製作出`JWT`)

## 驗證使用者分兩種

### 1.Stateful authentication

驗證身分後，`server` 回傳一個session id給 `user` ，然後 `db`  創建一個 `session` 

儲存跟 `user` 相關的訊息:

- session id 過期 時間

- user 可訪問的資源 

- 之類

#### 優點:

`a.` 

伺服器可以隨時將session內部資料刪除，方便管理，可及時讓user 持有的id失效。

`b.` 

如果server主機只有一台，可以很容易管理 stateful authentication。

#### 缺點:

##### 資源佔據:

隨著用戶數量，session會佔據越多資源。

##### 擴展性差:

如果session分佈在不同server，需要寫演算法追蹤各用戶狀態、還要避免故障問題。 

### 2.Stateless authentication

成功驗證之後，`server` 把 `user`相關的資訊拿去簽名，生成`token` 發回給`user` 

`JWT` `Json Web Token` ，`OIDC` `OpenID Connect` 有規範其生成過程。

## 優點:

##### 降低伺服器開銷:

大量數據不用儲存在server，不用擔心開銷問題

##### 易於擴展:

server擴展無所謂，只要持有相同secret就能解碼，驗證有效性

## 安裝 packages

npm i `jsonwebtoken` `passport` `passport-jwt` `passport-local`

## .env

PASSPORT_SECRET="passsssssssssssssssssssssssssssssss"

## auth.js

引用 `jwt` 

製作 `login`  post

```js
const jwt = require("jsonwebtoken");



router.post("/login", async (req, res) => {
  // 確認格式是否正確
  let { error } = loginValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // 確認信箱是否已註冊過
  const foundUser = await User.findOne({ email: req.body.email });
  if (!foundUser) return res.status(400).send("找不到使用者，請確認信箱");
  foundUser.comparePassword(req.body.password, (err, isMatch) => {
    console.log("pass");
    if (err) {
      console.log("錯誤", err);
      return res.status(500).send(err); // err發生
    }
    if (isMatch) {
      // 製作JWT
      const tokenObject = { _id: foundUser._id, email: foundUser.email };
      const token = jwt.sign(tokenObject, process.env.PASSPORT_SECRET);
      return res.send({
        msg: "成功登入",
        token: "JWT " + token, //JWT 後面留一個空白!
        user: foundUser,
      });
    } else {
      // 密碼錯誤
      return res.status(401).send("密碼錯誤");
    }
  });
});
```

## user-model.js

### v1 小改

改一下comparePassword 內部

`cb` = `callback function`  

```js
userSchema.methods.comparePassword = async function (password, cb) {
  let result;
  try {
    result = await bcrypt.compare(password, this.password);
    return cb(null, result);
  } catch (e) {
    return cb(e, result);
  }
};
```

## 成功登入feedback:

```json
{
    "msg": "成功登入",
    "token": "JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI2NWFiZDA0ZGFiZDM0NjIyZTZmY2Y0ZjQiLCJlbWFpbCI6Im9uaUBnbWFpbC5jb20iLCJpYXQiOjE3MDU4MTk4Mjd9.NGTMDmTj-8UeTDUGTU0mmB0nzXEvh9gl-L7n4Qlfhfg",
    "user": {
        "_id": "65abd04dabd34622e6fcf4f4",
        "username": "onisan",
        "email": "oni@gmail.com",
        "password": "$2b$10$Zt3ZmiXN4P8vHp5tGmROduSXvEpomDrfzRSSWRjLJbu4U43Sqjq.u",
        "role": "student",
        "date": "2024-01-20T13:53:17.388Z",
        "__v": 0
    }
}
```

# (371) 驗證JWT邏輯補充

很快的補充一下JWT驗證的邏輯步驟：

1.

在 https://openid.net/specs/draft-jones-json-web-token-07.html#ExamplePlaintextJWT 可以看到，當我們要將使用者相關的資訊做成JWT時，會先取得兩個部分。第一，製作JWT所使用的方法。例如，

```json
{
    "typ":"JWT",
    "alg":"HS256"
}
```

設定了使用的演算法是HMAC SHA 256。第二，要被製作成JWT的資訊。例如，

```json
{
    "iss":"joe",
    "exp":1300819380,
    "http://example.com/is_root":true
}
```

以上的資訊我想要放入JWT內部。有了這兩個部分之後，我們分別將上面這兩個部分換成Base64url encoding。這是一種可以跟UTF-8互換的編碼方式。轉換後，我們得到：

- `eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9`

以及

- `eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ`  

`Part1` 及 `Part2`。

將`Part1`以及`Part2`串接後，放入HMAC演算法，生成

- `dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk`

將Part1, Part2以及Part3串接在一起，成為JWT：

```batch
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9.
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ.
dBjftJeZ4CVPmB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

4. 將JWT寄給使用者。

5. 使用者將JWT寄回給伺服器時，伺服器可以先取得JWT中的Part1以及Part2。將 Part1 從Base64url encoding換成UTF-8後，可以知道，當初伺服器是使用何種演算法生成Part 3。如果Part1被更改過，伺服器無法轉換回正確的物件，或是無法匹配使用的演算法時，會馬上發現Part1有問題，於是判定JWT無效。

6. 若Part1沒有配竄改過，伺服器將Part1, Part2 都換成Base64url encoding後，串接起來，使用步驟5知道的演算法，生成HMAC值。再去跟步驟4獲得的JWT中的Part3比較，即可知道JWT是否被竄改過。

# (372) 驗證JWT令牌

## Work Flow

建立 MERN > config > `passport.js` `v1`

 `index.js`  (MERN) 

建立 `course-route.js`  `v1`

Postman 使用 POST 登入 夾帶JWT

`localhost:8080/api/courses` 

`Body` : 不需要

`Headers` : 增加一項 Authorization 放入得到的JWT eyJhbG...

順利的話，postman可得到下面

```json
{
  _id: '65abd04dabd34622e6fcf4f4',
  email: 'oni@gmail.com',
  iat: 1705819827
}
```

並且如果錯誤

直接passport幫忙回傳 unAuthorized 

---

![](../../../Images/2024-01-21-19-18-45-image.png)

`passport.js` `v2`   JWT的策略設計

`course-route.js` `v2` 

![](../../../Images/2024-01-21-19-10-47-image.png)

`user-model.js` 修改一下instructor 原本寫成了teacher 

`auth.js`  小改回傳而已

![](../../../Images/2024-01-21-19-18-27-image.png)

![](../../../Images/2024-01-21-19-19-04-image.png)

![](../../../Images/2024-01-21-19-19-14-image.png)

![](../../../Images/2024-01-21-19-19-34-image.png)

> 如果 token 錯誤則 unauthorized 

`course-model.js` 寫錯了  應該是🔥students🔥而不是單數!!💡

### 跑去刪除剛剛新增的資料💡

show dbs 

show collections

db.courses  

> 自動幫我從course 變成複數  這是mongoose幫忙的! 💡

```json
MernDB> db.courses.find();
[
  {
    _id: ObjectId('65acfdbd0e7f1b5b7be6b8fe'),
    title: 'APEXTechnique',
    description: 'some tech in apex',
    price: 99,
    instructor: ObjectId('65acfba1c5b83d0cb06938f6'),
    student: [],
    __v: 0
  },
  {
    _id: ObjectId('65acfddb0e7f1b5b7be6b901'),
    title: 'APEXTechnique',
    description: 'some tech in apex',
    price: 99,
    instructor: ObjectId('65acfba1c5b83d0cb06938f6'),
    student: [],
    __v: 0
  }
]
MernDB> db.courses.deleteOne({title:'APEXTechnique'});
{ acknowledged: true, deletedCount: 1 }
```

>  重新 post 課程資料

```js
{
    "msg": "新課程保存成功",
    "savedCourse": {
        "title": "APEXTechnique",
        "description": "some tech in apex",
        "price": 99,
        "instructor": "65acfba1c5b83d0cb06938f6",
        "students": [],
        "_id": "65ad03556be07bbc1ffdf5c3",
        "__v": 0
    }
}
```

然後換學生測試

![](../../../Images/2024-01-21-19-49-30-image.png)

![](../../../Images/2024-01-21-19-49-37-image.png)

## index.js (MERN)

### v1 套用course-route、passport💡

💡特殊技巧 require直接把passport交出去💡

```js
const authRoute = require("./routes").auth;
const courseRoute = require("./routes").course;
const passport = require("passport");
require("./config/passport")(passport);


app.use("/api/user", authRoute);

// 只有登入系統的人，instructor才能夠製作，student才能註冊課程
// courseRoute應要被JWT保護
// 如果 req.header 沒有jwt則 req就應該被視為unauthorized
app.use(
  "/api/courses",
  passport.authenticate("jwt", { session: false }),
  courseRoute
);
```

## passport.js (config)

### v1 - 使用特殊技巧讓require、JWT有效💡

exports 函數 然後外面交出 `passport` 就能共用 `passport`  !

拆解出訊息部位、並且密碼解析data。  

所以 `require` 的 兩個蠻重要

```js
let JwtStrategy = require("passport-jwt").Strategy;
let ExtractJwt = require("passport-jwt").ExtractJwt;
const User = require("../models").user;

module.exports = (passport) => {
  let opts = {};
  opts.jwtFromRequest = ExtractJwt.fromAuthHeaderWithScheme("jwt");
  opts.secretOrKey = process.env.PASSPORT_SECRET;

  passport.use(
    new JwtStrategy(opts, function (jwt_payload, done) {
      console.log(jwt_payload);
    })
  );
};
```

### v2 - 策略設計(登入狀態之類)

```js
  passport.use(
    new JwtStrategy(opts, async function (jwt_payload, done) {
      //   console.log(jwt_payload);
      // {
      //    _id: '65abd04dabd34622e6fcf4f4',
      //    email: 'oni@gmail.com',
      //    iat: 1705819827
      // }
      try {
        let foundUser = await User.findOne({ _id: jwt_payload._id }).exec();
        if (foundUser) {
          return done(null, foundUser); // req.user 會得到 foundUser
        } else {
          return done(null, false);
        }
      } catch (e) {
        return done(e, false);
      }
    })
  );
```

## course-route.js (routes)

### v1 - 初步判斷與套用middleware

```js
const router = require("express").Router();
const Course = require("../models").course;
const courseValidation = require("../validation").courseValidation;

router.use((req, res, next) => {
  console.log("course route 接收request...");
  next();
});
module.exports = router;
```

### v2 - 新增貼文

先看數據是否合規範

在看使用者身分

```js
router.use((req, res, next) => {
  console.log("course route 接收request...");
  console.log("驗證身分通過");
  next();
});
router.post("/", async (req, res) => {
  //數據要先符合規範
  let { error } = courseValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  if (req.user.isStudent()) {
    return res.status(400).send("只有講師才能發布課程，請登入其他帳號");
  }
  let { title, description, price } = req.body;
  try {
    let newCourse = new Course({
      title,
      description,
      price,
      instructor: req.user._id,
    });
    let savedCourse = await newCourse.save();
    return res.send({ msg: "新課程保存成功", savedCourse });
  } catch (e) {
    return res.status(500).send("無法創建課程");
  }
});
```

## auth.js

只有小改回傳而已

```js
router.post("/register", async (req, res) => {
  // 確認傳入的資料 ， 是否符合規範
  let { error } = registerValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  // 確認信箱是否已註冊過
  const emailExist = await User.findOne({ email: req.body.email });
  if (emailExist) return res.status(400).send("信箱已被註冊過了");
  // 製作新用戶
  let { email, username, password, role } = req.body;
  let newUser = new User({ email, username, password, role });
  try {
    let savedUser = await newUser.save();
    return res.send({
      msg: "成功建立",
      savedUser: savedUser,
    });
  } catch (error) {
    return res.status(500).send("無法儲存使用者" + error);
  }
});
```

## user-model.js

teacher - > instructor

```js
  role: {
    type: String,
    enum: ["student", "instructor"],
    required: true,
  },
```

## course-model.js

 students 而不是 student 

```js
  students: {
    type: [String],
    default: [],
  },
```

## 心得

git commit -m "Project9 - section 372 驗證JWT 令牌，主要使用index.js{負責route管理塞入jwt驗證授權功能}、passport.js{實際設計策略的地方}、course-route.js{設計route，創建課程需要是講師}、user-model.js{修改一下原本寫成teacher 我希望是instructor}、auth.js{小改一下註冊回傳錯誤訊息，順便把error也發過去讓人明白}、course-model.js{設計錯了，應該是students而不是student}，大致上就是這樣，可以先把teacher改成instructor、student改成students、err回傳改好，才不會寫完還要刪除collection內的格式不符者"

# (373) 課程routes設定

## Work Flow

稍微整理了 PostMan 

`course-routes.js` `v1` 基本的取得課程而已

---

`course-routes.js` `v2` 更詳細展現instructor

影片介紹了 `populate` 用法 ，可以取得關聯而不是只有instructor的_id

⭐順便解說 cmd 那邊 mongosh 想要靠id取得 要寫下面這樣⭐

`db.courses.findOne({_id: ObjectId("65ad03556be07bbc1ffdf5c3")})`

---

`course-routes.js` `v3`  依照 id取得課程

`course-route.js` `v4` Patch 資源，不過這邊因為驗證所以只有全部輸入才能通過

課程要存在

身分要正確才能修改

`course-route.js` `v5`  delete資源

## course-route.js

### v1 - 基本的取得課程

```js
router.get("/", async (req, res) => {
  let foundCourse = await Course.find({}).exec();
  if (foundCourse) {
    res.send(foundCourse);
  }
});
```

POSTMAN  : 取得課程

```json
[
    {
        "_id": "65ad03556be07bbc1ffdf5c3",
        "title": "APEXTechnique",
        "description": "some tech in apex",
        "price": 99,
        "instructor": "65acfba1c5b83d0cb06938f6",
        "students": [],
        "__v": 0
    }
]
```

### v2 - populate 讓講師資訊更詳細!

```js
router.get("/", async (req, res) => {
  let foundCourse = await Course.find({})
    .populate("instructor", ["username", "email", "password"])
    .exec();
  if (foundCourse) {
    res.send(foundCourse);
  }
});
```

POSTMAN : 取得課程

```json
[
    {
        "_id": "65ad03556be07bbc1ffdf5c3",
        "title": "APEXTechnique",
        "description": "some tech in apex",
        "price": 99,
        "instructor": {
            "_id": "65acfba1c5b83d0cb06938f6",
            "username": "Umisan",
            "email": "umi@gmail.com",
            "password": "$2b$10$0pltauifHExYXLQrLxx3mu6.2N63nsjzoVwkzlRL1OTv7bykiZ3Vy"
        },
        "students": [],
        "__v": 0
    }
]
```

### v3 - 依照id找課程

請注意，使用的是 `req.params` 

```js
// 依照課程id 找課程
router.get("/:_id", async (req, res) => {
  let { _id } = req.params;
  try {
    let foundCourse = await Course.findOne({ _id })
      .populate("instructor", ["email", "username"])
      .exec();
    console.log(foundCourse);
    return res.send(foundCourse);
  } catch (e) {
    return res.status(500).send(e);
  }
});
```

POSTMAN : 取得課程 byID

```json
{
    "_id": "65ad03556be07bbc1ffdf5c3",
    "title": "APEXTechnique",
    "description": "some tech in apex",
    "price": 99,
    "instructor": {
        "_id": "65acfba1c5b83d0cb06938f6",
        "username": "Umisan",
        "email": "umi@gmail.com"
    },
    "students": [],
    "__v": 0
}
```

### v4 - 編輯課程patch

```js
// 更改課程
router.patch("/:_id", async (req, res) => {
  // 驗證數據是否符合規範
  let { error } = courseValidation(req.body);
  if (error) return res.status(400).send(error.details[0].message);
  let { _id } = req.params;
  // 先確認course存在與否
  try {
    let foundCourse = await Course.findOne({ _id });
    if (!foundCourse) {
      return res.status(400).send("查無該課程，無法進行內容更新");
    }
    //必須為該課程講師才能修改
    if (foundCourse.instructor.equals(req.user._id)) {
      let updatedCourse = await Course.findOneAndUpdate({ _id }, req.body, {
        new: true,
        runValidators: true,
      }).exec();
      return res.send({
        message: "課程已經更新成功",
        updatedCourse,
      });
    } else {
      return res.status(403).send("課程講師才能編輯");
    }
  } catch (err) {
    return res.status(500).send(err);
  }
});
```

### v5 刪除資源

```js
router.delete("/:_id", async (req, res) => {
  let { _id } = req.params;
  // 確認課程存在
  try {
    let foundCourse = await Course.findOne({ _id });
    if (!foundCourse) {
      return res.status(400).send("找不到課程，無法刪除");
    }
    // 必須是該講師才能刪
    if (foundCourse.instructor.equals(req.user._id)) {
      let deleteCourse = await Course.findOneAndDelete({ _id }).exec();
      return res.send("課程已被刪除");
    } else {
      return res.status(403).send("只有課程講師能刪除");
    }
  } catch (err) {}
});
```

# (374) Axios 補充說明

> 接下來要做關於 前端的部分、先做些事

fetch 之前有用過，回傳一個promise

# Work Flow

打開她給的資料後整理一下放到

`axiosExample` > `axios.js` 、`index.html` 



> **上網查**
> 
> **what does axios return**

![](../../../Images/2024-01-21-22-28-50-image.png)



> **⭐稍微注意⭐**
> 
> **html如果裡面想使用 axios 記得裝上cdn** 
> 
> [axios - Libraries - cdnjs - The #1 free and open source CDN built to make life easier for developers](https://cdnjs.com/libraries/axios) 

## Difference between fetch & axios

### fetch

`fetch` 需要透過 await o.json() 才能提取  

`fetch` 內建就有

### axios⭐⭐⭐

`axios` 只需要直接 o.data 就能得到 

`axios` npm i axios  `node.js` 才能使用!⭐⭐⭐

> browser無法安裝 ， browser要使用，可以用cdn

```js
async function example1() {
  try {
    // fetch returns Promise object
    let responseObject = await fetch(
      "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json"
    );
    let data = await responseObject.json();
    console.log(data);
  } catch (e) {
    console.log(e);
  }
}

async function example2() {
  // axios.get() returns a Promise object
  // Axios Response Object
  try {
    let axiosResponseObject = await axios.get(
      "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json"
    );
    console.log(axiosResponseObject.data);
  } catch (e) {
    console.log(e);
  }
}

example1();
example2();
```

# (375) React設定

# (376) React註冊使用者

# (377) React登入使用者

# (378) React課程頁面

# (379) React 搜尋課程

# (380) React註冊課程

# (381) Final Code

# (382) Heroku免費託管服務終止

# (383) Heroku 部屬網站
