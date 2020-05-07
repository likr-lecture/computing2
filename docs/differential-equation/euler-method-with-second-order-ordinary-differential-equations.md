# 2 階常微分方程式への Euler 法の適用

連立微分方程式を応用することで、高階の常微分方程式も数値的に解くことが可能です。

次のような 2 階常微分方程式を考えます。

$$
\frac{d^2}{dt^2} x(t) + \alpha \frac{d}{dt} x(t) + \beta x(t) = 0
$$

ここで、

$$
\frac{d}{dt} x(t) = y(t)
$$

とおくことで、元の微分方程式は次のような連立微分方程式に書き換えることができます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -\beta x(t) -\alpha y(t)
\end{aligned}
$$

次の初期値問題を考えます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1 \\
\frac{d}{dt} x(0) &= 0
\end{aligned}
$$

これは次の初期値問題に書き換えられます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

なお、これは前節の連立微分方程式と全く同じです。

これを用いて $$x(t)$$ のグラフを表示するプログラムは以下の通りです。

```java
float xLeft = 0;
float xRight = 10 * PI;
float yTop = 5;
float yBottom = -5;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  int steps = 1200;
  int d = 2;
  float[] x0 = {1, 0};
  float[] x = new float[d];
  float[] dxt = new float[d];
  float t0 = 0;
  float tTarget = xRight;
  float dt = (tTarget - t0) / steps;
  for (float t = t0 + dt; t < tTarget; t += dt) {
    dx(x0, dxt);
    for (int i = 0; i < d; ++i) {
      x[i] = x0[i] + dt * dxt[i];
      if (i == 0) {
        stroke(255, 0, 0);
        drawLine(t - dt, x0[i], t, x[i]);
      }
      x0[i] = x[i];
    }

    stroke(0, 0, 255);
    drawLine(t - dt, cos(t - dt), t, cos(t));
  }
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[1];
  dxt[1] = -x[0];
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

実行結果は以下のようになります。

![Screen Shot 2019-05-12 at 23.03.28.png (32.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/99a4ad4d-8ea7-46ed-a98c-7042ec2ecd54.png)
