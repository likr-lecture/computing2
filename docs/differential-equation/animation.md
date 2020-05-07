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
