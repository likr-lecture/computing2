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
