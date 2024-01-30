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

> 開始用React+React bootstrap

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

## react bootstrap css引用方式

```js
import "bootstrap/dist/css/bootstrap.min.css"; // 引入 Bootstrap CSS
```

---

> 搬遷 portfolio  Project3的主畫面過來Next.js

---

# section 3-1

## Work Flow

看一下如何引用 style 

事先分隔 global 樣式 跟 含有class selector的樣式

變成 `styles > globals.css` 和 `styles > Home.module.css` 

前者是給 _app.js 進行全域設定

後者是給 Home使用 (`pages > index.js`)  

---

去看看 `Home.module.css`

裡面教你怎麼 轉換成 next JSX格式，包含一個`tag` 持有多個class

或者 `tag`  的 className 含有 `-`  要怎麼辦。

以及過濾一些用不到的@media

---

`layout.js` 

接著處理畫面 nav 沒有黏在top的問題、

內部頁面頂端被遮住 的問題

---

## style的引用方式

```js
import style from "../styles/Home.module.css";
import styleB from "../styles/bubble.module.css";
export default function Home() {
  return 
```

## Home.module.css

### 持有多個className 透過 {``} 達成

```js
<Layout>
  <div className={styleB.bubbleBackgroundWrap}>
    <div className={`${styleB.bubble} ${styleB.x1}`}></div>
```

### className含有 - 的處理方式

```js
<section className={style["main-area"]}>
```

## layout.js (components)

### Nav fixed:top 設定固定位置

```js
<Navbar expand={false} fixed="top" className="bg-body-tertiary mb-3">
```

### 頂端內容被fixed遮住，增加margin

```js
</Navbar>
      <section style={{ marginTop: "10vh" }}>{children}</section>
```

# section 3-2

# Work Flow

修改泡泡點擊遮住的問題、

修改Icons8 免費使用的連結要求

修改內文 Footer呈現

修改文字 css  (技能描述那邊)

## 點擊穿越泡泡

> ** pointer-event: none**

```scss
.bubble {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  position: absolute;
  box-shadow: 0 20px 30px rgba(0, 0, 0, 0.2),
    inset 0px 10px 30px 5px rgba(252, 255, 255, 1);
  pointer-events: none; /* 設置 pointer-events 為 none，讓事件穿透 */
}
```

## 連結引用只需要用icons by Icons8就夠

## 修改一些內文、Footer呈現

## 技能描述使用map達成(main+contact)、tool不管

```js
const mainSikksLeft = ["Java", "SpringBoot", "HTML", "CSS", "JavaScript"];
const mainSikksRight = [
  "Node.js",
  "Express",
  "React",
  "Next",
  "MySQL",
  "MongoDB",
];




<div className={style["skill-more-information"]}>
                <div className={style["skill-more-information"]}>
                  <div className={style["skill-more-info-left"]}>
                    {mainSikksLeft.map((skill) => (
                      <p>{skill}</p>
                    ))}
                  </div>
                  <div className={style["skill-more-info-right"]}>
                    {mainSikksRight.map((skill) => (
                      <p>{skill}</p>
                    ))}
                  </div>
                </div>
              </div>
```

## 修改css

```css
  .skill-main-title {
  flex-basis: 33%;

  section.about-me
  section.description
  .skill-descriptions
  .skill-description
  .skill-more-information {
  flex-basis: 66%;
  flex-wrap: wrap;
  display: flex;
  align-items: center;
  margin-bottom: 0.5rem;
  margin-top: 0.5rem;

  justify-content: flex-start;
  flex-wrap: wrap;
  flex-grow: 1;
  align-items: flex-start;
}
.skill-more-info-left {
  flex-basis: 50%;
}
.skill-more-info-right {
  flex-basis: 50%;
}
section.about-me
  section.description
  .skill-descriptions
  .skill-description
  .skill-more-information
  p {
  display: block;
  margin: 0;
  margin-left: 0.5rem;
  margin-bottom: 0.5rem;
  background-color: rgba(242, 249, 205, 0.8);
  border-radius: 5px;
  width: fit-content;
}
```

![](../Images/2024-01-30-21-12-54-image.png)

# section 3-3

## Work Flow

改成 Nav Dropdown 群集專案

修改 Nav `CHEN I` 文字置中

## 修改 其他頁面 Link (群集NavDropDown)

```js
const UdemyProjectArray = [
  { link: "/Udemy/project-GoGame", name: "圍棋網站" },
  { link: "/Udemy/project-Tourism", name: "日本旅遊網站" },
  { link: "/Udemy/project-GPA", name: "成績計算網站" },
  { link: "/Udemy/project-SnakeGame", name: "貪食蛇" },
  { link: "/Udemy/project-Bricks", name: "彈跳球" },
  { link: "/Udemy/project-MongoDB", name: "MongoDB" },
];
const ISpanProjectArray = [
  { link: "/ISpan/project-FlipCard", name: "翻卡牌遊戲" },
  { link: "/ISpan/project-OShop", name: "OShop網路商店" },
];
const IndividualProjectArray = [
  { link: "/Oni/project-MyAutoMechine", name: "樹梅派~自動玩遊戲" },
  { link: "/Oni/project-Yoichi", name: "夜市APP" },
];


               <NavDropdown>
                  className={style.NavLinkHover}
                  title="獨立開發專案"
                  id={`offcanvasNavbarDropdown-Individual-expand-${false}`}
                >
                  {IndividualProjectArray.map((object, index) => (
                    <NavDropdown.Item
                      className={style.NavLinkHover}
                      key={object.name + index}
                      href={object.link}
                    >
                      {object.name}
                    </NavDropdown.Item>
                  ))}
                </NavDropdown>
```

## 修改 Nav 文字置中

```js
<Navbar.Brand
            href="#"
            style={{
              marginLeft: "auto",
              marginRight: "auto",
              transform: "translate(50%,0)",
            }}
          >
```

> **主畫面差不多就到這邊，剩下是分頁內容 !**

## 製作bubble Toggler (手動開關泡泡)

## 當我修改到一半，發現bubble錯誤

應該要改成這個才對

```js
<div className={`${styleB["bubble-background-wrap"]}`}> 
```

## 泡泡添加RWD策略

x4 ~ x10 直接透明化

```css
@media screen and (max-width: 1161px) {
  .x1 {
    animation: animateBubble 25s linear infinite,
      sideWays 2s ease-in-out infinite alternate;
    left: 5%;
    top: 0;
    transform: scale(0.25);
  }
  .x2 {
    animation: animateBubble 15s linear infinite,
      sideWays 2s ease-in-out infinite alternate;
    left: 15%;
    top: 0;
    transform: scale(0.4);
  }
  .x3 {
    animation: animateBubble 19s linear infinite,
      sideWays 2s ease-in-out infinite alternate;
    left: 25%;
    top: 0;
    transform: scale(0.6);
  }
  .x4 {
    opacity: 0;
  }
  .x5 {
    opacity: 0;
  }
```

## 把div移動到layout 這樣就能全域共享

保留button功能在home但是layout持有div bubble cluster

# 先偷偷部屬Render試試看!⭐⭐⭐⭐⭐

## 發生警告 要求使用Image 這個next標籤

使用方式 一定要填入width + height 

基本上都跟img一樣

但是我這邊發生錯誤

### Image 錯誤 (請css要另外添加類別)⭐⭐⭐

#### index.js 特殊技巧Image⭐⭐⭐

🔥 缺一不可喔 最好別亂少

```js
<section className={style.picture}>

<Image
            className={style.nextImage}🔥
            width={0}🔥
            height={0}🔥
            sizes="100vw"🔥
            style={{ width: "90%", height: "auto" }}🔥
            src="/project3/images/貓貓彎曲三角形.png"
            alt="貓貓頭貼"
          />
```

#### Home.module.css

注意寫上class

```css
section.resume section.picture img,
.nextImage {
  width: 90%;
  position: relative;
  z-index: 4;
}


@media screen and (max-width: 1161px) {
  section.about-me section.description {
    width: 80%;
  }
  section.resume section.picture img,
  .nextImage {
    position: relative;
    width: 70%;
  }
```

## 警告: 不能直接寫 【'】

### 錯誤在index.js 【I'm Oni】

```js
  return (
    <Layout>
      <section className={style["main-area"]}>
        <div className={style.info}>
          <h1>嗨，I&apos;m Oni.</h1>
```

## static site Vs web severices

不能夠用npm run build

改用 npm run export

## Next可以導出靜態網站給Render

### neext.config.js

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
  images: {
    unoptimized: true,
  },
  output: "export",
};

module.exports = nextConfig;

```

### 步驟:

npm run build

npm run export

![](../Images/2024-01-31-01-43-12-image.png)



然後成功Static site了 
