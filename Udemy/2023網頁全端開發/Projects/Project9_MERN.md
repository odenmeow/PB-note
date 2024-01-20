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

# (371) 驗證JWT邏輯補充

# (372) 驗證JWT令牌

# (373) 課程routes設定

# (374) Axios 補充說明

# (375) React設定

# (376) React註冊使用者

# (377) React登入使用者

# (378) React課程頁面

# (379) React 搜尋課程

# (380) React註冊課程

# (381) Final Code

# (382) Heroku免費託管服務終止

# (383) Heroku 部屬網站
