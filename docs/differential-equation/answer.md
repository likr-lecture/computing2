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

## 演習 5

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

## 演習 6

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

## 演習 7

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

## 演習 8

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

## 演習 9

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

## 演習 10

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

## 演習 11

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

## 演習 12

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

## 演習 13

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
