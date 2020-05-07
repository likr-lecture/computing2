# 連立微分方程式への Euler 法の適用

連立微分方程式についても、必ずしも一般解が容易に得られるわけではありません。
そのため、前回と同様に数値計算によって連立微分方程式を解くことを考えてみましょう。
まずは前回と同様に Euler 法を使用します。

$$n$$ 個の未知関数 $$x(t) = \left( x_0(t), x_1(t), \cdots, x_{n-1}(t) \right)$$ を次のように表します。

$$
\frac{d}{dt} x_i(t) = f_i (t, x(t)) \qquad i = 0, 1, \cdots, n - 1
$$

Euler 法における更新式は次のように表されます。

$$
x_i (t_1 + h) = x_i (t_1) + h f_i (t_1, x(t_1)) \qquad i = 0, 1, \cdots, n - 1
$$

すなわち、ある時点 $$t_1$$ において、全ての勾配 $$f(t_1, x(t_1))$$ を計算し、その後全ての未知関数を更新します。

実装上の工夫として、勾配を計算する関数 `dx` は `x` の配列を受け取り、勾配の配列 `dxt` に新たな値を書き込むようにします。
（勾配の配列を返す関数にすることもできますが、オブジェクトの生成が頻繁に行われるため実行効率が悪いです。）

先ほどの微分方程式、

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

を Euler 法によって解き、各時点の $$x(t), y(t)$$ の値をコンソールに表示するプログラムは以下のようになります。

```java
void setup() {
  background(255);
  noLoop();
}

void draw() {
  int d = 2;
  float[] x0 = {1, 0};
  float[] x = new float[2];
  float[] dxt = new float[2];

  float t0 = 0;
  float tTarget = 2 * PI;
  float dt = (tTarget - t0) / 100;

  for (float t = t0 + dt; t < tTarget; t += dt) {
    dx(x0, dxt);
    for (int i = 0; i < d; ++i) {
      x[i] = x0[i] + dt * dxt[i];
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

実行結果は以下のようになります。

```
1.0 -0.06283186
0.99605215 -0.12566371
0.98815644 -0.18824752
0.9763285 -0.25033522
0.9605995 -0.31167975
0.9410161 -0.372036
0.9176404 -0.4311618
0.8905497 -0.48881882
0.85983634 -0.5447737
0.8256072 -0.5987988
0.78798354 -0.6506733
0.74710053 -0.70018375

…

```

さらに、この結果をグラフで表すようにプログラムを変更します。
Euler 法によって得られた数値解を赤色、解析解を青色の線で表します。

プログラムの全体は以下のようになります。

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

  int steps = width;
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

実行結果は以下のようになります。

![Screen Shot 2019-05-12 at 22.58.42.png (43.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/83e4ae43-5e4e-4da6-861c-a8081c3f05c7.png)

$$t$$ が進むにつれて、数値解と解析解のズレが大きくなっていくことが確認できます。

変数 `steps` の値を変えて `dt` の値を変化させたときの結果を確認しておきましょう。

`steps = 300` のときの結果は以下のようになります。

![Screen Shot 2019-05-12 at 22.58.51.png (59.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/2f987d9a-1e15-4753-ab96-47bdc2a8425a.png)

`steps = 1200` のときの結果は以下のようになります。

`dt` の値が大きければ（`steps` が小さければ）誤差は大きく、`dt` の値が小さければ（`steps` が大きければ）誤差が大きくなることが確認できます。

![Screen Shot 2019-05-12 at 22.59.08.png (33.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/abd3f04c-107e-449e-84d0-2d3a7e061d0b.png)
