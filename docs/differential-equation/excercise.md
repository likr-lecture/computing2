# 演習

## 演習 1

$$y = \sin(x)$$のグラフを$$-\pi \leq x \leq \pi$$、$$-1 \leq y \leq 1$$の範囲で幅 800、高さ 400 のウィンドウに描画しましょう。

## 演習 2

$$a = {-1, -0.9, …, 1}$$について$$x = a e^{-t}$$のグラフを、$$-1 \leq t \leq 1$$、$$-1 \leq x \leq 1$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

## 演習 3

以下の初期値問題について、$$x(t)$$のグラフを$$0 \leq t \leq 1$$、$$0 \leq x \leq 5$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

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

初期値 $$F(0)$$ を次式としたとき、$$F(t)$$ のグラフを $$0 \leq t \leq 1200$$、$$0 \leq F(t) \leq 0.0002$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

$$
F(0) = \frac{a_0}{b_0}
$$

## 演習 5

次の初期値問題の解析解を求めましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= x(t) \\
x(0) &= 0 \\
\frac{d}{dt} x(0) &= -1
\end{aligned}
$$

そして、解析解と Euler 法による数値解をそれぞれ、$$0 \leq t \leq 10 \pi$$、$$-5 \leq x \leq 5$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

## 演習 6

次の初期値問題の Euler 法による数値解を $$0 \leq t \leq 100$$、$$0 \leq x, y \leq 150$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

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

$$t = 0$$ から始まり、球の中心の x 座標を $$x(t)$$、y 座標を $$y(t)$$ とした時の球の運動のアニメーションを、$$-2 \leq x \leq 2$$、$$-2 \leq y \leq 2$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

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

以下の初期値が与えられたときの衛星の運動のアニメーションを、$$-0.5 \leq x \leq 3.5$$、$$-2 \leq y \leq 2$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

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

```java
float xLeft = 0;
float xRight = 4.0;
float yTop = 4.0;
float yBottom = 0.0;

float[] vx = {
  -2.0, -1.0, 0.0, 1.0, 2.0,
  -2.0, -1.0, 0.0, 1.0, 2.0,
  -2.0, -1.0, 0.0, 1.0, 2.0,
  -2.0, -1.0, 0.0, 1.0, 2.0,
  -2.0, -1.0, 0.0, 1.0, 2.0,
};

float[] vy = {
  2.0, 2.0, 2.0, 2.0, 2.0,
  1.0, 1.0, 1.0, 1.0, 1.0,
  0.0, 0.0, 0.0, 0.0, 0.0,
  -1.0, -1.0, -1.0, -1.0, -1.0,
  -2.0, -2.0, -2.0, -2.0, -2.0,
};

float[] x0 = {2.5, 2.5};
float t = 0;

void setup() {
  size(600, 600);
  background(255);
  drawVectorField();
}

void draw() {
  if (xLeft <= x0[0] && x0[0] < xRight && yBottom <= x0[1] && x0[1] < yTop) {
    float h = 0.01;
    int d = x0.length;
    float[] x = new float[d];
    float[] tmp = new float[d];
    float[] k1 = new float[d];
    float[] k2 = new float[d];
    float[] k3 = new float[d];
    float[] k4 = new float[d];

    dx(x0, k1);
    for (int i = 0; i < d; ++i) {
      tmp[i] = x0[i] + h * k1[i] / 2;
    }
    dx(tmp, k2);
    for (int i = 0; i < d; ++i) {
      tmp[i] = x0[i] + h * k2[i] / 2;
    }
    dx(tmp, k3);
    for (int i = 0; i < d; ++i) {
      tmp[i] = x0[i] + h * k3[i];
    }
    dx(tmp, k4);

    for (int i = 0; i < d; ++i) {
      x[i] = x0[i] + h * (k1[i] + 2 * k2[i] + 2 * k3[i] + k4[i]) / 6;
    }
    stroke(0, 0, 255);
    drawLine(x[0], x[1], x0[0], x0[1]);
    for (int i = 0; i < d; ++i) {
      x0[i] = x[i];
    }

    t += h;
  }
}

void dx(float[] x, float[] dxt) {
  int i = constrain(int(x[1]), 0, 3);
  int j = constrain(int(x[0]), 0, 3);
  float vx1 = (x[0] - i) * vx[(i + 1) * 5 + j] - (x[0] - i - 1) * vx[i * 5 + j];
  float vx2 = (x[0] - i) * vx[(i + 1) * 5 + (j + 1)] - (x[0] - i - 1) * vx[i * 5 + (j + 1)];
  dxt[0] = (x[1] - j) * vx2 - (x[1] - j - 1) * vx1;
  float vy1 = (x[0] - i) * vy[(i + 1) * 5 + j] - (x[0] - i - 1) * vy[i * 5 + j];
  float vy2 = (x[0] - i) * vy[(i + 1) * 5 + (j + 1)] - (x[0] - i - 1) * vy[i * 5 + (j + 1)];
  dxt[1] = (x[1] - j) * vy2 - (x[1] - j - 1) * vy1;
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height * (y - yBottom) / (yTop - yBottom) + height;
}

void drawLine(float x0, float y0, float x1, float y1) {
  line(scaleX(x0), scaleY(y0), scaleX(x1), scaleY(y1));
}

void drawVectorField() {
  float[] tmpX = new float[2];
  float[] tmpDx = new float[2];
  int steps = 20;
  float xStep = (xRight - xLeft) / steps;
  float yStep = (yTop - yBottom) / steps;
  for (float x = xLeft; x <= xRight; x += xStep) {
    for (float y = yBottom; y <= yTop; y += yStep) {
      tmpX[0] = x;
      tmpX[1] = y;
      dx(tmpX, tmpDx);
      drawVector(tmpX, tmpDx);
    }
  }
}

void drawVector(float[] x, float[] dxt) {
  float x1 = scaleX(x[0]);
  float y1 = scaleY(x[1]);
  float d = dist(0, 0, dxt[0], dxt[1]);
  float theta = atan2(dxt[1], dxt[0]);
  float x2 = d * cos(theta) + x1;
  float y2 = -d * sin(theta) + y1;
  stroke(0);
  line(x1, y1, x2, y2);
}

void mouseClicked() {
  x0[0] = (xRight - xLeft) * float(mouseX) / width + xLeft;
  x0[1] = (yBottom - yTop) * float(mouseY) / height + yTop;
}
```
