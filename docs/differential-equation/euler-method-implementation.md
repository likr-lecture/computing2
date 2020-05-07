# Euler 法の実装

Euler 法を用いて以下の初期値問題を計算してみます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

以下のプログラムでは、Euler 法によって得られた近似解と解析解をコンソールに表示しています。

```java
void setup() {
  background(255);
  noLoop();
}

void draw() {
  float x0 = 1;
  float t0 = 0;
  float tTarget = 1;
  float dt = (tTarget - t0) / 1000;
  for (float t = t0 + dt; t < tTarget; t += dt) {
    float x = x0 + dt * dx(t, x0);
    x0 = x;
  }
  println(x0);
  println(exp(-1));
}

float dx(float t, float x) {
  return -x;
}
```

実行結果は以下のようになります。

```console
0.36769542
0.36787945
```

さらにこれを改変して、$$x(t)$$のグラフを$$0 \leq t \leq 1$$、$$0 \leq x \leq 1$$の範囲で幅 600、高さ 600 のウィンドウに描画するプログラムは以下のようになります。

```java
float xLeft = 0;
float xRight = 1;
float yTop = 1;
float yBottom = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float x0 = 1;
  float t0 = 0;
  float tTarget = 1;
  float dt = (tTarget - t0) / 1000;
  for (float t = t0 + dt; t < tTarget; t += dt) {
    float x = x0 + dt * dx(t, x0);
    drawLine(t - dt, x0, t, x);
    x0 = x;
  }
}

float dx(float t, float x) {
  return -x;
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return height * (1 - (y - yBottom) / (yTop - yBottom));
}

void drawLine(float x0, float y0, float x1, float y1) {
  line(scaleX(x0), scaleY(y0), scaleX(x1), scaleY(y1));
}
```

実行結果は以下の通りです。

![Screen Shot 2019-05-06 at 7.12.19.png (15.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/06/28750/4c3cc622-1b41-44bb-ad6f-2eb70706a2ed.png)
