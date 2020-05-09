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
