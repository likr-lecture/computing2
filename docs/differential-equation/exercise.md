# 演習

## 演習 1

$$y = \sin(x)$$のチャートを$$-\pi \leq x \leq \pi$$、$$-\pi \leq y \leq \pi$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

## 演習 2

$$a = {-1, -0.9, …, 1}$$について$$x = a e^{-t}$$のチャートを、$$-1 \leq t \leq 1$$、$$-1 \leq x \leq 1$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

## 演習 3

以下の初期値問題について、$$x(t)$$のチャートを$$0 \leq t \leq 5$$、$$0 \leq x \leq 5$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= x(t) \\
x(0) &= 1
\end{aligned}
$$

## 演習 4

ある時点 $$t$$ において、細胞の突然変異率 $$F(t)$$ の変化を照射される放射線の線量率 $$d$$ によって以下の式で表すとします。

$$
\begin{aligned}
\frac{d}{dt} F(t) &= A - B F(t) \\
A &= a_0 + a_1 d \\
B &= b_0 + b_1 d
\end{aligned}
$$

$$a_0, a_1, b_0, b_1, d$$は以下の値の定数です。

| 記号    | 値                       |
| ------- | ------------------------ |
| $$a_0$$ | $$3.24 \times 10^{-8}$$  |
| $$a_1$$ | $$2.94 \times 10^{-5}$$  |
| $$b_0$$ | $$3.00 \times 10^{-3}$$  |
| $$b_1$$ | $$1.36 \times 10^{-1}$$  |
| $$d$$   | $$1.00 \times 10^{-1} $$ |

初期値 $$F(0)$$ を次式としたとき、$$F(t)$$ のチャートを $$0\leq t \leq 1000$$ の範囲で描画しましょう。

$$
F(0) = \frac{a_0}{b_0}
$$

なお、$$F(t)$$の範囲は$$t$$に対して非常に小さいので、`line`の呼び出し時に縦軸の値を $$\frac{1000}{0.0002}$$ 倍すると良い。

## 演習 5

次の初期値問題の解析解を求めましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= x(t) \\
x(0) &= 0 \\
\frac{d}{dt} x(0) &= -1
\end{aligned}
$$

そして、解析解と Euler 法による数値解をそれぞれ、$$0 \leq t \leq 10 \pi$$、$$-5 \pi \leq x \leq 5 \pi$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

## 演習 6

次の初期値問題の Euler 法による数値解を $$0 \leq t \leq 150$$、$$0 \leq x, y \leq 150$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= \alpha x(t) - \beta x(t) y(t) \\
\frac{d}{dt} y(t) &= -\gamma y(t) + \delta x(t) y(t) \\
x(0) &= 80 \\
y(0) &= 30 \\
\alpha &= 0.25 \\
\beta &= 0.01 \\
\gamma &= 1.0 \\
\delta &= 0.01
\end{aligned}
$$

なお、この連立微分方程式は **Lotka-Volterra の方程式** として知られています。
Lotka-Volterra の方程式は生物の捕食被食関係による個体数の増減を表現する数理モデルです。

## 演習 7

演習 5 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。

## 演習 8

演習 6 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。

## 演習 9

2 次の Runge-Kutta 法（Runge-Kutta の中点法、Heun 法、改良オイラー法）を用いて次の初期値問題を解きましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

## 演習 10

以下の連立微分方程式の初期値問題を考えます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

$$t = 0$$ から始まり、球の中心の x 座標を $$x(t)$$、y 座標を $$y(t)$$ とした時の球の運動のアニメーションを、$$-2 \leq x \leq 2$$、$$-2 \leq y \leq 2$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

## 演習 11

2 つの天体が座標 $$(0, 0)$$ と $$(r_0, 0)$$ に固定されていて、それぞれの質量を $$M_1$$ と $$M_2$$ とします。
これらの天体の周りを巡る質量 $$m$$ の衛星の運動方程式は以下の微分方程式で表されます。

$$
\begin{aligned}
m \frac{d^2}{dt^2} x(t) &= -G\frac{M_1 m x(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -G\frac{M_2 m (x(t) - r_0)}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}} \\
m \frac{d^2}{dt^2} y(t) &= -G\frac{M_1 m y(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -G\frac{M_2 m y_(t)}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}}
\end{aligned}
$$

$$GM_1 = GM_2 = 1$$、$$r_0 = 3.0$$ とし、新たな未知関数 $$u(t)$$ と $$v(t)$$ を導入すると、以下のように書き直すことができます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= u \\
\frac{d}{dt} y(t) &= v \\
\frac{d}{dt} u(t) &= -\frac{x(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} - \frac{x(t) - 3}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}} \\
\frac{d}{dt} v(t) &= -\frac{y(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -\frac{y_(t)}{((x(t) - 3)^2 + y(t)^2)^{\frac{3}{2}}}
\end{aligned}
$$

以下の初期値が与えられたときの衛星の運動のアニメーションを、$$-0.5 \leq x \leq 3.5$$、$$-2 \leq y \leq 2$$の範囲で幅 300、高さ 300 のウィンドウに描画しましょう。

$$
\begin{aligned}
x(0) &= 1.5 \\
y(0) &= 0 \\
u(0) &= 0.6 \\
v(0) &= 0.6
\end{aligned}
$$

## 演習 12

二次元空間上のある点 $$(x, y)$$ の速度 $$(v_x, v_y)$$ が次式で与えられている流れ場について、 $$\alpha, \beta, \gamma, \delta$$ を自由に決め、流線を描いてみましょう。

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= \alpha x + \beta y \\
v_y = \frac{d}{dt} y &=  \gamma x  + \delta y
\end{aligned}
$$

## 演習 13

以下のプログラムの `vx` と `vy` の値を自由に決め、流線を描いてみましょう。

```javascript
const xLeft = 0;
const xRight = 4;
const yTop = 4;
const yBottom = 0;
let t1 = 0;
const x1 = [2.5, 2.5];

function setup() {
  createCanvas(300, 300);
  background(255);

  applyTransform();
  drawVectorField(20);
}

function draw() {
  if (x1[0] < xLeft || xRight < x1[0] || x1[1] < yBottom || yTop < x1[1]) {
    return;
  }
  applyTransform();

  const h = 0.1;
  const d = x1.length;
  const x2 = new Array(d);
  const tmp = new Array(d);
  const f1 = new Array(d);
  const f2 = new Array(d);
  const f3 = new Array(d);
  const f4 = new Array(d);

  const t2 = t1 + h;
  dx(t1, x1, f1);
  for (let j = 0; j < d; ++j) {
    tmp[j] = x1[j] + (h * f1[j]) / 2;
  }
  dx(t1, tmp, f2);
  for (let j = 0; j < d; ++j) {
    tmp[j] = x1[j] + (h * f2[j]) / 2;
  }
  dx(t1, tmp, f3);
  for (let j = 0; j < d; ++j) {
    tmp[j] = x1[j] + h * f3[j];
  }
  dx(t1, tmp, f4);

  for (let j = 0; j < d; ++j) {
    x2[j] = x1[j] + (h * (f1[j] + 2 * f2[j] + 2 * f3[j] + f4[j])) / 6;
  }

  stroke(0, 0, 255);
  line(x1[0], x1[1], x2[0], x2[1]);

  t1 = t2;
  for (let j = 0; j < d; ++j) {
    x1[j] = x2[j];
  }
}

function dx(t, xt, dxt) {
  const vx = [
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
  ];
  const vy = [
    [2.0, 2.0, 2.0, 2.0, 2.0],
    [1.0, 1.0, 1.0, 1.0, 1.0],
    [0.0, 0.0, 0.0, 0.0, 0.0],
    [-1.0, -1.0, -1.0, -1.0, -1.0],
    [-2.0, -2.0, -2.0, -2.0, -2.0],
  ];
  const i = constrain(int(xt[1]), 0, 3);
  const j = constrain(int(xt[0]), 0, 3);
  const vx1 = (xt[0] - i) * vx[i + 1][j] - (xt[0] - (i + 1)) * vx[i][j];
  const vx2 = (xt[0] - i) * vx[i + 1][j + 1] - (xt[0] - (i + 1)) * vx[i][j + 1];
  dxt[0] = (xt[1] - j) * vx2 - (xt[1] - (j + 1)) * vx1;
  const vy1 = (xt[0] - i) * vy[i + 1][j] - (xt[0] - (i + 1)) * vy[i][j];
  const vy2 = (xt[0] - i) * vy[i + 1][j + 1] - (xt[0] - (i + 1)) * vy[i][j + 1];
  dxt[1] = (xt[1] - j) * vy2 - (xt[1] - (j + 1)) * vy1;
}

function applyTransform() {
  resetMatrix();
  scale(width / (xRight - xLeft), height / (yBottom - yTop));
  translate(-xLeft, -yTop);
  strokeWeight((xRight - xLeft) / width);
}

function drawVectorField(steps) {
  const tmpX = new Array(2);
  const tmpDx = new Array(2);
  const xStep = (xRight - xLeft) / steps;
  const yStep = (yTop - yBottom) / steps;
  for (let x = xLeft; x <= xRight; x += xStep) {
    for (let y = yBottom; y <= yTop; y += yStep) {
      tmpX[0] = x;
      tmpX[1] = y;
      dx(0, tmpX, tmpDx);
      drawVector(tmpX[0], tmpX[1], tmpDx[0], tmpDx[1]);
    }
  }
}

function drawVector(x, y, dx, dy) {
  const d = dist(0, 0, dx, dy) / 50;
  const theta = atan2(dy, dx);
  stroke(0);
  line(x, y, d * cos(theta) + x, d * sin(theta) + y);
}

function mouseClicked() {
  x1[0] = ((xRight - xLeft) * float(mouseX)) / width + xLeft;
  x1[1] = ((yBottom - yTop) * float(mouseY)) / height + yTop;
}
```
