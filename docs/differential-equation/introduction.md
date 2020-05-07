# グラフの描画

今回の本題に入る前に、Processing を用いた二次元グラフの描画について考えてみましょう。

例えば、$$y = f(x) = x^2$$のグラフを$$-1 \leq x \leq 1$$、$$-1 \leq y \leq 1$$の範囲で幅 600、高さ 600 のウィンドウに描画したいとします。
ここで、計算している空間と描画される画面の空間が異なることに注意しなければなりません。
それぞれの空間を **計算空間** と **画面空間** と呼ぶことにしましょう。

画面空間では左上が(0, 0)、右下が(width, height)であるのに対して、計算空間では左上が(-1, 1)、右下(1, -1)です。
Processing の描画命令は画面空間上の座標で行わなければいけないので、計算空間の座標を画面空間の座標に変換しなければいけません。
画面空間上での放物線 $$y = x^2$$ を 3 つのパラメータ$$a, b, c$$を用いて以下のように表しましょう。

$$
y = a (x - b)^2 + c
$$

この放物線は(width / 2, height / 2)を頂点とし、(0, 0)を通るので、$$a, b, c$$はそれぞれ以下のように求まります。

$$
\begin{aligned}
a &=  -\frac{2 \text{height}}{\text{width}^2} \\
b &= \frac{\text{width}}{2} \\
c &= \frac{\text{height}}{2}
\end{aligned}
$$

これに基づいてグラフの描画をするプログラムを作成すると以下のようになります。

```java
void setup() {
  size(600, 600);
}

void draw() {
  background(255);
  float y0 = f(0);
  for (int x = 1; x < width; ++x) {
    float y = f(x);
    line(x - 1, y0, x, y);
    y0 = y;
  }
}

float f(float x) {
  float a = -2.0 * height / width / width;
  float b = width / 2.0;
  float c = height / 2.0;
  return a * (x - b) * (x - b) + c;
}
```

実行結果は以下の通りです。

![Screen Shot 2019-05-06 at 4.47.15.png (15.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/06/28750/9eb6abb0-dbc7-4969-92aa-db4e47db6377.png)

$$y = x^2$$のグラフが$$-1 \leq x \leq 1$$、$$-1 \leq y \leq 1$$の範囲で正しく描画できていることが確認できます。

今回は二次関数だったので、画面空間上での一般式を導出することが容易でした。
どのような関数でも適用できるもう一つのアプローチとして、計算空間と画面空間の間の **座標変換** を考えてみましょう。

まずは一般的な議論として、一変数の座標変換を考えます。
$$a_l$$から$$a_r$$の範囲の$$a$$を$$b_l$$から$$b_r$$の範囲の$$b$$に変換します。
このとき、それぞれの範囲における$$a$$と$$b$$の比率は等しくなければいけません。
すなわち、$$a$$ と $$b$$ の関係は以下のようになります。

$$
\frac{a - a_l}{a_r - a_l} = \frac{b - b_l}{b_r - b_l}
$$

これを整理した次式によって$$a$$の空間の値を$$b$$の空間の値へと変換することができます。

$$
b = (b_r - b_l) \frac{a - a_l}{a_r - a_l} + b_l
$$

これを利用して、次式のように計算空間上の xLeft から xRight の範囲を、0 から width の範囲の画面空間へ座標変換することができます。

$$
\text{scaleX}(x) = \text{width} \frac{x - \text{xLeft}}{\text{xRight} - \text{xLeft}}
$$

$$y$$についても同様に、計算空間上の yBottom から yTop の範囲を、height から 0 の範囲の画面空間へ座標変換することができます。

$$
\text{scaleY}(y) = -\text{height} \frac{y - \text{yBottom}}{\text{yTop} - \text{yBottom}} + \text{height}
$$

$$y$$については、画面空間の上下が反転しているため、yBottom には height が、yTop には width が対応することに注意しましょう。

座標変換を用いた二次関数の描画プログラムは以下のようになります。

```java
float xLeft = -1;
float xRight = 1;
float yTop = 1;
float yBottom = -1;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float x0 = xLeft;
  float y0 = f(x0);
  float dx = (xRight - xLeft) / width;
  for (float x = x0 + dx; x < xRight; x += dx) {
    float y = f(x);
    drawLine(x0, y0, x, y);
    x0 = x;
    y0 = y;
  }
}

float f(float x) {
  return x * x;
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height *  (y - yBottom) / (yTop - yBottom) + height;
}

void drawLine(float x0, float y0, float x1, float y1) {
  line(scaleX(x0), scaleY(y0), scaleX(x1), scaleY(y1));
}
```

このような考え方は第 05 回で扱う **数値補間** につながります。

## 演習 1

$$y = \sin(x)$$のグラフを$$-\pi \leq x \leq \pi$$、$$-1 \leq y \leq 1$$の範囲で幅 800、高さ 400 のウィンドウに描画しましょう。

# 常微分方程式

未知関数とその導関数によって記述された方程式を **微分方程式** と呼びます。

微分方程式の中でも、一変数のみを含む微分方程式を **常微分方程式** と呼びます。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

以下は最も簡単な常微分方程式の例です。

$$
\frac{dx}{dt} = -x
$$

この常微分方程式は以下のような一般解を持ちます。

$$
x = a e^{-t}
$$

## 演習 2

$$a = {-1, -0.9, …, 1}$$について$$x = a e^{-t}$$のグラフを、$$-1 \leq t \leq 1$$、$$-1 \leq x \leq 1$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

# 常微分方程式の初期値問題

以下の常微分方程式について考えます。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

初期条件 $$x(t_0) = x_0$$ が与えられたときの$$x(T)$$ の値を求めたいとしましょう。

具体的に、前述の常微分方程式で考えてみます。

$$
\frac{d}{dt} x(t) = -x
$$

初期値 $$x(0) = 1$$ に対して $$x(1)$$ を求めてみましょう。

この常微分方程式の一般解は次式でした。

$$
x(t) = a e^{-t}
$$

初期値の条件を満たすのは $$a = 1$$ のときなので、$$x(1)$$ は以下のようになります。

$$
x(1) = \frac{1}{e}
$$

このように、常微分方程式と初期値が与えられ、任意の $$t$$ の時点での値を求める問題を常微分方程式の **初期値問題** と呼びます。
常微分方程式の一般解が得られている場合は容易に初期値問題の解を得ることができます。
一般解を用いた初期値問題の解を **厳密解** あるいは **解析解** と呼びます。

世の中に常微分方程式で表される現象は数多く存在し、初期値問題の工学的応用は数多く存在します。
しかしながら、それらの一般解を求めることは必ずしも容易ではありません。
そこで、数値計算によって近似的にその解を得ることを考えてみましょう。

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

# まとめ

今回は、常微分方程式の初期値問題とそれを解くための Euler 法について学びました。
しかしながら、Euler 法は精度の面で問題があります。
次回は、より高精度で実用的な解法について学びます。

# 演習の解答例

## 演習 1

```java
float xLeft = -PI;
float xRight = PI;
float yTop = 1;
float yBottom = -1;

void setup() {
  size(800, 400);
}

void draw() {
  background(255);

  float x0 = xLeft;
  float y0 = f(x0);
  float dx = (xRight - xLeft) / width;
  for (float x = x0 + dx; x < xRight; x += dx) {
    float y = f(x);
    drawLine(x0, y0, x, y);
    x0 = x;
    y0 = y;
  }
}

float f(float x) {
  return sin(x);
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
```

## 演習 2

```java
float xLeft = -1;
float xRight = 1;
float yTop = 1;
float yBottom = -1;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);
  for (float a = -1; a < 1; a += 0.1) {
    float x0 = xLeft;
    float y0 = f(x0, a);
    float dx = (xRight - xLeft) / width;
    for (float x = x0 + dx; x < xRight; x += dx) {
      float y = f(x, a);
      drawLine(x0, y0, x, y);
      x0 = x;
      y0 = y;
    }
  }
}

float f(float x, float a) {
  return a * exp(-x);
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

```

## 演習 3

```java
float xLeft = 0;
float xRight = 1;
float yTop = 5;
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
  return x;
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

## 演習 4

```java
float xLeft = 0;
float xRight = 1200;
float yTop = 0.0002;
float yBottom = 0;

float a0 = 3.24e-8;
float a1 = 2.94e-5;
float b0 = 3.00e-3;
float b1 = 1.36e-1;
float d = 0.1;
float A = a0 + a1 * d;
float B = b0 + b1 * d;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float x0 = a0 / b0;
  float t0 = 0;
  float tTarget = 1200;
  float dt = 1;
  for (float t = t0 + dt; t < tTarget; t += dt) {
    float x = x0 + dt * dx(t, x0);
    drawLine(t - dt, x0, t, x);
    x0 = x;
  }
}

float dx(float t, float x) {
  return A - B * x;
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

# 2 階常微分方程式

2 階微分を含んだ常微分方程式を **2 階常微分方程式** と呼びます。
次のような形の 2 階常微分方程式を考えてみましょう。

$$
\frac{d^2}{dt^2} x(t) + \alpha \frac{d}{dt} x(t) + \beta x(t) = 0
$$

このように右辺が 0 の 2 階常微分方程式を特に **2 階同次常微分方程式** と呼びます。

次式を特性方程式とします。

$$
\lambda^2 + \alpha \lambda + \beta = 0
$$

詳細な議論は省略しますが、特性方程式の解を $$\lambda_1, \lambda_2$$ 、$$C_1, C_2$$ を積分定数とすると、2 階同次常微分方程式の一般解は次のようになることが知られています。

$$
x(t) = C_1 e^{\lambda_1 t} + C_2 e^{\lambda_2 t}
$$

具体的に次のような 2 階常微分方程式を考えてみます。

$$
\frac{d^2}{dt^2} x(t) = -x(t)
$$

特性方程式は、

$$
\lambda^2 + 1 = 0
$$

であり、特性方程式の解は $$\lambda = \pm i$$（$$i^2 = -1$$）です。

一般解は次のように表されます。

$$
x(t) = C_1 e^{-i t} + C_2 e^{i t}
$$

これを Euler の公式 $$e^{i t} = \cos(t) + i \sin(t)$$ を用いて整理すると次のようになります。。

$$
x(t) = (C_1 + C_2) \cos(t) - (C_1 - C_2) i \sin(t)
$$

次のような初期値が与えられたときの初期値問題を考えてみましょう。

$$
\begin{aligned}
x(0) &= 1 \\
\frac{d}{dt} x(0) &= 0
\end{aligned}
$$

はじめに、$$C_1, C_2$$ を求めなければなりません。

以下の連立方程式より、

$$
\begin{aligned}
x(0) &= (C_1 + C_2) \cos(0) - (C_1 - C_2) i \sin(0) = 1 \\
\frac{d}{dt} x(0) &= -(C_1 + C_2) \sin(0) - (C_1 - C_2) i \cos(0) = 0
\end{aligned}
$$

$$C_1 = C_2 = \frac{1}{2}$$ であることがわかります。

そのため、この初期値問題の解析解は、

$$
x(t) = \cos(t)
$$

となります。

# 連立微分方程式

二つの変数が相互に影響しあっている現象について考えてみましょう。
そのような現象は例えば次のような **連立微分方程式** として表されます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= \alpha x(t) + \beta y(t) \\
\frac{d}{dt} y(t) &= \gamma x(t) + \delta y(t)
\end{aligned}
$$

最初はこの一般解を考えてみましょう。

ここから $$t$$ と $$x(t)$$ だけの微分方程式を導きます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= \alpha \frac{d}{dt} x(t) + \beta \frac{d}{dt} y(t) \\
\frac{d}{dt} y(t) &= \frac{1}{\beta} \left( \frac{d^2}{dt^2} x(t) - \alpha \frac{d}{dt} x(t) \right)
\end{aligned}
$$

と、

$$
y(t) = \frac{1}{\beta} \left( \frac{d}{dt} x(t) - \alpha x(t) \right)
$$

より、

$$
\frac{1}{\beta} \left( \frac{d^2}{dt^2} x(t) - \alpha \frac{d}{dt} x(t) \right) = \gamma x(t) + \frac{\delta}{\beta} \left( \frac{d}{dt} x(t) - \alpha x(t) \right)
$$

となります。

これを整理すると、

$$
\frac{d^2}{dt^2} x(t) - (\alpha + \delta) \frac{d}{dt} x(t) + (\alpha \delta - \beta \gamma) x(t) = 0
$$

となり、2 階同次常微分方程式が得られました。

$$y(t)$$ についても同様に、

$$
\frac{d^2}{dt^2} y(t) - (\alpha + \delta) \frac{d}{dt} y(t) + (\alpha \delta - \beta \gamma) y(t) = 0
$$

となります。

2 階同次常微分方程式の一般解を用いてこれらの連立方程式の一般解を得ることができます。

具体的に次のような初期値問題を考えてみましょう。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

$$x(t)$$ と $$y(t)$$ それぞれについて以下のような 2 階常微分方程式を得ることができます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) + x(t) &= 0 \\
\frac{d^2}{dt^2} y(t) + y(t) &= 0
\end{aligned}
$$

また、

$$
\begin{aligned}
\frac{d}{dt} x(0) &= 0 \\
\frac{d}{dt} y(0) &= -1
\end{aligned}
$$

です。

2 階同次常微分方程式の解析解と同様の計算を行うことで、

$$
\begin{aligned}
x(t) &= \cos(t) \\
y(t) &= -\sin(t)
\end{aligned}
$$

となります。

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

## 演習 1

次の初期値問題の解析解を求めましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= x(t) \\
x(0) &= 0 \\
\frac{d}{dt} x(0) &= -1
\end{aligned}
$$

そして、解析解と Euler 法による数値解をそれぞれ、$$0 \leq t \leq 10 \pi$$、$$-5 \leq x \leq 5$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

## 演習 2

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

# Runge-Kutta 法

これまでのような解が振動する場合には、Euler 法では十分な精度の解が得られないことが確認できました。
前回は、テイラー展開を用いて Euler 法を導出しました。
これを一般化し、より高次の項を使用する方法が **Runge-Kutta 法** です。
Runge-Kutta 法の導出や数理的な性質については次回に回しますが、一般的によく使われる 4 次の Runge-Kutta 法では、以下の式によって関数値を更新します。

$$
\begin{aligned}
x(t_1 + h) &= x(t_1) + h \frac{f_1 + f_2 + f_3 + f_4}{6} \\
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

## 演習 3

演習 1 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。

## 演習 4

演習 2 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。

# まとめ

今回は、常微分方程式の初期値問題について、Euler 法では正しく解が得られないケースを確認し、より高い精度を持つ解法である Runge-Kutta 法について学びました。
また、二階常微分方程式や連立微分方程式などの複数の変数を同時に扱う方法についても学びました。

# 演習の解答例

## 演習 1

解析解は $$x(t) = -\sin(t)$$ です。

### プログラム

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
  float[] x0 = {0, -1};
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

### 実行結果

![Screen Shot 2019-05-12 at 23.03.50.png (31.6 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/05844552-2635-488b-a412-d3d0f5e1bd8b.png)

## 演習 2

### プログラム

```java
float xLeft = 0;
float xRight = 100;
float yTop = 150;
float yBottom = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  int d = 2;
  float[] x0 = {80, 30};
  float[] x = new float[d];
  float[] dxt = new float[d];
  float t0 = 0;
  float tTarget = xRight;
  float dt = (tTarget - t0) / width;
  for (float t = t0 + dt; t < tTarget; t += dt) {
    dx(x0, dxt);
    for (int i = 0; i < d; ++i) {
      x[i] = x0[i] + dt * dxt[i];
      drawLine(t - dt, x0[i], t, x[i]);
      x0[i] = x[i];
    }
  }
}

void dx(float[] x, float[] dxt) {
  dxt[0] = 0.25 * x[0] - 0.01 * x[0] * x[1];
  dxt[1] = -x[1] + 0.01 * x[0] * x[1];
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

### 実行結果

![Screen Shot 2019-05-12 at 23.19.54.png (49.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/c484034c-7572-4ca3-b85e-1a086daf22de.png)

計算はできていますが厳密解とは大きな誤差があります。

## 演習 3

### プログラム

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
  float[] x0 = {0, -1};
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
      if (i == 0) {
        stroke(255, 0, 0);
        drawLine(t - dt, x0[i], t, x[i]);
      }
      x0[i] = x[i];
    }

    stroke(0, 0, 255);
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

### 実行結果

![Screen Shot 2019-05-12 at 23.06.47.png (18.2 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/7d178ed8-a3cf-40ae-923e-a5dfcf1edc9f.png)

## 演習 4

### プログラム

```java
float xLeft = 0;
float xRight = 100;
float yTop = 150;
float yBottom = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  int d = 2;
  float[] x0 = {80, 30};
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
      drawLine(t - dt, x0[i], t, x[i]);
      x0[i] = x[i];
    }
  }
}

void dx(float[] x, float[] dxt) {
  dxt[0] = 0.25 * x[0] - 0.01 * x[0] * x[1];
  dxt[1] = -x[1] + 0.01 * x[0] * x[1];
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

### 実行結果

![Screen Shot 2019-05-12 at 23.20.11.png (29.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/12/28750/a31282c6-1508-45f6-9170-20ffd84566d8.png)

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

## 演習 1

2 次の Runge-Kutta 法（Runge-Kutta の中点法、Heun 法、改良オイラー法）を用いて次の初期値問題を解きましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

# 微分方程式とアニメーションの描画

これまでは、 $$t$$ を横軸にとったグラフとして微分方程式の初期値問題の解を描画してきました。
今度は、微分方程式で表される量の時間変化を Processing のアニメーションで表してみましょう。

前回扱った以下の初期値問題を考えます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

これは以下の連立微分方程式の初期値問題に書き換えることができました。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

これをアニメーション表示するためには、$$t$$ が 0 から始まり、$$h$$ ずつ $$t$$ と $$x(t)$$ を更新していく様子を描画する必要があります。。
球の x 座標を $$x(t)$$、y 座標を 0 として、$$-2 \leq x \leq 2$$、$$-2 \leq y \leq 2$$の範囲で幅 600、高さ 600 のウィンドウに球の運動を描画するプログラムは以下のようになります。

```java
float xLeft = -2;
float xRight = 2;
float yTop = 2;
float yBottom = -2;

float[] x0 = {1, 0};
float t = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float h = 0.05;
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
    x0[i] = x[i];
  }

  float cx = x0[0];
  float cy = (xLeft + xRight) / 2;
  fill(0, 0, 255);
  drawEllipse(cx, cy, 50, 50);

  t += h;
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[1];
  dxt[1] = -x[0];
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height * (y - yBottom) / (yTop - yBottom) + height;
}

void drawEllipse(float cx, float cy, float w, float h) {
  ellipse(scaleX(cx), scaleY(cy), w, h);
}
```

ポイントは、現在の $$t$$ と $$x(t)$$ の状態を保持する変数 `t` と `x0` をグローバル変数に移し、1 回の `draw` 関数の実行ごとに `h` だけ `t` を更新するようにしたところです。
`h` の値を変えるとどうなるか確認してみましょう。

また、グローバル変数を利用することで、プログラム全体の見通しが悪くなってしまう場合があります。
このようなプログラムを、クラスなどを使用して整理する方法は各自で考えてみましょう。

## 演習 2

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

## 演習 3

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

# まとめ

Runge-Kutta 法の原理について紹介し、実際に 2 次の Runge-Kutta 法の導出を行いました。
また、微分方程式を用いた運動のアニメーションプログラムの作成法を紹介しました。

# 演習の解答例

## 演習 1

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

  float c2 = 2.0 / 3.0;
  float a1 = 1.0 / 4.0;
  float a2 = 3.0 / 4.0;

  int d = 2;
  float[] x0 = {0, -1};
  float[] x = new float[d];
  float[] tmp = new float[d];
  float[] k1 = new float[d];
  float[] k2 = new float[d];
  float t0 = 0;
  float tTarget = xRight;
  float h = (tTarget - t0) / width;

  for (float t = t0 + h; t < tTarget; t += h) {
    dx(x0, k1);
    for (int i = 0; i < d; ++i) {
      tmp[i] = x0[i] + h * c2 * k1[i];
    }
    dx(tmp, k2);

    for (int i = 0; i < d; ++i) {
      x[i] = x0[i] + h * (a1 * k1[i] + a2 * k2[i]);
      if (i == 0) {
        stroke(255, 0, 0);
        drawLine(t - h, x0[i], t, x[i]);
      }
      x0[i] = x[i];
    }
    stroke(0, 0, 255);
    drawLine(t - h, -sin(t - h), t, -sin(t));
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

## 演習 2

```java
float xLeft = -2;
float xRight = 2;
float yTop = 2;
float yBottom = -2;

float[] x0 = {1, 0};
float t = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float h = 0.05;
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
    x0[i] = x[i];
  }

  float cx = x0[0];
  float cy = (xLeft + xRight) / 2;
  fill(0, 0, 255);
  drawEllipse(cx, cy, 50, 50);

  t += h;
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[1];
  dxt[1] = -x[0];
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height * (y - yBottom) / (yTop - yBottom) + height;
}

void drawEllipse(float cx, float cy, float w, float h) {
  ellipse(scaleX(cx), scaleY(cy), w, h);
}
```

## 演習 3

```java
float xLeft = -0.5;
float xRight = 3.5;
float yTop = 2;
float yBottom = -2;

float[] x0 = {1.5, 0, 0.6, 0.6};
float t = 0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

  float h = 0.05;
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
    x0[i] = x[i];
  }

  int size = 30;
  float cx = x0[0];
  float cy = x0[1];
  fill(0, 0, 255);
  drawEllipse(cx, cy, size, size);

  fill(255, 0, 0);
  drawEllipse(0, 0, size, size);
  drawEllipse(3, 0, size, size);

  t += h;
}

void dx(float[] x, float[] dxt) {
  dxt[0] = x[2];
  dxt[1] = x[3];
  dxt[2] = -x[0] / pow(x[0] * x[0] + x[1] * x[1], 1.5) - (x[0] - 3) / pow((x[0] - 3) * (x[0] - 3) + x[1] * x[1], 1.5);
  dxt[3] = -x[1] / pow(x[0] * x[0] + x[1] * x[1], 1.5) - x[1] / pow((x[0] - 3) * (x[0] - 3) + x[1] * x[1], 1.5);
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height * (y - yBottom) / (yTop - yBottom) + height;
}

void drawEllipse(float cx, float cy, float w, float h) {
  ellipse(scaleX(cx), scaleY(cy), w, h);
}
```

# スカラー場とベクトル場

空間中の任意の点に何らかのスカラー値が定義されているとき、その空間を **スカラー場** と呼びます。
例えば、室内の温度は室内のどこでも観測することができます。
すなわち、温度は室内という空間中のどこにでも定義されており、温度の分布はスカラー場であると言えます。

同じように、空間中の任意の点に何らかのベクトル値が定義されている空間を **ベクトル場** と呼びます。
物理学における電磁場や重力場はベクトル場の一種です。
電磁場では電磁力が、重力場では重力が空間上の任意の点で定義されます。

# 流れ場

**流れ場** は、空間上の任意の点で流速が定義されているようなベクトル場の一種です。
大気や室内の空気の流れ、海の海流などが流れ場として見ることができるでしょう。

具体例を考えてみましょう。
二次元空間上のある点 $$(x, y)$$ の速度 $$(v_x, v_y)$$ が次式で与えられているとします。

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= 2 x \\
v_y = \frac{d}{dt} y &= - \frac{1}{2} y
\end{aligned}
$$

この式により好きな座標 $$(x, y)$$ における流速を知ることができます。

いくつかの点における流速の向きと大きさを Processing で描画してみましょう。

$$0 \leq x \leq 5$$、$$0 \leq y \leq 5$$ の範囲で $$x$$ と $$y$$ を 0.25 ずつずらした点での流速を `line` で表示します。

```java
float xLeft = 0;
float xRight = 5.0;
float yTop = 5.0;
float yBottom = 0.0;

void setup() {
  size(600, 600);
}

void draw() {
  background(255);

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

void dx(float[] x, float[] dxt) {
  dxt[0] = 2.0 * x[0];
  dxt[1] = -0.5 * x[1];
}

float scaleX(float x) {
  return width * (x - xLeft) / (xRight - xLeft);
}

float scaleY(float y) {
  return -height * (y - yBottom) / (yTop - yBottom) + height;
}

void drawVector(float[] x, float[] dxt) {
  float x1 = scaleX(x[0]);
  float y1 = scaleY(x[1]);
  float d = dist(0, 0, dxt[0], dxt[1]);
  float theta = atan2(dxt[1], dxt[0]);
  float x2 = d * cos(theta) + x1;
  float y2 = -d * sin(theta) + y1;
  line(x1, y1, x2, y2);
}
```

実行結果は以下のようになります。

![Screen Shot 2019-05-27 at 10.59.35.png (20.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/e427af73-c525-4651-bf56-6dab511f103e.png)

線分の向きが流速の向きを、長さが流速の大きさを表しています。

# 流線

流れのある川の中に、軽くて小さいボールを浮かばれば、ボールは川の流れに沿って移動していくことが想像できるでしょう。
流れ場 $$X$$ において、ある点 $$(x_0, y_0)$$ に粒子を置いたとき、粒子が流れ場に沿って移動する軌道は微分方程式の初期値問題を解くことで得られます。
そのようにして得られた粒子の軌跡を **流線** と呼びます。

先ほどの流れ場

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= 2 x \\
v_y = \frac{d}{dt} y &= - \frac{1}{2} y
\end{aligned}
$$

において、$$(1.0, 4.0)$$ を初期値とした流線を描いてみましょう。
基本的には微分方程式を解くだけです。

流線のイメージとして、軌跡を青色の線でアニメーションさせてみます。
プログラムの全体は以下のようになります。

```java
float xLeft = 0;
float xRight = 5.0;
float yTop = 5.0;
float yBottom = 0.0;

float[] x0 = {1.0, 4.0};
float t = 0;

void setup() {
  size(600, 600);
  background(255);
  drawVectorField();
}

void draw() {
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

void dx(float[] x, float[] dxt) {
  dxt[0] = 2.0 * x[0];
  dxt[1] = -0.5 * x[1];
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
```

実行結果は以下のようになります。

![Screen Shot 2019-05-27 at 12.27.02.png (22.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/33d6b7e7-197e-4ee7-991a-2081d15a1afe.png)

## ユーザーインタラクション

初期値を変えれば異なる流線が描かれることが確認できるでしょう。
しかし、毎回プログラム中の初期値を変更するのは面倒です。
例えば、マウスをクリックした位置から流線を描けると面白いでしょう。

`streamline.pde` に以下のコードを加えてみましょう。

```java
void mouseClicked() {
  x0[0] = (xRight - xLeft) * float(mouseX) / width + xLeft;
  x0[1] = (yBottom - yTop) * float(mouseY) / height + yTop;
}
```

`mouseClicked` 関数は、画面中をマウスでクリックされるたびに呼ばれるイベントハンドラです。
描画時とは逆の座標変換を用いて、画面空間の `mouseX` と `mouseY` から計算空間の座標を計算しています。

実行結果は以下のようになります。

![Screen Shot 2019-05-27 at 11.34.23.png (58.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/81a001b8-1a0c-48c8-abf1-1f712c122207.png)

## 演習 1

二次元空間上のある点 $$(x, y)$$ の速度 $$(v_x, v_y)$$ が次式で与えられている流れ場について、 $$\alpha, \beta, \gamma, \delta$$ を自由に決め、流線を描いてみましょう。

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= \alpha x + \beta y \\
v_y = \frac{d}{dt} y &=  \gamma x  + \delta y
\end{aligned}
$$

# 数値補間

ある流れ場について、微分方程式はわかっていないが、いくつかの点での流速が観測によって得られるような場合があります。
室内の空気の流れを厳密に微分方程式として表すことは難しいですが、何らかの観測装置を使えばある一点での空気の流れを測ることができるでしょう。
そして、観測点から少し離れた点での流速は、観測点での流速と極端に違うということはあまりないでしょう。
つまり、何点かの観測点があれば、観測点の間の観測していない点の流速を大体予測することができます。
このように、離散的な観測値からその間の値を求めることを **補間** と言います。

補間の方法は数多く存在しています。
以下では簡単な 1 次元と 2 次元の線形補間について説明します。

## 1 次元の線形補間

1 次元上の点 $$x_a$$ における観測値を $$v_a$$、点 $$x_b$$ における観測値を $$v_b$$ とします。
ある点 $$x$$ （$$ x_a \leq x \leq x_b$$ とする）の値 $$v$$ は次の式で表されます。

$$
v = \frac{x - x_a}{x_b - x_a} (v_b - v_a) + v_a
$$

以下のように整理してもいいでしょう。

$$
v = \frac{(x - x_a) v_b - (x - x_b) v_a}{x_b - x_a}
$$

$$v$$ がスカラー値であってもベクトル値であっても同じ式になります。
$$v$$ をスカラー値として見れば、この式は第 03 回で扱った座標変換と一致します。
具体的に計算する場合は、ベクトル値の各要素をスカラー値として補間することでベクトル値の補間が可能です。

## 2 次元の線形補間

2 次元空間状の 4 点 $$(x_a, y_a), (x_b, y_b), (x_c, y_c), (x_d, y_d)$$ を考えます。
単純な場合として、ここでは 4 点が長方形の頂点となっている場合を考えます。
すなわち、$$x_a = x_c$$、$$x_b = x_d$$、$$y_a = y_b$$、$$y_c = y_d$$ とします。
また、それぞれの点の値を $$v_a, v_b, v_c, v_d$$ とします。
長方形内の点 $$(x, y)$$ の値 $$v$$ は次のように補間できます。

$$
\begin{aligned}
v &= \frac{(y - y_a) v_2 - (y - y_c) v_1}{y_c - y_a} \\
v_1 &= \frac{(x - x_a) v_b - (x - x_b) v_a}{x_b - x_a} \\
v_2 &= \frac{(x - x_c) v_d - (x - x_d) v_c}{x_d - x_c}
\end{aligned}
$$

考え方としては、まず 2 点$$(x_a, y_a), (x_b, y_b)$$を結ぶ直前上で 1 次元の線形補間を行い $$v_1$$ を得ます。
同様に、$$(x_c, y_c), (x_d, y_d)$$の間の $$v_2$$ を得ます。
今度は、$$y_a, y_c$$の間を $$v_1$$ と $$v_2$$ で線形補間することで $$v$$ を得ます。

## 補間を用いた流線の計算

それでは、微分方程式の代わりに、2 次元正方形格子上の観測点での流速が与えられたときの流線の描画を行ってみましょう。

$$i = \{ 0, 1, 2, 3, 4 \}$$、$$j = \{ 0, 1, 2, 3, 4 \}$$ について、 $$x = j, y = i$$ の点の流速が以下の配列の `i * 5 + j` 番目の要素で表されているとします。

```java
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
```

関数 `dx` は、現在の座標 $$(x, y)$$ から線形補間を用いて流速を計算する必要があります。
はじめに $$(x, y)$$を含むような観測点 4 つを見つけます。
すなわち、 $$i = \{ 0, 1, 2, 3 \}$$、$$j = \{ 0, 1, 2, 3 \}$$ について、$$j \leq x < j + 1$$、$$i \leq y < i + 1$$ となるような $$(j, i)$$ を見つける必要があります。
そして、$$(j, i), (j, i + 1), (j + 1, i), (j + 1, i + 1)$$ の 4 点を用いて二次元の線形補間を `vx` と `vy` それぞれで行います。
実装例は以下のようになります。

```java
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
```

プログラムの全体は以下のようになります。

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

実行結果は以下のようになります。

![Screen Shot 2019-05-27 at 12.30.09.png (19.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/20fc4e7b-bb1f-4dd7-a2a1-927179bca394.png)

## 演習 2

`streamline_interpolation.pde` の `vx` と `vy` の値を自由に決め、流線を描いてみましょう。

# まとめ

微分方程式として記述された流れ場の初期値問題を解くことで流線を描けることを確認しました。
また、数値補間を用いて観測データから流線を描く方法を学びました。
流線を用いた流れ場の可視化は、気象学などの自然科学や、空調設計などの実用的なところでも幅広く用いられています。

# 演習の解答例

## 演習 1

```java
float xLeft = -10;
float xRight = 10;
float yTop = 10;
float yBottom = -10;

float[] x0 = {1.0, 4.0};
float t = 0;

void setup() {
  size(600, 600);
  background(255);
  drawVectorField();
}

void draw() {
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

void dx(float[] x, float[] dxt) {
  float a = 1.0;
  float b = 0.0;
  float c = 0.0;
  float d = 1.0;
  dxt[0] = a * x[0] + b * x[1];
  dxt[1] = c * x[0] + d * x[1];
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

$$\alpha = 1.0, \beta = 0.0, \gamma = 0.0, \delta = 1.0$$ とした結果は以下のようになります。

![Screen Shot 2019-05-27 at 11.49.59.png (46.2 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/11657738-b566-413f-bc4f-b95e174d91ce.png)

$$\alpha = -2.0, \beta = 1.0, \gamma = 1.0, \delta = -2.0$$ とした結果は以下のようになります。

![Screen Shot 2019-05-27 at 11.54.52.png (83.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/1ad10a08-f643-40ec-85e5-e6a0cd0bd23a.png)

$$\alpha = 0.0, \beta = 1.0, \gamma = -2.0, \delta = 0.1$$ とした結果は以下のようになります。

![Screen Shot 2019-05-27 at 11.57.28.png (79.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/edab4b5f-7464-45f4-9acd-a062689c819d.png)

## 演習 2

`vx` と `vy` の値を乱数で生成したプログラムは以下のようになります。
キーボードのキーを押すことで、`vx` と `vy` の再生成を行ないます。

```java
float xLeft = 0;
float xRight = 4.0;
float yTop = 4.0;
float yBottom = 0.0;

float[] vx = new float[25];
float[] vy = new float[25];

float[] x0 = {2.5, 2.5};
float t = 0;

void setup() {
  size(600, 600);
  background(255);
  resetV();
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

void resetV() {
  for (int i = 0; i < vx.length; ++i) {
    vx[i] = random(-1, 1);
    vy[i] = random(-1, 1);
  }
  drawVectorField();
}

void mouseClicked() {
  x0[0] = (xRight - xLeft) * float(mouseX) / width + xLeft;
  x0[1] = (yBottom - yTop) * float(mouseY) / height + yTop;
}

void keyPressed() {
  background(255);
  resetV();
}
```

実行例は以下のようになります。

![Screen Shot 2019-05-27 at 12.24.31.png (56.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/05/27/28750/84470587-0acd-4c29-8a74-108b1eb3ec49.png)
