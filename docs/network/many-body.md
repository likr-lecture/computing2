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
