# section 1-1

## 透過webservices才搭建完成

```js
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node app.js",
    "build": "npm install"
  },
```

`BuildCommand` : npm install

`StartCommand` : node app.js

`Auto-Deployee` : Yes

記得填入環境變數

# section 1-2

## 解決revise可停留，趁機paid或fulfill，偷偷卡bug，導致奇怪錯誤

# section 1-3

## 解決時間忘記'new'的時候更新HTMLt_showUp()的部分

---

---

# section 2-1

準備開始做 react Next 版本的 前端畫面



發現直接像往常一樣使用 bootstrap會發生錯誤

需要安裝 react-bootstrap版本 才能

因此我去安裝了

## 安裝React Bootstrap



```batch
npm install react-bootstrap bootstrap
```

在 React 中使用 

`import { Button } from 'react-bootstrap';` 的方式

通常被描述為「less ideally」，是因為這樣的引入方式可能會導致 bundle 大小增加。

這是由於 `react-bootstrap` 的每個組件都被打包為獨立的模組，如果你只引入其中一個組件，整個模組也會被包含在 bundle 中。

相對地

`import Button from 'react-bootstrap/Button';` 的方式

僅引入了特定的 `Button` 組件，這樣可以減小 bundle 大小，因為只有你實際使用的模組件會被包含。

## render 錯誤server not match client

### 不能使用body標籤!!!

![](../Images/2024-01-28-23-34-31-image.png)

因為chrome 自己重新組織 所以導致不匹配!
