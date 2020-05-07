# 演習の解答例

## 演習 1

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

## 演習 2

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

## 演習 3

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

## 演習 4

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
