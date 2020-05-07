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
