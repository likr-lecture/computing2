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
