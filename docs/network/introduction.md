# ニュートンの運動方程式

ニュートンの運動方程式においてある物体にかかる力 $$F$$ 、質量 $$m$$、加速度 $$a$$ の関係は以下のようになっています。

$$
F = ma
$$

加速度 $$a$$ は速度 $$v$$ の微分であり、速度は位置 $$r$$ の微分であるため、運動方程式は位置 $$r$$ の 2 階微分により以下のように書くことができます。

$$
\frac{d^2}{dt^2} r = \frac{F(t)}{m} = a
$$

これは加速度 $$a$$ と位置 $$r$$ の関係を表した微分方程式であり、これを解くことで物体の運動を計算することができます。
ある $$t$$ における加速度 $$a(t)$$ が数式で表されている場合は Runge-Kutta 法を用いることで高い精度で解を得ることができます。
しかし、複数の物体が複雑に相互作用する場合にはそれが困難になるため、他の方法を考える必要があります。

# 速度 Verlet 法

$$r(t)$$ の $$t0$$ のまわりでのテイラー展開は以下のようになります。

$$
r(t) \simeq r(t_0) + (t - t_0) v(t_0) + \frac{(t - t_0)^2}{2} a(t_0)
$$

$$t0$$ から微小時間 $$\Delta$$ 変化した時の位置 $$r(t + \Delta)$$ は、上の式の $$t = t0 + \Delta$$ のときで得られます。

$$
r(t_0 + \Delta) \simeq r(t_0) + \Delta v(t_0) + \frac{\Delta^2}{2} a(t_0)
$$

これは位置 $$r(t)$$ の更新式となります。

上と同様に、$$r(t - \Delta)$$ は以下のようになります。

$$
r(t_0 - \Delta) \simeq r(t_0) - \Delta v(t_0) + \frac{\Delta^2}{2} a(t_0)
$$

これらを整理すると、以下の $$v(t)$$ の更新式が得られます。

$$
v(t_0 + \Delta) \simeq v(t_0) + \frac{\Delta}{2} \left(a(t_0 + \Delta) + a(t_0) \right)
$$

すなわち、ある時刻 $$t$$ における加速度 $$a(t)$$ が現在の位置 $$r(t)$$ によって得られたら、速度 $$v(t + \Delta)$$ と位置 $$r(t + \Delta)$$ を更新していくことができます。

速度 Verlet 法は、粒子同士の衝突を考慮する場合など、粒子にかかる力が刻一刻変化する場合に適していて、実際に分子動力学シミュレーションなどの分野で使用されています。

# 簡易版速度 Verlet 法

速度 Verlet 法では一般的には 2 次のテイラー展開が使用されます。
物理現象を正確に再現するためには精度が必要です。
しかし、次回以降で扱うネットワーク可視化では、粒子間の物理的な相互作用をメタファーにしますが、現象の再現ではないため精度は必要ありません。
そのため、より簡易的な更新式を用いることができます。

1 次のテイラー展開を用いて、$$\Delta = 1$$ とすれば、更新式は以下のようになります。

$$
\begin{aligned}
r(t + 1) &= r(t) + v(t) \\
v(t + 1) &= v(t) + a(t)
\end{aligned}
$$

すなわち、2 次元空間上の現在の座標 $$r = (x, y)$$ と速度 $$v = (v_x, v_y)$$ が得られており、加速度 $$a = (a_x, a_y)$$ を計算することができるとき、以下のような手順で座標を更新できます。

```java
x += vx;
y += vy;
vx += ax;
vy += ay;
```

大雑把な説明として、Verlet 法とは加速度を用いて速度を更新し、速度を用いて物体の座標を更新していく方法です。

# 等速直線運動

まず最初の例として、加速度が常に 0 であるとき、すなわち等速直線運動について考えます。
Verlet 法の更新式に基づいて Processing のプログラムを作成します。

```java
float x;
float y;
float vx;
float vy;
float r = 25;

void setup() {
  size(600, 600);
  x = r;
  y = height / 2;
  vx = 1;
  vy = 0;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;
  vx += 0;
  vy += 0;
}
```

実行結果は以下のようになります。
（以降では、アニメーションの様子をわかりやすくするために軌跡が残るようにプログラムを改変しています。）

![Screen Shot 2019-07-01 at 12.13.16.png (16.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/19244357-24da-4c4c-ac35-a4bab9ef23c9.png)

# 放物運動

次に放物運動を考えます。
例えばボールを真上に放り投げたとき、しばらく上昇してから下降を始めます。
これはボールが重力によって下方向に加速し続けるためです。

地球上の重力加速度は約 $$9.8 m/s^2$$ です。
しかし、1 ピクセルの距離や 1 フレームの時間を適切に定めることで、プログラム上では加速度の値は計算のしやすい値に設定することができます。

Processing でのプログラムは以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;

void setup() {
  size(600, 600);
  x = width / 2;
  y = height - r;
  vx = 0;
  vy = -10;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;
  vx += 0;
  vy += 0.1;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-01 at 12.14.42.png (42.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/8efb1c1c-2c21-4139-a6c7-ca9dd78af28f.png)

さらに、横方向の初速度を加え、投射運動をシミュレーションしてみます。

```java
float x;
float y;
float vx;
float vy;
float r = 25;

void setup() {
  size(600, 600);
  x = r;
  y = height - r;
  vx = 2.5;
  vy = -10;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;
  vx += 0;
  vy += 0.1;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-01 at 12.11.08.png (76.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/55256e10-e0ed-4030-b88c-ed010d581e89.png)

# 壁への衝突

画面の端を壁と見立てて、壁に当たったときに物体が反射する動作を上のプログラムに加えます。
反射は速度を-1 倍することで実現されます。

プログラムにすると以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
boolean boundX = false;
boolean boundY = false;

void setup() {
  size(600, 600);
  x = width / 2;
  y = height - r;
  vx = 25;
  vy = -30;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;

  if (x - r < 0 || width < x + r) {
    if (!boundX) {
      vx *= -1;
      boundX = true;
    }
  } else {
    boundX = false;
  }

  if (y + r > height) {
    if (!boundY) {
      vy *= -1;
      boundY = true;
    }
  } else {
    boundY = false;
    vy += 1;
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-01 at 12.31.52.png (78.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/952bfe0c-b1a3-487e-ba68-b50aa1299c86.png)

上のプログラムでは、壁への衝突によって減速が起こりませんでした。
衝突の度に減速の効果を加えると以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
boolean boundX = false;
boolean boundY = false;
float restitution = 0.9;

void setup() {
  size(600, 600);
  x = width / 2;
  y = height - r;
  vx = 25;
  vy = -30;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;

  if (x - r < 0 || width < x + r) {
    if (!boundX) {
      vx *= -restitution;
      boundX = true;
    }
  } else {
    boundX = false;
  }

  if (y + r > height) {
    if (!boundY) {
      vy *= -restitution;
      boundY = true;
    }
  } else {
    boundY = false;
    vy += 1;
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-01 at 12.28.33.png (130.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/63f473e7-8c92-43b2-b7f7-a78be82e9f10.png)

# 力の加算

物体に複数の力がかかっている場合、物体の加速度はそれぞれの力の影響力の加算で表すことができます。

例えば、空気抵抗を考慮した自由落下では、物体にかかる力は以下のように表されます。

$$
\begin{aligned}
F &= F_1 + F_2 \\
F_1 &= mg \\
F_2 &= kv
\end{aligned}
$$

物体の運動をシミュレーションする際には、$$F_1$$ と $$F_2$$ による加速度をそれぞれ別で計算して足し合わせることができます。
プログラムにすると以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;

void setup() {
  size(600, 600);
  x = width / 2;
  y = r;
  vx = 0;
  vy = 100;
}

void draw() {
  background(255);
  fill(0);
  ellipse(x, y, 2 * r, 2 * r);

  x += vx;
  y += vy;

  vx += 0;
  vy += 1;
  vy += -0.25 * vy;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-01 at 12.32.59.png (28.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/c7d97fca-c5f9-4c47-bfe0-2e4c5c52c690.png)

# 天体の運動

物体にかかる力が刻一刻と変化するような運動の例として、天体の運動を取り上げます。
画面の中心に固定された天体があり、その周りを衛星が周回します。

万有引力定数を $$G$$、中心の天体の質量を $$M$$、中心の天体と衛星の距離を $$d$$ とすると、衛星が中心の天体から受ける力による加速度は以下のように表されます。

$$
a(t) = -\frac{GM}{d^2}
$$

これを用いて衛星の軌道をシミュレーションしてみます。

```java
float GM = 100;
float r1 = 10;
float r2 = 2.5;
float d0 = 360;
float cx, cy;
float vx, vy, x, y;
int L = 5000;
float[] trailX = new float[L];
float[] trailY = new float[L];

void setup() {
  size(800, 800);
  cx = width / 2;
  cy = height / 2;
  vx = 0;
  vy = sqrt(GM / d0);
  x = width / 2 + d0;
  y = height / 2;
  for (int i = 0; i < L; ++i) {
    trailX[i] = x;
    trailY[i] = y;
  }
}

void draw() {
  background(0);

  float t = atan2(y - cy, x - cx);
  float d = dist(cx, cy, x, y);

  if (d > r1 + r2) {
    vx += -GM / (d * d) * cos(t);
    vy += -GM / (d * d) * sin(t);
    x += vx;
    y += vy;

    for (int i = L - 1; i > 0; --i) {
      trailX[i] = trailX[i - 1];
      trailY[i] = trailY[i - 1];
    }
    trailX[0] = x;
    trailY[0] = y;
  }

  stroke(255);
  for (int i = 1; i < L; ++i) {
    line(trailX[i - 1], trailY[i - 1], trailX[i], trailY[i]);
  }

  noStroke();
  fill(0, 0, 255);
  ellipse(cx, cy, 2 * r1, 2 * r1);
  fill(255, 255, 0);
  ellipse(x, y, 2 * r2, 2 * r2);
}
```

実行結果は以下のようになります。

## 演習 1

上記のプログラムにおいて、衛星の初速 `vx`, `vy` や初期距離 `d0` を変更した時の衛星の軌道を観察しましょう。

# 多体問題

上のプログラムを複数の天体が重力で引き合うのように改変すると以下のようになります。

```java
int n = 10;
float[] GM = new float[n];
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 10;

void setup() {
  size(800, 800);
  float v0 = 0.05;
  GM[0] = 100;
  x[0] = width / 2;
  y[0] = height / 2;
  for (int i = 1; i < n; ++i) {
    GM[i] = random(10, 50);
    x[i] = random(width);
    y[i] = random(height);
    vx[i] = v0 * cos(random(TWO_PI));
    vy[i] = v0 * sin(random(TWO_PI));
  }
}

void draw() {
  background(0);

  for (int i = 1; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
  for (int i = 1; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i != j) {
        float t = atan2(y[j] - y[i], x[j] - x[i]);
        float d = dist(x[i], y[i], x[j], y[j]);
        vx[i] += GM[i] / (d * d) * cos(t);
        vy[i] += GM[i] / (d * d) * sin(t);
      }
    }
  }

  for (int i = 0; i < n; ++i) {
    noStroke();
    fill(255, 255, 0);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}
```

## 演習 2

運動方程式に基づいて、自由な物体の運動をシミュレーションしてみましょう。

例）単振り子運動

# まとめ

（簡易版）速度 Verlet 法によって、速度と位置を更新していくことで物体の運動をシミュレーションできることを確認しました。
次回以降は、これを応用してネットワーク構造の可視化に取り組みます。

# 演習問題の解答例

## 演習 1

初速を落としていくと、中央の天体により近くような楕円軌道になります。

![Screen Shot 2019-07-01 at 12.47.49.png (24.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/eb09efed-e8da-4724-9e0a-98dfa5dafcc0.png)

初速を一定以上落とすと中央の天体に衝突します。

![Screen Shot 2019-07-01 at 12.48.55.png (20.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/f0db50af-4f74-4688-b537-bb9940a1fa25.png)

一方で、初速を上げすぎると、中央の天体の重力を振り切ってしまいます。

![Screen Shot 2019-07-01 at 12.49.33.png (22.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/8255b655-1fc2-407a-8159-ddd86aa6967e.png)

# 色空間と colorMode

色をランダムに生成することを考えてみましょう。
例えば以下のように RGB の各成分を `random` 関数によって生成することができます。

```java
size(600, 600);
background(255);
int n = 10;
int d = width / n;
for (int i = 0; i < n; ++i) {
  for (int j = 0; j < n; ++j) {
    fill(random(255), random(255), random(255));
    ellipse(d * j + d / 2, d * i + d / 2, d, d);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 8.50.51.png (164.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/f824248f-5f2d-4887-a9f5-d609fb09356b.png)

色の生成には成功していますが、どちらかというとくすんだ色の方が目立ちます。
また、色に統一感がないように見えます。
これを解決するために、**HSB 色空間** を使用することが考えられます。

Processing では、`fill` や `stroke` で設定する色はデフォルトで **RGB 色空間** が用いられます。
RGB 色空間では、一つの色を 3 次元のベクトル、すなわち赤色、緑色、青色の 3 つの成分で表します。

以下は横軸に赤色、縦軸に緑色成分をとり、青色成分を時間変化させて描画するプログラムです。

```java
int z = 0;

void setup() {
  size(256, 256);
}

void draw() {
  for (int y = 0; y < height; ++y) {
    for (int x = 0; x < width; ++x) {
      stroke(x, y, z);
      point(x, y);
    }
  }
  z = (z + 16) % 256;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.36.34.png (4.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/028d1449-646a-41c9-9496-74f836d426a1.png)

HSB 色空間では、RGB と同様に一つの色を 3 次元のベクトルで表しますが、それぞれの成分は色相（Hue）、彩度（Saturation）、明度（Brightness）を表します。

Processing では、 `colorMode` 関数を使用することで、HSB 色空間を使用することができます。
`colorMode` 関数は`colorMode(mode)` または `colorMode(mode, max1, max2, max3)` のように呼び出します。
`mode` には定数 `RGB` または `HSB` を渡すことができます。
`max1`、`max2`、`max3` は色の 3 成分の最大値を設定します。

RGB と同様に、彩度を横軸、明度を縦軸、色相を時間軸にとって描画するプログラムを以下に提示します。

```java
int z = 0;

void setup() {
  size(256, 256);
  colorMode(HSB);
}

void draw() {
  for (int y = 0; y < height; ++y) {
    for (int x = 0; x < width; ++x) {
      stroke(z, x, y);
      point(x, y);
    }
  }
  z = (z + 256) % 256;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 8.47.56.png (34.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/96c47d88-e0be-421a-ac75-64b7348e8cfb.png)

HSB 色空間における色彩は、円柱の断面の円の角度として表されます。
以下は色相の変化を円上に可視化したプログラムです。

```java
size(400, 400);
background(255);
colorMode(HSB, 360, 100, 100);
int x0 = width / 2;
int y0 = height / 2;
int r = width / 2;
for (float t = 0; t < TWO_PI; t += 0.001) {
  float h = degrees(t);
  stroke(h, 100, 100);
  line(x0, y0, x0 + r * cos(t), y0 + r * sin(t));
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.44.58.png (110.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/ae48d742-85ea-4b3e-bb5a-004f85c304f1.png)

HSB 色空間において、彩度と明度を固定したまま色相をランダムに決めることで生成される色に統一感を持たせることができます。

```java
size(600, 600);
background(255);
colorMode(HSB, 360, 100, 100);
int n = 10;
int d = width / n;
for (int i = 0; i < n; ++i) {
  for (int j = 0; j < n; ++j) {
    fill(random(360), 100, 100);
    ellipse(d * j + d / 2, d * i + d / 2, d, d);
  }
}
```

![Screen Shot 2019-07-08 at 8.51.39.png (146.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/eef0f36a-5b4a-4773-9f63-e8478b0b713f.png)

HSB は連続的な値の大小表現にも使用することができます。

# Verlet 法プログラムの整理

Verlet 法のプログラムは以下の部分から構成されています。

1. オブジェクトの描画
1. 座標の更新
1. 速度の更新

速度の更新は、さらにオブジェクトにかかる力をそれぞれ個別に適用することで処理を分割できます。
より複雑なシミュレーションを行うため、プログラムを関数化して整理します。

以下のプログラムは、重力、壁の反射、空気抵抗を考慮した球体の運動を関数化して整理したプログラムです。
`drawObjects` 関数の引数で軌跡の表示の有無を切り替えられるようにしています。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
boolean boundX = false;
boolean boundY = false;

void setup() {
  size(600, 600);
  background(255);
  x = width / 2;
  y = height - r;
  vx = 30;
  vy = -30;
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applyReflection(0.99);
  applyGravityForce(1);
  applyResistanceForce(0.01);
}

void applyReflection(float restitution) {
  if (x - r < 0 || width < x + r) {
    if (!boundX) {
      vx *= -restitution;
      boundX = true;
    }
  } else {
    boundX = false;
  }
  if (y + r > height) {
    if (!boundY) {
      vy *= -restitution;
      boundY = true;
    }
  } else {
    boundY = false;
  }
}

void applyGravityForce(float g) {
  if (!boundY) {
    vy += g;
  }
}

void applyResistanceForce(float k) {
  vx += -k * vx;
  vy += -k * vy;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.09.15.png (120.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/346ac6fa-e08f-47f7-be7b-ad199a20622f.png)

以降はこのプログラムをベースにしてシミュレーションを行います。

## 演習 1

上記のプログラムを改変し、3 つの球体が同時に運動するプログラムを作成しましょう。
色相の異なる 3 つの色をそれぞれの球体に割り当てるようにしましょう。

# バネの運動

バネによってかかる力は、バネの自然長からの伸びに比例します。
これを **フックの法則** と呼びます。
バネの現在の長さを $$d$$ 、自然長を $$L$$ 、バネ定数を $$k$$ としたときのフックの法則に従う力は以下のように表されます。

$$
F_s = k (L - d)
$$

片側が固定され、もう片側におもりがついたバネの運動の Verlet 法によるシミュレーションのプログラムは以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
float x0;
float y0;

void setup() {
  size(600, 600);
  background(255);
  x = width / 4;
  y = height / 4;
  vx = 0;
  vy = 0;
  x0 = 0;
  y0 = 0;
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  stroke(0);
  line(x0, y0, x, y);
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applySpringForce(x0, y0, 0.01, 500);
}

void applySpringForce(float x0, float y0, float k, float L) {
  float d = dist(x0, y0, x, y);
  float theta = atan2(y - y0, x - x0);
  float w = k * (L - d);
  vx += w * cos(theta);
  vy += w * sin(theta);
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.50.32.png (28.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/01e3539b-ecf7-4435-bb2f-ab34a0df5345.png)

上のプログラムに重力の影響を加えると以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
float x0;
float y0;

void setup() {
  size(600, 600);
  background(255);
  x = width / 2;
  y = height / 4;
  vx = 0;
  vy = 0;
  x0 = width / 2;
  y0 = 0;
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  stroke(0);
  line(x0, y0, x, y);
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applySpringForce(x0, y0, 0.01, 300);
  applyGravityForce(1);
}

void applySpringForce(float x0, float y0, float k, float L) {
  float d = dist(x0, y0, x, y);
  float theta = atan2(y - y0, x - x0);
  float w = k * (L - d);
  vx += w * cos(theta);
  vy += w * sin(theta);
}

void applyGravityForce(float g) {
  vy += g;
}
```

![Screen Shot 2019-07-08 at 6.53.32.png (32.2 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/684646d3-9534-4e7b-b1c5-ecd2098986ce.png)

また、空気抵抗力を考慮すると以下のようになります。

```java
float x;
float y;
float vx;
float vy;
float r = 25;
float x0;
float y0;

void setup() {
  size(600, 600);
  background(255);
  x = width / 2;
  y = height / 4;
  vx = 0;
  vy = 0;
  x0 = width / 2;
  y0 = 0;
}

void draw() {
  drawObjects(false);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  stroke(0);
  line(x0, y0, x, y);
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applySpringForce(x0, y0, 0.01, 300);
  applyResistanceForce(0.01);
}

void applySpringForce(float x0, float y0, float k, float L) {
  float d = dist(x0, y0, x, y);
  float theta = atan2(y - y0, x - x0);
  float w = k * (L - d);
  vx += w * cos(theta);
  vy += w * sin(theta);
}

void applyResistanceForce(float k) {
  vx += -k * vx;
  vy += -k * vy;
}
```

![Screen Shot 2019-07-08 at 11.44.17.png (26.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/0ab7b47f-da71-4a48-9165-01643c519580.png)

空気抵抗を考慮すると、バネの伸びが自然長に近づくように収束していきます。

# 複数のバネの運動

一つのおもりに複数のバネがつながっている場合でも、それぞれのバネからかかる力を合成することで運動のシミュレーションを行うことができます。
以下は、画面の左上、左下、右下、右上の 4 点に片側が固定されたバネがあり、もう一方につながったおもりを (100, 150) の点で静かに離したときの運動のシミュレーションです。

```java
float x;
float y;
float vx;
float vy;
float r = 25;

int n = 4;
float[] springX = new float[n];
float[] springY = new float[n];

void setup() {
  size(600, 600);
  background(255);
  x = 100;
  y = 150;
  vx = 0;
  vy = 0;

  springX[0] = 0;
  springX[1] = 0;
  springX[2] = width;
  springX[3] = width;
  springY[0] = 0;
  springY[1] = height;
  springY[2] = height;
  springY[3] = 0;
}

void draw() {
  drawObjects(false);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  for (int i = 0; i < n; ++i) {
    stroke(0, alpha);
    line(springX[i], springY[i], x, y);
  }
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applySpringForce(0.005, 300);
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < n; ++i) {
    float d = dist(x, y, springX[i], springY[i]);
    float theta = atan2(y - springY[i], x - springX[i]);
    float w = k * (L - d);
    vx += w * cos(theta);
    vy += w * sin(theta);
  }
}
```

![Screen Shot 2019-07-08 at 7.34.37.png (226.6 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/7a8792de-a5c6-4a9b-af8d-9e42f7628e1d.png)

## 演習 2

1 つのおもりに 2 本のバネがつながっていて、バネのもう片側がそれぞれ (0, 0) と (600, 300) に固定されているとします。
おもりはバネの力と重力、空気抵抗力の影響を受けます。
ここで重力加速度を 1、バネの自然長を 300、バネ定数を 0.005、空気抵抗係数を 0.01 とします。
おもりを (100, 150) で静かに離したときの運動の様子を幅 600、高さ 600 の画面に描画するプログラムを作成しましょう。
また、おもりが静止する点の座標を求めてみましょう。

# 両端におもりをつけたバネの運動

バネの片側を固定せず、移動可能なおもりをバネの両端につけた場合、それぞれのおもりに力が働きます。
一方のおもりにかかる力と同じ大きさの力が逆方向にもう一方のおもりへかかります。

```java
int n = 2;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

void setup() {
  size(600, 600);
  background(255);
  x[0] = 100;
  y[0] = height / 2;
  x[1] = width - 100;
  y[1] = height / 2;
}

void draw() {
  drawObjects(false);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  stroke(0);
  line(x[0], y[0], x[1], y[1]);
  noStroke();
  fill(0, alpha);
  for (int i = 0; i < n; ++i) {
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 200);
  applyResistanceForce(0.01);
}

void applySpringForce(float k, float L) {
  float d = dist(x[0], y[0], x[1], y[1]);
  float theta = atan2(y[1] - y[0], x[1] - x[0]);
  float w = k * (L - d);
  vx[0] -= w * cos(theta);
  vy[0] -= w * sin(theta);
  vx[1] += w * cos(theta);
  vy[1] += w * sin(theta);
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}
```

![Screen Shot 2019-07-08 at 11.52.27.png (29.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/ccc4de76-9591-4fcb-a6ab-e642ee224a1e.png)

# グラフ描画

グラフ理論のグラフはノードの集合とリンクの集合によって表されます。
グラフに含まれてるノード同士がリンクによって接続されています。

グラフはそのようなつながりの関係のみを持ちますが、グラフがどのような構造を持っているかを視覚的に表現したい場合には、2 次元や 3 次元空間でノードとリンクを描画する必要があります。
直接的に、画面にノードを描画し、ノード間につながるリンクを線でつなぐような表現を **ノードリンク図** と呼びます。
ノードリンク図を描くためには、ノードをどこに配置するかどうかを決定する必要があります。
このような、ノード配置を行う方法を **グラフ描画** （Graph Drawing）と呼びます。
グラフのどのような特徴を強調するかに応じて、様々なグラフ描画手法が考えられています。
グラフ構造の可視化は、世の中に存在する様々なネットワーク構造を持ったデータの理解に役立てることができます。

グラフ描画の一つのアプローチとして、ノード間に働く力をシミュレーションすることでノードの座標を決定する方法があります。
このようなアプローチは **Force-directed アルゴリズム** と呼ばれます。
Force-directed アルゴリズムのもっとも基本的な方法の一つは、ノード間をつなぐリンクをバネと見立ててその運動をシミュレーションすることです。
このような方法は **バネモデル** とも呼ばれます。
バネモデルを用いたグラフ描画としては Eades の方法が有名です。
しかし、本講義では Verlet 法により容易に実装できるようにバネモデルをアレンジしています。

バネモデルのシミュレーションの前に、グラフのプログラム上での表現を考えましょう。
ノードについては、ノードの個数分の座標と速度を配列として保持しておく必要があります。
これは複数の物体の運動と同様になります。
リンクについては、どのノードとどのノードがつながっているか、一方のノードの添字 `source` ともう一方のノードの添字 `target` をそれぞれ整数型の配列で表すことができます。

次にバネモデルのシミュレーションについて考えます。
まずはじめに、ノードの初期座標を適当に与えておく必要があります。
これは`random`関数を用いてランダムに決めることが可能です。
次に、全てのリンクに対してフックの法則に従う力の計算を行いますが、それだけでは座標がうまく収束しません。
そのため、空気抵抗の影響を与えることでノードの座標が静止状態になるまで繰り返しを行います。
また、いずれのノードも固定されていないため、ノードの位置が表示可能な画面の領域から遠く離れていく場合も考えられます。
これを回避するために、速度の更新の際に、全てのノードの重心が画面中央にくるように全ての座標の平行移動を行います。

完全グラフ $$K_3$$ のバネモデルによって可視化するプログラムは以下のようになります。

```java
int n = 3;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

int m = 3;
int[] source = {0, 0, 1};
int[] target = {1, 2, 2};

void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 200);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = dist(x[s], y[s], x[t], y[t]);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}
```

実行結果は以下の通りです。

![Screen Shot 2019-07-08 at 12.25.58.png (21.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/b8e071e9-a34f-43ef-a067-d93e21a24cc2.png)

リンクを以下のように変更してみます。

```java
int m = 7;
int[] source = {0, 0, 1, 1, 2, 3, 3};
int[] target = {1, 4, 2, 4, 3, 4, 5};
```

うまくいけば以下のような配置になります。

![Screen Shot 2019-07-08 at 12.30.16.png (32.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/72b9b25f-a4d5-450f-b8b8-04950d056864.png)

一方で、ノードの初期位置によっては以下のように重なりが生じたりします。

![Screen Shot 2019-07-08 at 12.31.49.png (26.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/7c48044f-b676-4683-933d-1d6c58d35e61.png)

## 演習 3

10 頂点の完全グラフ $$K_{10}$$ をバネモデルによって可視化しましょう。
完全グラフ $$K_n$$ とは、全ての頂点の組の間に辺が存在するグラフです。

# まとめ

Verlet 法によるバネの運動のシミュレーションと、それを用いたネットワーク構造（グラフ）の可視化を行いました。
しかし、バネモデルだけではノードの重なりが生じたり、視覚的に優れた配置にならない場合も生じます。
次回からはこれらの解消について考えていきます。

# 演習問題の解答例

## 演習 1

```java
int n = 3;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;
boolean[] boundX = new boolean[n];
boolean[] boundY = new boolean[n];
color[] colors = new color[n];

void setup() {
  size(600, 600);
  background(255);
  colorMode(HSB, 360, 100, 100);
  for (int i = 0; i < n; ++i) {
    x[i] = random(r, width - r);
    y[i] = random(r, height - r);
    vx[i] = random(-30, 30);
    vy[i] = random(-10, 0);
    boundX[i] = false;
    boundY[i] = false;
    colors[i] = color(random(360), 100, 100);
  }
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    colorMode(RGB, 255);
    background(255);
    alpha = 255;
  } else {
    alpha = 50;
  }
  noStroke();
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    fill(colors[i], alpha);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applyReflection(0.99);
  applyGravityForce(1);
  applyResistanceForce(0.01);
}

void applyReflection(float restitution) {
  for (int i = 0; i < n; ++i) {
    if (x[i] - r < 0 || width < x[i] + r) {
      if (!boundX[i]) {
        vx[i] *= -restitution;
        boundX[i] = true;
      }
    } else {
      boundX[i] = false;
    }
    if (y[i] + r > height) {
      if (!boundY[i]) {
        vy[i] *= -restitution;
        boundY[i] = true;
      }
    } else {
      boundY[i] = false;
    }
  }
}

void applyGravityForce(float g) {
  for (int i = 0; i < n; ++i) {
    if (!boundY[i]) {
      vy[i] += g;
    }
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}
```

実行結果

![Screen Shot 2019-07-08 at 6.23.22.png (205.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/eb6c8ce0-b7c2-4c9d-a03b-fcebb02b06e0.png)

## 演習 2

```java
float x;
float y;
float vx;
float vy;
float r = 25;

int n = 2;
float[] springX = new float[n];
float[] springY = new float[n];

void setup() {
  size(600, 600);
  background(255);
  x = 100;
  y = 150;
  vx = 0;
  vy = 0;

  springX[0] = 0;
  springX[1] = width;
  springY[0] = 0;
  springY[1] = height / 2;
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 10;
  }
  for (int i = 0; i < n; ++i) {
    stroke(0, alpha);
    line(springX[i], springY[i], x, y);
  }
  noStroke();
  fill(0, alpha);
  ellipse(x, y, 2 * r, 2 * r);
}

void updatePosition() {
  x += vx;
  y += vy;
}

void updateVelocity() {
  applySpringForce(0.005, 300);
  applyGravityForce(1);
  applyResistanceForce(0.01);
  println(x, y);
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < n; ++i) {
    float d = dist(x, y, springX[i], springY[i]);
    float theta = atan2(y - springY[i], x - springX[i]);
    float w = k * (L - d);
    vx += w * cos(theta);
    vy += w * sin(theta);
  }
}

void applyGravityForce(float g) {
  vy += g;
}

void applyResistanceForce(float k) {
  vx += -k * vx;
  vy+= -k * vy;
}
```

実行結果

![Screen Shot 2019-07-08 at 8.15.57.png (167.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/f53676b1-7f27-4f02-aacf-e33788baaf41.png)

## 演習 3

```java
int n = 10;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

int m = n * (n - 1);
int[] source = new int[m];
int[] target = new int[m];

void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
  int k = 0;
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      source[k] = i;
      target[k] = j;
      k += 1;
    }
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 200);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = dist(x[s], y[s], x[t], y[t]);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}
```

実行結果

![Screen Shot 2019-07-08 at 8.20.53.png (56.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/25658b0b-9266-4b3f-84d2-63a05daf320b.png)

# クーロン力

荷電粒子（電荷を帯びた粒子）には **クーロンの法則** に従う **クーロン力** が働きます。
2 つの荷電粒子間に働くクーロン力は以下の式で表されます。

$$
F = k \frac{q_1 q_2}{r^2}
$$

ここで $$k$$ は比例定数、$$q_1$$ と $$q_2$$ はそれぞれの粒子の電荷、$$r$$ は 2 粒子間の距離とします。
$$q_1$$ と $$q_2$$ が同じ符号であれば 2 粒子は反発し合い、異なる符号であれば 2 粒子は引き合います。

空間に複数の荷電粒子が存在するとしましょう。
$$i$$ 番目の粒子は、他の全ての粒子からクーロン力の影響を受けます。
そのため速度 Verlet 法では、二重ループによって全粒子ペアの影響を計算する必要があります。

以下は 5 つの荷電粒子の運動のシミュレーションを行うプログラムです。
0 番目の粒子は-5 の電荷を、それ以外は+1 の電荷を持っています。

```java
int n = 5;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float[] q = new float[n];
float r = 25;

float minDistance = 10;

void setup() {
  size(600, 600);
  background(255);

  x[0] = width / 2;
  y[0] = height / 2;
  q[0] = -5;

  for (int i = 1; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
    q[i] = 1;
  }
}

void draw() {
  drawObjects(true);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 10;
  }
  noStroke();
  fill(0, alpha);
  for (int i = 0; i < n; ++i) {
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applyRepulsiveForce(10);
}

void applyRepulsiveForce(float k) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -k * q[i] * q[j] / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 4.16.11.png (136.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/53b78183-9b1f-4047-9300-984af16e5e5c.png)

正の電荷と負の電荷はお互いに引き合い、正の電荷同士は反発し合いながら運動します。

# ネットワーク可視化へのクーロン力の導入

前回、バネの力だけではネットワークのノードに重なりが生じて、ノード配置がうまくいかない場合がありました。
クーロン力を用いてノード間の反発力を計算することでノードの重なりを回避してみましょう。
ノードが全て同じ符号の電荷を持っているとします。
i 番目のノードには、j 番目のノードから、電荷に比例して距離の 2 乗に反比例する加速度が働きます。

```java
int n = 6;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

int m = 7;
int[] source = {0, 0, 1, 1, 2, 3, 3};
int[] target = {1, 4, 2, 4, 3, 4, 5};

float minDistance = 1;

void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 10);
  applyRepulsiveForce(10);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 4.56.34.png (28.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/b50be7ec-9ba2-4e4f-a8a4-824684c4725d.png)

ノード間の反発力を導入することでノードを均等に配置できるようになりました。

ネットワーク可視化において、ノード間の反発力が最初に導入されたのは **Fruchterman-Reingold のアルゴリズム** です。

# グラフの生成

ランダムに生成したグラフを Force-directed アルゴリズムで可視化してみます。
以下のプログラムでは、10 頂点のグラフにおいて、全ての頂点ペアが 0.5 の確率でリンクを持つようにランダムにグラフを生成しています。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

void setup() {
  size(600, 600);
  initializeGraph(10);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int k) {
  n = k;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.5) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 10);
  applyRepulsiveForce(10);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

閉路を持たないグラフを木と呼びます。
以下は 100 ノードの木をランダムに生成し、Force-directed アルゴリズムによって可視化するプログラムです。

```java
int n, m;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] source;
int[] target;

float r = 5;
float minDistance = 10;

void setup() {
  size(600, 600);
  initializeGraph(100);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int k) {
  n = k;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = n - 1;
  source = new int[m];
  target = new int[m];
  for (int i = 0; i < m; ++i) {
    source[i] = i;
    target[i] = int(random(i + 1, n));
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.1, 10);
  applyRepulsiveForce(1);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 12.43.44.png (38.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/de456f4f-2642-44e6-a7bd-c289f049d19c.png)

ここで、以下の 2 つの問題に気づきます。

- ノード数が多いとノード座標が収束しにくい
- グラフが非連結の場合に、それぞれの連結成分が離れていく

次にこれらの解決方法を考えていきます。

## 演習 1

グラフ $$G$$ において全てのノードが少なくても $$n$$ 本の辺を持つとき、$$G$$ を $$n$$連結グラフと呼びます。
2 連結グラフをランダムに生成し、Force-directed アルゴリズムを用いて可視化しましょう。

各ノードにつき、$$n$$ 個の自分以外のノードを重複なく選び、リンクで結ぶこと$$n$$連結グラフをつくることができます。

# アニーリング（焼きなまし）

空気抵抗力を受けることでノードの座標は収束していきます。
そのため、空気抵抗係数を大きくすればノードは速く停止します。
しかし、最初から空気抵抗係数が大きければノードが綺麗に配置される前に停止してしまいます。
そこで、最初は空気抵抗係数を小さくして、徐々に大きくしていく方法をとります。
このような方法を **アニーリング** と呼びます。

空気抵抗係数を徐々に大きくしていくには様々な方法が考えられます。
ここでは、係数 $$0 \leq \alpha \leq 1$$ に対して空気抵抗係数を $$k = 1 - \alpha$$ とします。
そして、$$\alpha = 1$$ からはじまり、 $$\alpha \leftarrow e \alpha \quad (0 < e < 1)$$ と更新していくことで空気抵抗係数を大きくしていきます。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(20);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.3) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(50);
  applyResistanceForce(1 - alpha);
  applyCentering();
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

# ノードの中心化

以下のように画面の中心に引き寄せる力をかけることで、非連結なグラフにおいても画面内に収めることができます。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(40);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.01) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(0);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(200);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

![Screen Shot 2019-07-15 at 12.47.57.png (58.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/02010e08-2410-4664-8dc4-08aa908f89d1.png)

## 演習 2

アニーリングとノードの中心化を取り入れて非連結なツリーの可視化を行いましょう。
連結なツリーからランダムにリンクを削除することで非連結なツリーを生成できます。

# 視覚表現の工夫

ネットワークのグラフやノードの持つ情報を、視覚的表現と対応づけることで、それらの情報の可視化が可能です。

以下はノードの次数を計算し、次数が低いノードは青色、高いノードは赤色でその間を連続的に色相を変化させています。
色相を 0 から 360 の範囲で表すとき、青色の色相は 240、赤色の色相は 0、その中間の緑色の色相は 120 です。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] degree;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;
int maxDegree;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(40);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  degree = new int[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.05) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
  for (int i = 0; i < m; ++i) {
    degree[source[i]] += 1;
    degree[target[i]] += 1;
  }
  for (int i = 0; i < n; ++i) {
    if (degree[i] > maxDegree) {
      maxDegree = degree[i];
    }
  }
}

void drawObjects() {
  colorMode(RGB, 255, 255, 255);
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    fill((1 - float(degree[i]) / maxDegree) * 240, 100, 100);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  colorMode(RGB, 255, 255, 255);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(200);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 7.16.03.png (118.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/77b0c342-2d37-480e-8265-42f24f1665f2.png)

## 演習 2

ノード数 40、リンク密度 0.05 のランダムグラフにおいて、最初に全てのノードについて `values[i]` を 0 以上 100 未満の範囲でランダムに割り当てます。
Force-directed アルゴリズムを用いて生成したランダムグラフを可視化しましょう。
その際にノードの色は色相は青色（240）で、彩度（HSB の 2 つ目の値）は `values[i]` の大きさに応じて割り当てましょう。
また、ノードの直径はノードの次数に比例するようにし、最も小さい次数のノードの直径は 40、最も大きい次数のノードの直径は 80 となるようにしましょう。

# まとめ

Force-directed アルゴリズムを用いたネットワークの可視化において、バネの力に加えてノード間の反発力を加えることでより視認性の高いノード配置ができることを確認しました。
しかし、全てのノードの組で反発力を計算する場合の計算量は $$O(n^2)$$ となり、ノード数が多い場合には膨大な計算が必要となります。
次回はこれらの計算量を削減する工夫について学習します。

また、非連結グラフの扱いや、ノードやリンクの持つ情報を視覚的に表現する方法について学習しました。
これらのテクニックを総合的に用いることで効果的なネットワーク可視化を実現する必要があります。

# 演習問題の解答例

## 演習 3

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] degree;
float[] values;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;
int maxDegree;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(40);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  degree = new int[n];
  values = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.05) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
  for (int i = 0; i < m; ++i) {
    degree[source[i]] += 1;
    degree[target[i]] += 1;
  }
  for (int i = 0; i < n; ++i) {
    if (degree[i] > maxDegree) {
      maxDegree = degree[i];
    }
    values[i] = random(0, 100);
  }
}

void drawObjects() {
  colorMode(RGB, 255, 255, 255);
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    fill(240, values[i], 100);
    float d = 40.0 * degree[i] / maxDegree + 40;
    ellipse(x[i], y[i], d, d);
    colorMode(RGB, 255, 255, 255);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(200);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果の例

![Screen Shot 2019-07-15 at 12.34.12.png (132.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/851d3688-72aa-43c7-b33e-bd4ae349ad01.png)

# 多体計算の近似

粒子間の重力やクーロン力の計算には、全ての粒子のペアでそれらの間に働く力を計算する必要がありました。
そのため、粒子の数を $$n$$ とすると、その計算量は $$O(n^2)$$ となります。
計算量を減らすための近似について考えてみましょう。

具体的にクーロン力で考えます。
$$i$$ 番目の粒子が他の粒子群 $$P$$ から受けるクーロン力は以下のようになります。

$$
F = k q_i \sum_{P}^j \frac{q_j}{(x_i - x_j)^2 + (y_i - y_j)^2}
$$

他の粒子が $$i$$ 番目の粒子から離れたところにかたまって位置している場合を考えます。
このとき、他の粒子の塊をそれらの重心 $$(\bar{x}, \bar{y})$$ に位置し、電荷が粒子の総和 $$Q$$ を持つような一つの粒子と見做すことができるでしょう。
その重心の粒子から受けるクーロン力は以下のように表されます。

$$
F = k q_i \frac{Q}{(x_i - \bar{x})^2 + (y_i - \bar{y})^2}
$$

すなわち、ある粒子から遠く離れた粒子の塊を効率的に見つけることができれば、粒子間の重力やクーロン力をより少ない計算量で近似できます。
次は、それを実現するためのデータ構造について考えてみます。

# Barnes-Hut 木

それぞれのノードが高々 4 つの子を持つ根付き木を **4 分木** と呼びます。
ある長方形領域を左上、左下、右上、右下の 4 つに再帰的に分割し、4 分木に割り当てることを考えます。
さらに、1 つの粒子と 4 分木の葉を対応づけます。
すなわち、全ての粒子が 4 分木の葉に割り当てられるように、粒子の存在する領域を再帰的に分割していきます。
（ただし全く同じ座標に複数の粒子が存在する場合は、葉が複数の粒子を持つことを許します。）
このような 4 分木を **Barnes-Hut 木** と呼びます。
Barnes-Hut 木の葉でないノードにおいて、その子孫に含まれる粒子の重心を格納しておくことで、ある領域に存在する粒子の重心を効率よく求めることができます。

Barnes-Hut 木を構築するアルゴリズムを考えます。
既にある Barnes-Hut 木に新たに粒子 $$p$$ を追加することを考えてみましょう。
Barnes-Hut 木の根が葉である場合とそうでない場合を分けて考えます。
根が葉でない場合、粒子 $$p$$ の座標が根の領域の左上、左下、右上、右下のどこに位置するかを判断し、その部分領域に対応する部分木に粒子 $$p$$ を追加します。
根が葉の場合、根が既に持っている粒子 $$q$$ と新たな粒子 $$p$$ を、それぞれが位置する領域に対応する子ノードを作成して追加します。
以上の手順を部分木に対して再帰的に行うことで、Barnes-Hut 木に粒子 $$p$$ を追加することができます。
そのため、1 粒子のみを持った Barnes-Hut 木から始まり、全ての粒子を順番に木に追加していくことで全ての粒子を含んだ Barnes-Hut 木を構築することができます。

粒子数を $$n$$ とし、空間中に偏りなく粒子が存在している場合は、すなわち、Barnes-Hut 木が完全 4 分木になっている場合を考えます。
このとき Barnes-Hut 木からある座標に対応する葉を見つけるための計算量は $$O(\log n)$$ となります。
すなわち、全ての粒子を含んだ Barnes-Hut 木を構築するのに必要な最良時の計算量は $$O(n \log n)$$ となります。

以下は、画面中にランダムに生成した粒子を格納した Barnes-Hut 木を構築し、その領域を可視化するプログラムです。
再帰的なデータ構造を表すためにクラスを使用しています。

```java
class Point {
  float x;
  float y;

  Point (float x, float y) {
    this.x = x;
    this.y = y;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

Point[] points = new Point[100];
QuadTree root;

void setup() {
  size(600, 600);
  root = new QuadTree(0, 0, width - 1, height - 1);
  for (int i = 0; i < points.length; ++i) {
    println(i);
    points[i] = new Point(random(width), random(height));
    root.addPoint(points[i]);
  }
  for (int i = 0; i < points.length; ++i) {
    root.addPoint(points[i]);
  }
}

void draw() {
  background(255);
  drawTree(root);
  for (int i = 0; i < points.length; ++i) {
    fill(0);
    noStroke();
    ellipse(points[i].x, points[i].y, 5, 5);
  }
}

void drawTree(QuadTree tree) {
  if (tree.isLeaf()) {
    fill(200, 200, 200);
  } else {
    noFill();
  }
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  for (QuadTree child : tree.children()) {
    drawTree(child);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-22 at 9.29.48.png (26.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/c9f8510b-040c-4744-96f3-01d21d361e80.png)

葉の領域は灰色で塗りつぶしています。
葉と一つの粒子が対応していることが確認できます。

# Barnes-Hut アルゴリズム

次に、Barnes-Hut 木を用いて粒子間にはたらく力を粒子群の重心によって近似できる条件を考えてみましょう。
粒子 $$p$$ と粒子群の重心 $$gc$$ は十分離れている必要があります。
閾値を $$\lambda$$ 、 $$p$$ と $$gc$$ の距離を $$d$$、$$gc$$ を持つ部分木の面積を $$A$$ とします。
ここでは近似の条件を、以下の式で表します。

$$
\lambda^2  d^2 > A
$$

これは、部分木の幅と高さの幾何平均 $$\sqrt{A}$$ と $$d$$ との比が閾値 $$\lambda$$ を超えたときを意味しています。
Barnes-Hut 木を用いて多体問題の計算を近似する方法を **Barnes-Hut アルゴリズム** と呼びます。

以下は (100, 100) に存在する粒子にかかる力の計算の様子を可視化するプログラムです。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

Point[] points = new Point[100];
QuadTree root;
float threshold = 0.81;

void setup() {
  size(600, 600);
  root = new QuadTree(0, 0, width - 1, height - 1);
  for (int i = 0; i < points.length; ++i) {
    points[i] = new Point(random(width), random(height), 1);
    root.addPoint(points[i]);
  }
  for (int i = 0; i < points.length; ++i) {
    root.addPoint(points[i]);
  }
}

void draw() {
  background(255);
  Point p = new Point(100, 100, 1);
  drawGravityCenter(p, root);
  for (int i = 0; i < points.length; ++i) {
    fill(0);
    noStroke();
    ellipse(points[i].x, points[i].y, 5, 5);
  }
  fill(255, 0, 0);
  ellipse(p.x, p.y, 10, 10);
}

void drawGravityCenter(Point p, QuadTree tree) {
  noFill();
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  Point gc = tree.gravityCenter();
  float d = dist(p.x, p.y, gc.x, gc.y);
  if (tree.isLeaf() || threshold * d * d > tree.boxWidth * tree.boxHeight) {
    fill(0, 0, 255);
    noStroke();
    ellipse(gc.x, gc.y, 10, 10);
    stroke(0, 0, 255);
    line(p.x, p.y, gc.x, gc.y);
  } else {
    for (QuadTree child : tree.children()) {
      drawGravityCenter(p, child);
    }
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-22 at 10.11.38.png (43.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/242ced83-e266-42f7-8fa1-0e9ac905c389.png)

(100, 100) から遠いところでは、大きな領域の部分木の重心で力の計算が近似できることを確認できます。

それでは、速度 Verlet 法に Barnes-Hut アルゴリズムを取り入れて計算をしてみましょう。
以下のプログラムは赤色で表した負電荷と青色で表した正電荷の運動のシミュレーションです。
負電荷にかかるクーロン力を上記のプログラムと同様に可視化しています。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

int n = 100;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float[] q = new float[n];
float r = 15;

float minDistance = 10;

void setup() {
  size(600, 600);
  background(255);

  x[0] = width / 2;
  y[0] = height / 2;
  q[0] = -5;

  for (int i = 1; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
    q[i] = 0.1;
  }
}

void draw() {
  drawObjects(false);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 10;
  }
  noStroke();
  for (int i = 0; i < n; ++i) {
    if (q[i] < 0) {
      fill(255, 0, 0, alpha);
    } else {
      fill(0, 0, 255, alpha);
    }
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applyRepulsiveForce(1);
}

void applyRepulsiveForce(float k, QuadTree tree, int i) {
  Point gc = tree.gravityCenter();
  float d = dist(x[i], y[i], gc.x, gc.y);
  if (d < minDistance) {
    return;
  }
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    float w = -k * q[i] * gc.value / (d * d);
    vx[i] += (gc.x - x[i]) * w;
    vy[i] += (gc.y - y[i]) * w;
  } else {
    for (QuadTree child : tree.children()) {
      applyRepulsiveForce(k, child, i);
    }
  }
}

void applyRepulsiveForce(float k) {
  float left = x[0];
  float right = x[0];
  float top = y[0];
  float bottom = y[0];
  for (int i = 1; i < n; ++i) {
    if (x[i] < left) {
      left = x[i];
    }
    if (x[i] > right) {
      right = x[i];
    }
    if (y[i] < top) {
      top = y[i];
    }
    if (y[i] > bottom) {
      bottom = y[i];
    }
  }
  float size = right - left > bottom - top ? right - left : bottom - top;
  QuadTree root = new QuadTree(left, top, size, size);
  for (int i = 0; i < n; ++i) {
    root.addPoint(new Point(x[i], y[i], q[i]));
  }
  for (int i = 0; i < n; ++i) {
    applyRepulsiveForce(k, root, i);
  }
  drawTree(root);
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}

void drawTree(QuadTree tree) {
  noFill();
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  Point gc = tree.gravityCenter();
  float d = dist(x[0], y[0], gc.x, gc.y);
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    fill(0, 0, 255);
    noStroke();
    ellipse(gc.x, gc.y, 10, 10);
    stroke(0, 0, 255);
    line(x[0], y[0], gc.x, gc.y);
  } else {
    for (QuadTree child : tree.children()) {
      drawTree(child);
    }
  }
}
```

![Screen Shot 2019-07-22 at 10.48.39.png (86.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/d080418c-ca85-4a4f-982b-f19c7ba04db9.png)

# ネットワーク可視化への Barnes-Hut アルゴリズムの導入

Verlet 法によるネットワーク可視化において、ノード間の反発力の計算に Barnes-Hut アルゴリズムを導入しましょう。
プログラムの全体は以下のようになります。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

int n;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] degree;

int m;
int[] source;
int[] target;

float r = 5;
float minDistance = 10;
int maxDegree;

float alpha = 1;
float decay = 0.995;
float totalTime = 0;
int count = 0;

void setup() {
  size(600, 600);
  randomSeed(0);
  initializeGraph(250);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  float start = millis();
  updateVelocity();
  float end = millis();
  totalTime += end - start;
  count += 1;
  if (count == 100) {
    println(totalTime / count);
  }
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  degree = new int[n];
  m = 0;
  source = new int[n - 1];
  target = new int[n - 1];
  for (int i = 0; i < n - 1; ++i) {
    if (random(1) < 0.9) {
      source[m] = i;
      target[m] = int(random(i + 1, n));
      m += 1;
    }
  }
  for (int i = 0; i < m; ++i) {
    degree[source[i]] += 1;
    degree[target[i]] += 1;
  }
  for (int i = 0; i < n; ++i) {
    if (degree[i] > maxDegree) {
      maxDegree = degree[i];
    }
  }
}

void drawObjects() {
  colorMode(RGB, 255, 255, 255);
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    stroke(0);
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    noStroke();
    fill((1 - float(degree[i]) / maxDegree) * 240, 100, 100);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.1, 10);
  applyRepulsiveForce(30, true);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q, QuadTree tree, int i) {
  Point gc = tree.gravityCenter();
  float d = dist(x[i], y[i], gc.x, gc.y);
  if (d < minDistance) {
    return;
  }
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    float w = -gc.value / (d * d);
    vx[i] += (gc.x - x[i]) * w;
    vy[i] += (gc.y - y[i]) * w;
  } else {
    for (QuadTree child : tree.children()) {
      applyRepulsiveForce(q, child, i);
    }
  }
}

void applyRepulsiveForce(float q, boolean bh) {
  if (!bh) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
    return;
  }
  float left = x[0];
  float right = x[0];
  float top = y[0];
  float bottom = y[0];
  for (int i = 1; i < n; ++i) {
    if (x[i] < left) {
      left = x[i];
    }
    if (x[i] > right) {
      right = x[i];
    }
    if (y[i] < top) {
      top = y[i];
    }
    if (y[i] > bottom) {
      bottom = y[i];
    }
  }
  QuadTree root = new QuadTree(left, top, right - left, bottom - top);
  for (int i = 0; i < n; ++i) {
    root.addPoint(new Point(x[i], y[i], q));
  }
  for (int i = 0; i < n; ++i) {
    applyRepulsiveForce(q, root, i);
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

以下は実行結果です。
可視化結果自体は前回と大きく変わりません。

![Screen Shot 2019-07-22 at 12.06.22.png (89.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/a630e1d2-b157-488e-a2ea-d2c4f68a3fba.png)

以下は、グラフのノード数 $$n$$ を {250, 500, 1000, 2000, 4000} としたときの計算時間の比較です。
赤色が Barnes-Hut アルゴリズム（barnes-hut）、青色がそうでない場合（all pairs）を表しています。

![chart (3).png (12.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/2e22fc02-5202-4d5b-ac30-cde2931edf7e.png)

|      | all pairs | barnes-hut |
| ---- | --------- | ---------- |
| 250  | 1.08      | 1.78       |
| 500  | 2.87      | 3.05       |
| 1000 | 10.01     | 5.59       |
| 2000 | 39.68     | 10.48      |
| 4000 | 161.47    | 23.95      |

all pairs では、ノード数が倍になったときに計算時間が約 4 倍になっています。
これは、all pairs の計算量が $$O(n^2)$$ であるためです。
一方で、barnes-hut では、ノード数が倍になっても計算時間はほぼ倍程度で収まっています。
Barnes-Hut アルゴリズムの計算量はおおよそ $$O(n \log n)$$ となっています。
ただし、Barnes-Hut 木を構築するためのオーバーヘッドがあるため、ノード数が少ないと Barnes-Hut アルゴリズムの方が遅くなる場合もあります。

# まとめ

ノード数が数千を超える大規模なネットワークの可視化においては、アルゴリズム改善による計算量の削減も重要です。
今回は、Barnes-Hut アルゴリズムを用いてノード間の反発力の計算を $$O(n \log n)$$ 程度に抑える方法を学びました。

第 11〜14 回で扱ったネットワーク可視化の方法は十分に実用的ですが、2000 年代前後までに提案されているおよそ 20 年以上前の手法です。
現在もレイアウト計算の高速化や、可視化結果の視認性向上のために様々な研究が行われています。

興味を持った人は以下の教科書の第 12 章を読むと、最先端の研究を理解する基礎が身につくでしょう。
Pre Print 版の PDF ファイルが無料でダウンロードできます。

- [Handbook of Graph Drawing and Visualization](http://cs.brown.edu/people/rtamassi/gdhandbook/)
