# 演習問題の解答例

## 演習 1

初速を落としていくと、中央の天体により近くような楕円軌道になります。

![Screen Shot 2019-07-01 at 12.47.49.png (24.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/eb09efed-e8da-4724-9e0a-98dfa5dafcc0.png)

初速を一定以上落とすと中央の天体に衝突します。

![Screen Shot 2019-07-01 at 12.48.55.png (20.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/f0db50af-4f74-4688-b537-bb9940a1fa25.png)

一方で、初速を上げすぎると、中央の天体の重力を振り切ってしまいます。

![Screen Shot 2019-07-01 at 12.49.33.png (22.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/01/28750/8255b655-1fc2-407a-8159-ddd86aa6967e.png)

## 演習 2

省略

## 演習 3

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

## 演習 4

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

## 演習 5

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

## 演習 6

## 演習 7

## 演習 8

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
