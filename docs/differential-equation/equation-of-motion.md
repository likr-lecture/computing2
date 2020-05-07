# 運動方程式の数値解法

振り子の運動において、糸が地面の鉛直方向となす角度 $$\theta$$ は以下の微分方程式表されます。

$$
\frac{d^2}{dt^2} \theta = - \frac{g}{l} \sin (\theta)
$$

ここで、$$l$$ は糸の長さ、$$g$$ は重力加速度を表します。
これは、角速度 $$\frac{d}{dt} \theta(t) = \omega(t)$$ を導入することで、以下の連立微分方程式で表されます。

$$
\begin{aligned}
\frac{d}{dt} \omega(t) &= - \frac{g}{l} \sin(\theta(t)) \\
\frac{d}{dt} \theta(t) &= \omega(t)
\end{aligned}
$$

$$\theta$$ の時間変化をアニメーションで描画するプログラムは以下のようになります。

```java
float xLeft = 0;
float xRight = 10;
float yTop = 0;
float yBottom = 10;

float g = 9.8;
float l = 300;

int d = 2;
float[] x0 = {PI / 4, 0};
float[] x = new float[d];
float[] tmp = new float[d];
float[] f1 = new float[d];
float[] f2 = new float[d];
float[] f3 = new float[d];
float[] f4 = new float[d];
float t = 0;
float dt = 0.1;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  dx(x0, f1);
  for (int i = 0; i < d; ++i) {
    tmp[i] = x0[i] + dt * f1[i] / 2;
  }
  dx(tmp, f2);
  for (int i = 0; i < d; ++i) {
    tmp[i] = x0[i] + dt * f2[i] / 2;
  }
  dx(tmp, f3);
  for (int i = 0; i < d; ++i) {
    tmp[i] = x0[i] + dt * f3[i];
  }
  dx(tmp, f4);

  for (int i = 0; i < d; ++i) {
    x[i] = x0[i] + dt * (f1[i] + 2 * f2[i] + 2 * f3[i] + f4[i]) / 6;
    x0[i] = x[i];
  }

  float cx = l * cos(x[0] + PI / 2) + width / 2;
  float cy = l * sin(x[0] + PI / 2);
  line(width / 2, 0, cx, cy);
  ellipse(cx, cy, 100, 100);

  t += dt;
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[1];
  dxt[1] = -g * sin(x[0]) / l;
}
```
