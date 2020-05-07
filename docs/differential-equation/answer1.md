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
