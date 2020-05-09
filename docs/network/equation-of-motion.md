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
