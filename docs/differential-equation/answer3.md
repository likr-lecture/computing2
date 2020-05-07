# 演習の解答例

## 演習 1

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

## 演習 2

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

## 演習 3

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
