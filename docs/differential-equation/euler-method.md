# Euler 法

これまでに引き続き、常微分方程式が以下のように表されているとします。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

ただし、$$x(t)$$ はテイラー展開可能であるとします。

時刻 $$t_1$$ における関数値 $$x(t_1)$$ に対して、時間が $$h$$ 変化したときの関数値 $$x(t_1 + h)$$ について考えます。
$$x(t)$$ の $$t_1$$ のまわりでのテイラー展開を第 2 項まで表すと次式になります。

$$
x(t) = x(t_1) +  (t - t_1) x'(t_1)
$$

これは $$x(t)$$ を近似的に表しています。

そして、$$t = t_1 + h$$ のとき次式が得られます。

$$
x(t_1 + h) = x(t_1) + h f(t_1, x(t_1))
$$

これにより現在の時刻 $$t_1$$ の $$x(t_1)$$ がわかっているとき、時刻が$$h$$変化したときの値$$x(t_1 + h)$$ を近似的に求めることができます。
この式に基づいて、初期値$$x(t_0) = x_0$$ からはじまり、 $$x(t_0), x(t_0 + h), x(t_0 + 2h), \cdots, x(t_0 + nh)$$を求める方法を **Euler 法** と呼びます。

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
