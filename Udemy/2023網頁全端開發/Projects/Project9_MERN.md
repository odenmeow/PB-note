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

## Work Flow

先安裝 react

`npx create-react-app client`

`client` > `src` > 只留下 `App.js`  、 `index.js`

`client` > `public` > 只留下 `index.html` 

`npm install react-router-dom`

然後回去server 幫忙安裝 cors 並加入server > index.js 小改一下

---

> 回到REact

client > src > `App.js` `v1` 添加基本route功能

client > src > components >  把課堂提供的component.js 檔案都放到這邊

client > public > `index.html` `v2` 增加 bootstrap cdn內容 

client > src > `App.js` `v2` 引用Component 組件

並且 運行會出錯，需要改 `Components` > `nav-component.js` 登出的onClick先拿掉

## npm 要安裝的 packages

Project9_MERN \ client > `npm install react-router-dom`

Project9_MERN \ server > `npm i cors` 

因為是同源 所以要幫 `server` > `index.js` 添加 middleware cors

## index.html ( client / public / index.html)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

### v2 添加cdn

```js
 <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN"
      crossorigin="anonymous"
    />
    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-C6RzsynM9kWDrMNeT87bh95OGNyZPhcTNXj1NW7RuBCsyN/o0jlpcV8Qyq46cDfL"
      crossorigin="anonymous"
    ></script>
  </head>
```

## index.js ( server / index.js)

添加 cors 同源才能使用

```js
const passport = require("passport");
const cors = require("cors");

app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(cors());;
```

## App.js ( client / src / App.js)

添加 router 功能

```js
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return;
  <BrowserRouter>
    <Routes>
      <Route path="/" element></Route>
    </Routes>
  </BrowserRouter>;
}

export default App;
```

### v2 引用組件

```js
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<HomeComponent />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

## nav-component.js (client / src / component /...)

這邊有 onClick 但是還沒寫 `handleLogout` ，所以要先拿走 ，才能開啟畫面

```js
<li className="nav-item">
  <Link onClick={handleLogout} className="nav-link" to="/">
      登出
  </Link>
</li>
```

# (376) React註冊使用者

## Work Flow

接著要做出註冊的畫面，nav-component 那邊有 `/register` 

回到 `src` /`App.js` ( v1 )  ，添加Route

接著 `src` / `components` / `register-component.js` 內部 onClick /Change 都先移除

---

`client` 安裝 `axios` 

建立

`src` / `services` / `auth.service.js`  (v1) 編寫axios功能，register 先寫

然後 回到 `register-component.js`  `(v2)` 套用 剛剛寫的axios功能

> 改蠻多內容，useState 、const ()=>{} 寫很多處理小幫手=handler

建議export那邊不要直接new 會warning ! 

然後可以開網頁先測試畫面 <u>**server + client**</u> 都打開 ! 

<img src="../../../Images/2024-01-22-11-31-48-image.png" title="" alt="" width="173">

> **真的有建立，畫面沒有跳轉而已，因為還沒寫**

```json
{
    _id: ObjectId('65ade158949e372a69768c33'),
    username: 'apex',
    email: 'apex@gmail.com',
    password: '$2b$10$lNqZsZRDYRzjv/5y4BxdYuraLOPU/20o42vJ1lh0lXxBGnkOewsma',
    role: 'instructor',
    date: ISODate('2024-01-22T03:30:32.350Z'),
    __v: 0
  }
```

---

> 接著要做導向功能

`register-component.js` `v3`  導向功能添加! 

useNavigate 沒用過的東西

`App.js`  `v2` 添加login component上去

`login-component.js` `v1`修改一下，先拿掉 onClick onChange之類

> 重新註冊一次，然後這次名稱叫做maple 其他類推 密碼=兩次名稱
> 
> 然後應該要成功導向

---

> 這一次故意密碼少打

![](../../../Images/2024-01-22-11-46-52-image.png)

`register-component.js` `v4` 錯誤訊息 加工

![](../../../Images/2024-01-22-11-53-43-image.png)

- 透過技巧解決

**⭐請去後面 v4 有詳細說明⭐**

## package安裝

### client

`npm install axios` 

## App.js ( client / src /... )

## v1 添加register

```js
import RegisterComponent from "./components/register-component";
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<HomeComponent />} />
          <Route path="register" element={<RegisterComponent />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

### v2 - 添加login

也要記得去刪除onclick 否則無法直接先查看 ( 會報錯 )

```js
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Layout from "./components/Layout";
import HomeComponent from "./components/home-component";
import RegisterComponent from "./components/register-component";
import LoginComponent from "./components/login-component";
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<HomeComponent />} />
          <Route path="register" element={<RegisterComponent />} />
          <Route path="login" element={<LoginComponent />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
export default App;
```

## register-component.js ( src / components / ...)

### v1 - 先移除 on ...行為

```js
onChange={handleChangeUsername}
onChange={handleChangeEmail}
onChange={handleChangePassword}
onChange={handleChnageRole}
onClick={handleRegister}
```

### v2 - 套入AuthService 以及 ustState

多了userState 跟 const ，然後放回onClick onChange 

畫面跳轉還沒寫上

```js
import React, { useState } from "react";
import AuthService from "../services/auth.service";
const RegisterComponent = () => {
  let [username, setUsername] = useState("");
  let [email, setEmail] = useState("");
  let [password, setPassword] = useState("");
  let [role, setRole] = useState("");

  // 別使用function 除非另外設定 bind(this)
  const handleUsername = (e) => {
    setUsername(e.target.value);
  };
  const handleEmail = (e) => {
    setEmail(e.target.value);
  };
  const handlePassword = (e) => {
    setPassword(e.target.value);
  };
  const handleRole = (e) => {
    setRole(e.target.value);
  };
  const handleRegister = () => {
    AuthService.register(username, email, password, role)
      .then(() => {
        window.alert("註冊成功，即將導向登入頁面");
      })
      .catch((e) => {
        console.log(e);
      });
  };
  return (
    <div style={{ padding: "3rem" }} className="col-md-12">
      <div>
        <div>
          <label htmlFor="username">用戶名稱:</label>
          <input
            onClick={handleUsername}
            type="text"
            className="form-control"
            name="username"
          />
        </div>
        <br />
        <div className="form-group">
          <label htmlFor="email">電子信箱：</label>
          <input
            onChange={handleEmail}
            type="text"
            className="form-control"
            name="email"
          />
        </div>
        <br />
        <div className="form-group">
          <label htmlFor="password">密碼：</label>
          <input
            onChange={handlePassword}
            type="password"
            className="form-control"
            name="password"
            placeholder="長度至少超過6個英文或數字"
          />
        </div>
        <br />
        <div className="form-group">
          <label htmlFor="password">身份：</label>
          <input
            onChange={handleRole}
            type="text"
            className="form-control"
            placeholder="只能填入student或是instructor這兩個選項其一"
            name="role"
          />
        </div>
        <br />
        <button onClick={handleRegister} className="btn btn-primary">
          <span>註冊會員</span>
        </button>
      </div>
    </div>
  );
};

export default RegisterComponent;
```

### v3 - 導向的功能添加，useNavigate

```js
import { useNavigate } from "react-router-dom";

const RegisterComponent = () => {
  const navigate = useNavigate(); 



  const handleRegister = () => {
    AuthService.register(username, email, password, role)
      .then(() => {
        window.alert("註冊成功，即將導向登入頁面");
        navigate("/login");
      })
      .catch((e) => {
        console.log(e);
      });
  };

}
```

### v4 - 錯誤訊息⭐⭐⭐

透過console.log (e) 發現是 e.response.data 會回傳格式錯誤訊息

#### 原始樣貌

```js
return (
    <div style={{ padding: "3rem" }} className="col-md-12">
      <div>
        <div className="alert alert-danger">{message}</div>
```

![](../../../Images/2024-01-22-11-53-43-image.png)

#### 優化後⭐⭐⭐

> 透過 message && 來做事

```js
return (
  <div style={{ padding: "3rem" }} className="col-md-12">
    <div>
      {message && <div className="alert alert-danger">{message}</div>}
```

![](../../../Images/2024-01-22-11-58-32-image.png)

`if (emailExist) return res.status(400).send("信箱已被註冊過了");`

`if (error) return res.status(400).send(error.details[0].message);`

## auth.service.js (src / services /...)

### v1 - 編寫 axios 功能

```js
import axios from "axios";
const API_URL = "http://localhost:8080/api/user";

class AuthService {
  login() {}
  logout() {}
  register(username, email, password, role) {
    return axios.post(API_URL + "/register", {
      username,
      email,
      password,
      role,
    });
  }
}

export default new AuthService();
```

> 建議使用 變數名稱然後再回傳 不然會有warning

```js
let authService = new AuthService();
export default authService;
```

## login-component.js (components)

先拿掉 onChange onClick 避免報錯!

### v1 -拿掉onChange之類

import 也先註解

```js
// import React, { useState } from "react";
// import { useNavigate } from "react-router-dom";
// import AuthService from "../services/auth.service";

onChange={handleChangeEmail}
onChange={handleChangePassword}
onClick={handleLogin}
```

## 解釋 export default 是什麼

### 使用default

如果有使用 default 則 import可以自己命名

```js
// MyModule.js
const myModuleValue = 42;
export default myModuleValue;

// AnotherFile.js
import myValue from './MyModule';

console.log(myValue); // 42vice();
```

### 沒使用default

就不能隨便取，而是名稱要一樣

```js
// MyModule.js
export const myModuleValue = 42;

// AnotherFile.js
import { myModuleValue } from './MyModule';

console.log(myModuleValue); // 42
```

### 混合操作

```js
// MyModule.js
export const namedExport1 = "Hello";
export const namedExport2 = "World";
export default 42;

// AnotherFile.js
import { namedExport1, namedExport2 } from './MyModule'; // 使用具名匯入
import myDefaultValue from './MyModule'; // 使用預設匯入

console.log(namedExport1); // Hello
console.log(namedExport2); // World
console.log(myDefaultValue); // 42
```

# (377) React登入使用者

## Work Flow

先去 `auth.service.js` 製作 login 函數的功能

接著 `login-component.js` 完善登入細節

- 包含 錯誤訊息、登入轉向 `/profile` 、令牌儲存

`App.js` 這邊則是寫 profile-component畫面

寫好之後先不管 `profile-component.js`

---

> 先製作登出功能

`auth.service.js`  `v2` 增加logout內容、`getCurrentUser()` 取得目前使用者data

> 登出繼續製作功能

`nav-component.js`  在導覽列有登出按鈕 ，完成它的 onClick 登出功能

> profile製作

`profile-component.js`  `v1` 製作個人頁面 要取得個人資料

![](../../../Images/2024-01-22-15-57-44-image.png)

---

> 希望 登入後只有登出按鈕 反之亦然

> 使用 state lifting 概念將 currentUser往上搬動，讓其他route也能共享!

`profile-component.js` `v2`  state lifting 搬走useState的位置往上層移動

`App.js`  `v2` 開始作為源頭傳遞 

`Layout.js` 也要繼續往下傳遞

`nav-component.js`  `v2` 也往下傳遞 (導覽列畫面顯示)

`login-component.js` `v2` 登入後要設定一下currentUser ^^ 

---

> 確實無法偷渡

![](../../../Images/2024-01-22-16-26-25-image.png)

## auth.service.js

簡單製作一下該函數功能

### login功能

```js
class AuthService {
  login(email, password) {
    return axios.post(API_URL + "/login", { email, password });
  }
```

### v2 - logout功能、取得user

```js
logout() {
    localStorage.removeItem("user");
  }

getCurrentUser() {
    return JSON.parse(localStorage.getItem("user"));
  }
```

## login-component.js (src / components)

總結一下，`response.data.token` 是成功登入時返回的令牌，而 `e.response.data` 是錯誤時返回的錯誤信息。

除了基本行為，還要設定

> **JWT 儲存在localStorage**

然後成功登入要導向 `/profile` 

```js
import React, { useState } from "react";
import { useNavigate } from "react-router-dom";
import AuthService from "../services/auth.service";
const LoginComponent = (props) => {
  let [email, setEmail] = useState("");
  let [password, setPassword] = useState("");
  let [message, setMessage] = useState("");
  const navigate = useNavigate();
  const handleEmail = (e) => {
    setEmail(e.target.value);
  };
  const handlePassword = (e) => {
    setPassword(e.target.value);
  };
  // return res.send({
  //   msg: "成功登入",
  //   token: "JWT " + token, //JWT 後面留一個空白!
  //   user: foundUser,
  // });
  const handleLogin = async () => {
    try {
      let response = await AuthService.login(email, password);
      localStorage.setItem("user", JSON.stringify(response.data));
      window.alert("成功登入，即將導向個人頁面");
      navigate("/profile");
    } catch (e) {
      setMessage(e.response.data);
    }
  };
  return (
    <div style={{ padding: "3rem" }} className="col-md-12">
      <div>
        {message && <div className="alert alert-danger">{message}</div>}
        <div className="form-group">
          <label htmlFor="username">電子信箱：</label>
          <input
            onChange={handleEmail}
            type="text"
            className="form-control"
            name="email"
          />
        </div>
        <br />
        <div className="form-group">
          <label htmlFor="password">密碼：</label>
          <input
            onChange={handlePassword}
            type="password"
            className="form-control"
            name="password"
          />
        </div>
        <br />
        <div className="form-group">
          <button onClick={handleLogin} className="btn btn-primary btn-block">
            <span>登入系統</span>
          </button>
        </div>
      </div>
    </div>
  );
};

export default LoginComponent;
div>
    </div>
  );
};

export default LoginComponent;
```

### v2 - 使用透過prop傳遞的SetCurrentUser

setCurrentUser 登入後要設定一下!

```js
const LoginComponent = ({ currentUser, setCurrentUser }) => {

    const handleLogin = async () => {
    try {
      let response = await AuthService.login(email, password);
      localStorage.setItem("user", JSON.stringify(response.data));
      window.alert("成功登入，即將導向個人頁面");
      setCurrentUser(AuthService.getCurrentUser());
      navigate("/profile");
    } catch (e) {
      setMessage(e.response.data);
    }
  };);
```

## App.js ( src/... )

登入畫面增加 profile

```js
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Layout from "./components/Layout";
import HomeComponent from "./components/home-component";
import RegisterComponent from "./components/register-component";
import LoginComponent from "./components/login-component";
import ProfileComponent from "./components/profile-component";
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Layout />}>
          <Route index element={<HomeComponent />} />
          <Route path="register" element={<RegisterComponent />} />
          <Route path="login" element={<LoginComponent />} />
          <Route path="profile" element={<ProfileComponent />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

### v2 - state傳遞鏈源頭

基本上都有使用了，只有register沒設定props

```js
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Layout from "./components/Layout";
import HomeComponent from "./components/home-component";
import RegisterComponent from "./components/register-component";
import LoginComponent from "./components/login-component";
import ProfileComponent from "./components/profile-component";
import { useState } from "react";
import AuthService from "./services/auth.service";
function App() {
  let [currentUser, setCurrentUser] = useState(AuthService.getCurrentUser());
  return (
    <BrowserRouter>
      <Routes>
        <Route
          path="/"
          element={
            <Layout currentUser={currentUser} setCurrentUser={setCurrentUser} />
          }
        >
          <Route index element={<HomeComponent />} />
          <Route path="register" element={<RegisterComponent />} />
          <Route
            path="login"
            element={
              <LoginComponent
                currentUser={currentUser}
                setCurrentUser={setCurrentUser}
              />
            }
          />
          <Route
            path="profile"
            element={
              <ProfileComponent
                currentUser={currentUser}
                setCurrentUser={setCurrentUser}
              />
            }
          />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

## nav-component.js (src / components)

透過onClick配合登出

```js
import AuthService from "../services/auth.service";
const NavComponent = () => {
  // 不需要使用Nav 因為下面 Link 有使用to所以會自己導向
  const handleLogOut = () => {
    AuthService.logout();
    window.alert("登出成功");
  };



<li className="nav-item">
    <Link onClick={handleLogOut} className="nav-link" to="/">
        登出
    </Link>
</li>
```

### v2 - setCurrentUser 傳遞鏈、導覽畫面

> 如此 登出的時候才會同步影響

有些還要有身分才產生畫面

```js
const NavComponent = ({ currentUser, setCurrentUser }) => {
  // 不需要使用Nav 因為下面 Link 有使用to所以會自己導向
  const handleLogOut = () => {
    AuthService.logout();
    window.alert("登出成功");
    setCurrentUser(null);
  };


    {!currentUser && (
      <li className="nav-item">
        <Link className="nav-link" to="/register">
          註冊會員
        </Link>
      </li>
    )}
    {!currentUser && (
      <li className="nav-item">
        <Link className="nav-link" to="/login">
          會員登入
        </Link>
      </li>
    )}
    {currentUser && (
      <li className="nav-item">
        <Link onClick={handleLogOut} className="nav-link" to="/">
          登出
        </Link>
      </li>
    )}
    {currentUser && (
      <li className="nav-item">
        <Link className="nav-link" to="/profile">
          個人頁面
        </Link>
      </li>
    )}
    {currentUser && (
      <li className="nav-item">
        <Link className="nav-link" to="/course">
          課程頁面
        </Link>
      </li>
    )}
    {currentUser && currentUser.user.role === "instructor" && (
      <li className="nav-item">
        <Link className="nav-link" to="/postCourse">
          新增課程
        </Link>
      </li>
    )}
    {currentUser && currentUser.user.role === "student" && (
      <li className="nav-item">
        <Link className="nav-link" to="/enroll">
          註冊課程
        </Link>
      </li>
    )}
```

## profile-component.js (src / components)

### v1 取得個資

`useEffect` 讓第一次渲染，設定`currentUser` 資訊

```js
import { useState, useEffect } from "react";
import AuthService from "../services/auth.service";
const ProfileComponent = (props) => {
  let [currentUser, setCurrentUser] = useState(null);
  useEffect(() => {
    setCurrentUser(AuthService.getCurrentUser());
  }, []); //只要render就呼喚前面fn
  return (
    <div style={{ padding: "3rem" }}>
      {!currentUser && <div>在獲取您的個人資料之前，您必須先登錄。</div>}
      {currentUser && (
        <div>
          <h2>以下是您的個人檔案：</h2>

          <table className="table">
            <tbody>
              <tr>
                <td>
                  <strong>姓名：{currentUser.user.username}</strong>
                </td>
              </tr>
              <tr>
                <td>
                  <strong>您的用戶ID: {currentUser.user._id}</strong>
                </td>
              </tr>
              <tr>
                <td>
                  <strong>您註冊的電子信箱: {currentUser.user.email}</strong>
                </td>
              </tr>
              <tr>
                <td>
                  <strong>身份: {currentUser.user.role}</strong>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      )}
    </div>
  );
};

export default ProfileComponent;
```

### v2 - state Lifting

> 將 currentUser往上搬動，讓其他route也能共享!

連import的東西也不需要了，全靠傳遞過來的共享內容

```js
const ProfileComponent = ({ currentUser, setCurrentUser }) => {
  return (
```

## Layout.js

> 作為傳遞鏈的一部份 也要負責往下傳遞

```js
const Layout = ({ currentUser, setCurrentUser }) => {
  return (
    <>
      <Nav currentUser={currentUser} setCurrentUser={setCurrentUser} />
      <Outlet />
    </>
  );
};
```

# (378) React課程頁面



## Work Flow



新建 課程頁面組件

`src` > `components` >  `course-component.js` 

`App.js` `v1` 納入 `course-component.js`  `v1`

> **初步顯示 : 如果沒登入，給一個返回登入按鈕**
> 
> **如果有登入，暫無畫面**

---

> **接著做講師身分畫面呈現**

`course-component.js` `v2` 

---

> 講師id尋找課程

`server` > `routes` > `course-route.js`  `v1` 新增 route API (依照講師id尋找課程)

建立  `course.service.js` axios 服務。

前往 `course-component.js` `v2`，引用服務 (老師id可以找，但學生註冊的課程還沒做)

> course-component先做一些 ( v2 )，下面student API 做完再放上 ( v3 )

`server` > `routes` > `course-route.js` `v2` 新增 route API (依照學生id尋找課程)



`course.service.js` `v2` 學生註冊過的課程，Axios API

> 可以繼續寫 學生的API了 

回到 `course-component.js` `v3` 繼續完成studnet api 

---

- 學生那邊還沒有註冊課程的功能

- 老師那邊有新增的功能  ( 所以可以先查看老師的課程頁面 )

<img title="" src="../../../Images/2024-01-22-20-13-44-image.png" alt="" width="611">

---

回到 `course-component.js`  `v4` 修改console.log(data) ，

我們要放入 setCourseData(data.data) ; 讓state傳遞

> v4 改蠻多內容，最後可以呈現如下

![](../../../Images/2024-01-22-20-25-52-image.png)









## App.js

### v1 增加course頁面組件

```js
import CourseComponent from "./components/course-component";


         <Route
            path="course"
            element={
              <CourseComponent
                currentUser={currentUser}
                setCurrentUser={setCurrentUser}
              />
            }
         />
```



## course-component.js

### v1 課程畫面

如果沒有登入則重新導向 所以引用 `{useNavigate}` 

按下按鈕後 導回登入畫面

```js
import React from "react";
import { useNavigate } from "react-router-dom";
const CourseComponent = ({ currentUser, setCurrentUser }) => {
  const Navigate = useNavigate();
  const handleTakeToLogin = () => {
    Navigate("/login");
  };
  return (
    <div style={{ padding: "3rem" }}>
      {!currentUser && (
        <div>
          <p>必須先登入才能看到課程</p>
          <button
            className="btn btn-primary btn-lg"
            onClick={handleTakeToLogin}
          >
            回到登入畫面
          </button>
        </div>
      )}
      {currentUser && currentUser.user.role === "instructor" && (
        <div>
          <h1>歡迎來到講師的課程頁面</h1>
        </div>
      )}
      {currentUser && currentUser.user.role === "student" && (
        <div>
          <h1>歡迎來到講師的課程頁面</h1>
        </div>
      )}
    </div>
  );
};

export default CourseComponent;
default CourseComponent;

```

### v2 使用course.service.js

> student 先暫時放在那

```js
import React, { useEffect } from "react";
import CourseService from "../services/course.service";



const CourseComponent = ({ currentUser, setCurrentUser }) => {
  const Navigate = useNavigate();
  const handleTakeToLogin = () => {
    Navigate("/login");
  };
  const [courseData, setCourseData] = useState(null);
  useEffect(() => {
    if (currentUser) {
      let _id = currentUser.user._id;
      if (currentUser.user.role === "instructor") {
        CourseService.get(_id)
          .then((data) => {
            console.log(data);
          })
          .catch((e) => {
            console.log(e);
          });
      } else if (currentUser.user.role === "student") {
        // CourseService.get()
      }
    }
  }, []);

```

### v3 繼續完成學生api

可以透過getEnrolledCourse (bystudentID) 了

記得引用 useState

```js

import React, { useEffect, useState } from "react";




const CourseComponent = ({ currentUser, setCurrentUser }) => {
  const Navigate = useNavigate();
  const handleTakeToLogin = () => {
    Navigate("/login");
  };
  const [courseData, setCourseData] = useState(null);
  useEffect(() => {
    if (currentUser) {
      let _id = currentUser.user._id;
      if (currentUser.user.role === "instructor") {
        CourseService.get(_id)
          .then((data) => {
            console.log(data);
          })
          .catch((e) => {
            console.log(e);
          });
      } else if (currentUser.user.role === "student") {
        CourseService.getEnrolledCourse(_id)
          .then((data) => {
            console.log(data);
          })
          .catch((e) => {
            console.log(e);
          });
      }
    }
  }, []);
```



### v4 - setCourseData

> 稍微改一下內容 因為已查看了 data 樣貌

> 另外還有畫面呈現也修改了

```js
const CourseComponent = ({ currentUser, setCurrentUser }) => {
  const Navigate = useNavigate();
  const handleTakeToLogin = () => {
    Navigate("/login");
  };
  const [courseData, setCourseData] = useState(null);
  useEffect(() => {
    if (currentUser) {
      let _id = currentUser.user._id;
      if (currentUser.user.role === "instructor") {
        CourseService.get(_id)
          .then((data) => {
            // console.log(data);
            setCourseData(data.data);
          })
          .catch((e) => {
            console.log(e);
          });
      } else if (currentUser.user.role === "student") {
        CourseService.getEnrolledCourse(_id)
          .then((data) => {
            setCourseData(data.data);
          })
          .catch((e) => {
            console.log(e);
          });
      }
    }
  }, []);
  return (
    <div style={{ padding: "3rem" }}>
      {!currentUser && (
        <div>
          <p>必須先登入才能看到課程</p>
          <button
            className="btn btn-primary btn-lg"
            onClick={handleTakeToLogin}
          >
            回到登入畫面
          </button>
        </div>
      )}
      {currentUser && currentUser.user.role === "instructor" && (
        <div>
          <h1>歡迎來到講師的課程頁面</h1>
        </div>
      )}
      {currentUser && currentUser.user.role === "student" && (
        <div>
          <h1>歡迎來到講師的課程頁面</h1>
        </div>
      )}
      {currentUser && courseData && courseData.length != 0 && (
        <div style={{ display: "flex", flexWrap: "wrap" }}>
          {courseData.map((course) => {
            return (
              <div className="card" style={{ width: "18rem", margin: "1rem" }}>
                <div className="card-body">
                  <h5 className="card-title">課程名稱 :{course.title}</h5>
                  <p style={{ margin: "0.5rem 0rem" }} className="card-text">
                    {course.description}
                  </p>
                  <p style={{ margin: "0.5rem 0rem" }}>
                    {" "}
                    學生人數:{course.students.length}
                  </p>
                  <p style={{ margin: "0.5rem 0rem" }}>
                    課程價格 :{course.price}
                  </p>
                </div>
              </div>
            );
          })}
        </div>
      )}
    </div>
  );
};   }
    }
  }, []);
```



## course-route.js

### v1 - 依照講師id找課程

> **new route = 依講師id** 

```js
// 依照講師id尋找課程
router.get("/instructor/:instructor_id", async (req, res) => {
  let { instructor_id } = req.params;
  try {
    let foundCourse = await Course.find({ instructor: instructor_id })
      .populate("instructor", ["username", "email"])
      .exec();
    res.send(foundCourse);
  } catch (e) {
    return res.send(500).send(e);
  }
});
```

### v2 - 依照學生id找課程

> 讓學生註冊過的課程顯示出來

```js
// 用學生id尋找課程
router.get("/student/:student_id", async (req, res) => {
  let { student_id } = req.params;
  try {
    let foundCourses = await Course.find({ students: student_id })
      .populate("instructor", ["username", "email"])
      .exec();
    return res.send(foundCourses);
  } catch (error) {}
});
```

## course.service.js



### v1 - 講師擁有的課程+張貼課程

> **get / post** 
> 
> **取得資料、貼新課程** 

```js
import axios from "axios";
const API_URL = "http://localhost:8080/api/courses";
class CourseService {
  post(title, description, price) {
    let token;
    if (localStorage.getItem("user")) {
      token = JSON.parse(localStorage.getItem("user")).token;
    } else {
      token = "";
    }
    return axios.post(
      API_URL,
      { title, description, price },
      { headers: { Authorization: token } }
    );
  }

  // 講師擁有的課程
  get(_id) {
    let token;
    if (localStorage.getItem("user")) {
      token = JSON.parse(localStorage.getItem("user")).token;
    } else {
      token = "";
    }
    return axios.get(API_URL + "/instructor/" + _id, {
      headers: { Authorization: token },
    });
  }
}

let courseService = new CourseService();
export default courseService;

export default courseService;


```

### v2 學生註冊過的課程



```js
// 學生註冊過的課程
  getEnrolledCourse(_id) {
    let token;
    if (localStorage.getItem("user")) {
      token = JSON.parse(localStorage.getItem("user")).token;
    } else {
      token = "";
    }
    return axios.get(API_URL + "/student/" + _id, {
      headers: { Authorization: token },
    });
  }   });
  }
```













# (379) React 搜尋課程

# (380) React註冊課程

# (381) Final Code

# (382) Heroku免費託管服務終止

# (383) Heroku 部屬網站
