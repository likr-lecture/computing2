# Runge-Kutta 法

これまでのような解が振動する場合には、Euler 法では十分な精度の解が得られないことが確認できました。
前回は、テイラー展開を用いて Euler 法を導出しました。
これを一般化し、より高次の項を使用する方法が **Runge-Kutta 法** です。
Runge-Kutta 法の導出や数理的な性質については次回に回しますが、一般的によく使われる 4 次の Runge-Kutta 法では、以下の式によって関数値を更新します。

$$
\begin{aligned}
x(t_1 + h) &= x(t_1) + h \frac{f_1 + 2 f_2 + 2 f_3 + f_4}{6} \\
f_1 &= f \left(t_1, x(t_1) \right) \\
f_2 &= f \left(t_1 + \frac{h}{2}, x(t_1) + \frac{h}{2} f_1 \right) \\
f_3 &= f \left(t_1 + \frac{h}{2}, x(t_1) + \frac{h}{2} f_2 \right) \\
f_4 &= f \left(t_1 + h, x(t_1) + h f_3 \right)
\end{aligned}
$$

この更新式に基づいて前述の連立微分方程式を解いてコンソールに表示するプログラムは以下のようになります。

```java
void setup() {
  background(255);
  noLoop();
}

void draw() {
  int d = 2;
  float[] x0 = {1, 0};
  float[] x = new float[2];

  float[] tmp = new float[d];
  float[] f1 = new float[d];
  float[] f2 = new float[d];
  float[] f3 = new float[d];
  float[] f4 = new float[d];

  float t0 = 0;
  float tTarget = 2 * PI;
  float dt = (tTarget - t0) / 100;

  for (float t = t0 + dt; t < tTarget; t += dt) {
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
    println(x0[0], x0[1]);
  }
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[1];
  dxt[1] = -x[0];
}
```

Runge-Kutta 法での更新量を決定するために`f1`、`f2`、`f3`、`f4`、`tmp`の 4 つの実数型配列を用意しています。
$$f_i$$ を計算するたびに 1 回 `dx` を呼び出しているので、1 回の更新につき合計で 4 回 `dx` を呼び出します。。
`dx` の実装は変化しないことに注目しましょう。

実行結果は以下のようになります。

```
0.9980267 -0.06279051
0.9921147 -0.12533322
0.9822873 -0.1873813
0.9685832 -0.24868988
0.9510566 -0.30901697
0.92977655 -0.36812454
0.9048271 -0.42577928
0.8763068 -0.48175368
0.84432805 -0.5358268
0.8090171 -0.58778524
0.77051336 -0.637424
0.72896874 -0.6845471

…
```

さらにその結果をグラフに表示します。

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

  int d = 2;
  float[] x0 = {1, 0};
  float[] x = new float[d];
  float[] tmp = new float[d];
  float[] f1 = new float[d];
  float[] f2 = new float[d];
  float[] f3 = new float[d];
  float[] f4 = new float[d];
  float t0 = 0;
  float tTarget = xRight;
  float dt = (tTarget - t0) / width;
  for (float t = t0 + dt; t < tTarget; t += dt) {
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
      stroke(255, 0, 0);
      drawLine(t - dt, x0[i], t, x[i]);
      x0[i] = x[i];
    }

    stroke(0, 0, 255);
    drawLine(t - dt, cos(t - dt), t, cos(t));
    drawLine(t - dt, -sin(t - dt), t, -sin(t));
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

実行結果は以下の通りです。

![Screen Shot 2019-05-12 at 22.41.57.png (22.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/ff4a46c7-6ba7-48e6-ba6a-47b89019f1dd.png)

ほぼ完全に一致しているので、数値解を表す赤線はほとんど見えません。

# Runge-Kutta 法の導出

前回までに引き続き、以下のような常微分方程式を考えます。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

時刻 $$t$$ のときの関数値 $$x(t)$$ がわかっており、$$t$$ から $$h$$ だけ進んだ関数値 $$x(t + h)$$ について知りたいとします。

$$x(t)$$ の $$t_1$$ のまわりでのテイラー展開は以下の通りでした。

$$
x(t) = \sum_{m=0}^{\infty} \frac{(t - t_1)^m}{m!} x^{(m)}(t_1)
$$

これにより、$$x(t_1 + h)$$ は以下のようになります。

$$
x(t_1 + h) = \sum_{m = 0}^{\infty} \frac{h^m}{m!} x^{(m)}(t_1)
$$

右辺の級数を $$m = s$$ まで求め、残りの項を $$R_s$$ と表しましょう。

すなわち、

$$
x(t_1 + h) = \sum_{m = 0}^{s} \frac{h^m}{m!} x^{(m)}(t_1) + R_s
$$

となります。

さらに、

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

より、

$$
x(t_1 + h) = x(t_1) + \sum_{m = 1}^{s} \frac{h^m}{m!} f^{(m-1)}(t_1, x(t)) + R_s
$$

となります。

$$R_s$$ を誤差として切り捨てることで、$$x(t_1 + h)$$ の近似値を得ることができました。
この近似値は $$h^s$$ まではテイラー展開と厳密に一致するはずです。
ある近似値 $$\tilde{x}(t_1 + h)$$ が $$x(t_1 + h)$$ のテイラー展開の第 $$s$$ 項目まで一致するとき、$$\tilde{x}(t_1 + h)$$ の精度の次数は $$s$$ であると言います。

$$f(t, x(t))$$ の $$s - 1$$ 階微分が得られる場合、上記の式は直ちに計算することができます。
しかしながら、必ずしも与えられた微分方程式から高階の導関数を得られるわけではありません。
そのため別の方法で、精度の次数が $$s$$ となるような近似式を考えなければいけません。

$$x(t_1 + h)$$ を以下のような級数で表してみます。

$$
\begin{aligned}
x(t_1 + h) &= x(t_1) + h \sum_{m = 1}^s a_m k_m + R_s \\
k_m &= f(t_1 + c_m h, x(t_1) + c_m h k_{m - 1}) \qquad 2 \leq m \leq s \\
k_1 &= f(t_1, x(t_1))
\end{aligned}
$$

ここで $$a_1, \cdots, a_s$$ と $$c_2, \cdots, c_s$$ は未知の定数係数です。
この級数がテイラー展開の第$$s$$項目までとすれば以下のようになります。

$$
x(t_1) + h \sum_{m = 1}^s a_m k_m + R_s = x(t_1) + \sum_{m = 1}^s \frac{h^m}{m!} f^{(m - 1)}(t_1, x(t_1)) + R_s
$$

すなわち、以下の条件を満たすように $$a_1, \cdots, a_s$$ と $$c_2, \cdots, c_s$$ を決めます。

$$
\sum_{m = 1}^s a_m k_m = \sum_{m = 1}^s \frac{h^{m-1}}{m!} f^{(m-1)}(t_1, x(t_1))
$$

実際の係数の求め方は、$$s$$ に応じて変わります。
精度の次数が $$s$$ となるように上記の係数を求めた式を $$s$$ 次の Runge-Kutta 法と呼びます。
前回利用したのは 4 次の Runge-Kutta 法でした。

# 1 次の Runge-Kutta 法

はじめに精度の次数が 1（$$s = 1$$）となるような級数について考えてみましょう。

これは次式のようになり、

$$
a_1 k_1 = f(t_1, x(t_1))
$$

また、

$$
k_1 = f(t_1, x(t_1))
$$

なので、$$a_1 = 1$$ です。

すなわち、

$$
x(t_1 + h) = x(t_1) + h f(t_1, x(t_1))
$$

となります。

これは、Euler 法の近似式と一致しています。

# 2 次の Runge-Kutta 法

$$
a_1 k_1 + a_2 k_2 = f(t_1, x(t_1)) + \frac{h}{2} f^{(1)} (t_1, x(t_1))
$$

$$
\begin{aligned}
k_1 &= f(t, x(t)) \\
k_2 &= f(t + c_2 h, x(t) + c_2 h k_{i - 1})
\end{aligned}
$$

関数 $$g(h)$$ を $$h = 0$$ とした結果を $$\left[g(h)\right]_{h=0}$$ と表すことにすると、以下の条件式が成り立ちます。

$$
\begin{aligned}
a_1 \left[k_1\right]_{h=0} + a_2 \left[k_2\right]_{h=0} &= f(t_1, x(t_1)) \\
a_1 \left[ \frac{d}{dh} k_1\right]_{h=0} + a_2 \left[ \frac{d}{dh} k_2\right]_{h=0} &= \frac{1}{2} \frac{d}{dx} f(t_1, x(t_1))
\end{aligned}
$$

ここで、

$$
(a_1 + a_2) f(t_1, x(t_1)) = f(t_1, x(t_1))
$$

より、

$$
a_1 + a_2 = 1
$$

となります。

また、

$$
c_2 a_2 \frac{d}{dt}f(t, x(t)) = \frac{1}{2} \frac{d}{dt}f(t, x(t))
$$

より、

$$
a_2 c_2 = \frac{1}{2}
$$

となります。

すなわち、以下の条件のもとで係数を決定する必要があります。

$$
\begin{aligned}
a_1 + a_2 &= 1 \\
a_2 c_2 &= \frac{1}{2}
\end{aligned}
$$

これらの係数には自由度があります。
2 次の Runge-Kutta 法は係数の決め方によっていくつかの呼び方があります。

Runge-Kutta の中点法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= \frac{1}{2} \\
a_1 &= 0 \\
a_2  &= 1
\end{aligned}
$$

Heun 法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= \frac{2}{3} \\
a_1 &= \frac{1}{4} \\
a_2  &= \frac{3}{4}
\end{aligned}
$$

改良 Euler 法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= 1 \\
a_1 &= \frac{1}{2} \\
a_2  &= \frac{1}{2}
\end{aligned}
$$

いずれの場合にも精度の次数が 2 であるための条件が満たされています。

また、3 次以降の Runge-Kutta 法についても同様に導出可能です。
