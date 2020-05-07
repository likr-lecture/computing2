# 演習の解答例

## 演習 1

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

## 演習 2

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
