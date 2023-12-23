# (211) 基本設定



- 設定畫布跟基本的canvas前置作業。

```js
const c = document.getElementById("myCanvas");
const canvasHeight = c.height;
const canvasWidth = c.width;

const ctx = c.getContext("2d");

function drawCircle() {
  console.log("畫圓");
}
let game = setInterval(drawCircle, 25);

```

# (212) 彈跳球功能設定

```js
// 撞牆壁反彈與否
  if (circle_x + radius >= canvasWidth) {
    xSpeed *= -1;
  }
  if (circle_x + radius <= 0) {
    xSpeed *= -1;
  }
  if (circle_y + radius >= canvasHeight) {
    ySpeed *= -1;
  }
  if (circle_y + radius <= 0) {
    ySpeed *= -1;
  }
  // 改變移動方向
  circle_x += xSpeed;
  circle_y += ySpeed;
```

# (213) 地板彈跳設定

- 變數設定一下就有了。

- 粗略設置而已 沒有很精細考量

# (214) 製作磚塊

## 使用abs 計算距離才正確

這邊要避免無限迴圈 (如果磚塊重疊的邏輯沒寫好會產生)

```js
function isOverlapping() {
  if (brickArray.length < 2) {
    console.log("還沒重疊");
    return false;
  }
  let newBrick = brickArray[brickArray.length - 1];

  for (let i = 0; i < brickArray.length - 1; i++) {
    if (
      Math.abs(brickArray[i].x - newBrick.x) <= 50 &&
      Math.abs(brickArray[i].x - newBrick.x) >= 0 &&
      Math.abs(brickArray[i].y - newBrick.y) <= 50 &&
      Math.abs(brickArray[i].y - newBrick.y) >= 0
    ) {
      console.log("重疊了");
      return true;
    }
  }
  return false;
} 
```

## 不管誰在誰前面，總之保持50px的距離

- 不需要在那邊計算+50 -50 那會比較複雜化問題。



# (215) 磚塊邏輯設計



## splice 拿掉某一個撞到的磚塊!



# (216) 系統性能優化

## 透過 visible去做，雖然要額外判斷，但是陣列的整陀移動問題消失了。
