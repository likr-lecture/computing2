# 演習の解答例

## 演習 1

```java
int count = 0;
int repeat = 10000;
for (int i = 0; i < repeat; ++i) {
  int hit = 0;
  for (int j = 0; j < 5; ++j) {
    if (random(1) < 0.5) {
      hit += 1;
    }
  }
  if (hit == 3) {
    count += 1;
  }
}

println(float(count) / repeat);
```

実行結果の例は以下のようになります。

```console
0.3145
```

正確な確率は $$\frac{1}{2^5} \binom{5}{3} = \frac{5}{16} = 0.3125$$ なので大体近い数字が得られています。

## 演習 2

```java
int aCount = 0;
int bCount = 0;
int repeat = 1000000;
for (int i = 0; i < repeat; ++i) {
  if (random(1) < 0.6) {
    if (random(1) < 0.02) {
      aCount += 1;
    }
  } else {
    if (random(1) < 0.01) {
      bCount += 1;
    }
  }
}

println(float(aCount) / (aCount + bCount) * 100);
```

実行結果の例は以下のようになります。

```console
75.09978
```

正確な確率は 75% なので、大体近い数字が得られています。

## 演習 3

```java
int count = 0;
int repeat = 100000;
for (int i = 0; i < repeat; ++i) {
  float x = random(1);
  float y = random(1);
  float z = random(1);
  float w = random(1);
  if (x * x + y * y + z * z + w * w < 1) {
    count += 1;
  }
}
println(float(count) / repeat * 16);
```

実行結果の例は以下のようになります。

```console
4.93472
```

4 次元球の体積は $$\frac{\pi^2}{2} = 4.9348022\cdots$$ なので、大体近い数字が得られています。

## 演習 4

```java
int[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new int[10000];
  for (int i = 0; i < values.length; ++i) {
    int a = int(random(1, 7));
    int b = int(random(1, 7));
    values[i] = a + b;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 2, 12);
  drawHistogram(bins);
}

int[] createBins(int[] values, int min, int max) {
  int[] bins = new int[max - min + 1];
  for (int i = 0; i < values.length; ++i) {
    bins[values[i] - min] += 1;
  }
  return bins;
}

void drawHistogram(int[] bins) {
  float binWidth = float(width) / bins.length;

  int binMax = 0;
  for (int i = 0; i < bins.length; ++i) {
    if (bins[i] > binMax) {
      binMax = bins[i];
    }
  }

  fill(0, 0, 255);
  for (int i = 0; i < bins.length; ++i) {
    int binHeight = height * bins[i] / binMax;
    rect(binWidth * i, height - binHeight, binWidth, binHeight);
  }
}
```

![Screen Shot 2019-06-10 at 0.19.23.png (13.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/2e82dad7-0aa5-4eba-9ccf-f46aba5fc1d6.png)

## 演習 5

省略

## 演習 6

```java
int seed = 1;

float rand() {
  int a = 601;
  int b = 111;
  int m = 1024;
  seed = (a * seed + b) % m;

  return float(seed) / m;
}

int n = 1000;
float[] x = new float[n];
float[] y = new float[n];

void setup() {
  size(600, 600);
  noLoop();

  for (int i = 0; i < n; ++i) {
    x[i] = rand();
    y[i] = rand();
  }
}

void draw() {
  background(255);

  noFill();
  stroke(0);
  ellipse(0, 0, 2 * width, 2 * height);

  fill(255, 0, 0);
  noStroke();
  for (int i = 0; i < n; ++i) {
    ellipse(x[i] * width, y[i] * height, 5, 5);
  }

  int count = 0;
  for (int i = 0; i < n; ++i) {
    if (dist(0, 0, x[i], y[i]) < 1) {
      count += 1;
    }
  }
  println(float(count) * 4 / n);
}
```

$$a = 601, b=111, m=1024$$ のときの実行結果は以下のようになります。

![Screen Shot 2019-06-10 at 6.10.27.png (61.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/f56f4436-2f04-4a83-84e2-912b9b36f5ec.png)

```console
3.136
```

$$a = 607, b=111, m=1024$$ のときの実行結果は以下のようになります。

![Screen Shot 2019-06-10 at 6.10.07.png (23.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/5e1e17f4-95cc-4583-8103-c9dea4cabd74.png)

```console
1.748
```

パラメーターの選び方によっては点の偏りがあり、正しくシミュレーションができていないことが確認できます。
また、点の偏りがない場合でも点が格子状に並んでいて、規則性が見えます。

## 演習 7

```java
int seed = 1;

float rand() {
  int a = 48271;
  int m = 2147483647;
  int q = m / a;
  int r = m % a;
  int hi = seed / q;
  int lo = seed % q;
  int test = a * lo - r * hi;
  if (test > 0) {
    seed = test;
  } else {
    seed = test + m;
  }
  return float(seed) / m;
}

int n = 1000;
float[] x = new float[n];
float[] y = new float[n];

void setup() {
  size(600, 600);
  noLoop();

  for (int i = 0; i < n; ++i) {
    x[i] = rand();
    y[i] = rand();
  }
}

void draw() {
  background(255);

  noFill();
  stroke(0);
  ellipse(0, 0, 2 * width, 2 * height);

  fill(255, 0, 0);
  noStroke();
  for (int i = 0; i < n; ++i) {
    ellipse(x[i] * width, y[i] * height, 5, 5);
  }

  int count = 0;
  for (int i = 0; i < n; ++i) {
    if (dist(0, 0, x[i], y[i]) < 1) {
      count += 1;
    }
  }
  println(float(count) * 4 / n);
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-10 at 6.23.22.png (83.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/ccc3e1c2-597b-4b1d-91ba-dbbfe5589d84.png)

```console
3.148
```

## 演習 8

```java
float[] values;

void setup() {
  noLoop();

  values = new float[80];
  for (int i = 0; i < values.length; ++i) {
    int a = int(5 * randomGaussian() + 60);
    int b = int(5 * randomGaussian() + 70);
    int c = int(10 * randomGaussian() + 65);
    values[i] = a + b + c;
  }
}

void draw() {
  background(255);
  float average = calculateAverage(values);
  float variance = calculateVariance(values, average);
  println(average, variance);
}

float calculateAverage(float[] values) {
  float s = 0;
  for (int i = 0; i < values.length; ++i) {
    s += values[i];
  }
  return s / values.length;
}

float calculateVariance(float[] values, float average) {
  float s = 0;
  for (int i = 0; i < values.length; ++i) {
    s += (values[i] - average) * (values[i] - average);
  }
  return s / values.length;
}
```

実行結果は以下のようになります。

```console
193.5443 148.51859
```

2 つの確率変数がそれぞれ正規分布 $$N(\mu_1, \sigma_1^2)$$ と $$N(\mu_2, \sigma_2^2)$$ に従っているとき、それらの和は正規分布 $$N(\mu_1 + \mu_2, \sigma_1^2 + \sigma_2^2)$$ に従います。
すなわち、科目 A、B、C の平均点は正規分布 $$N(195, 150)$$ に従っており、出力結果と近い値になっています。。
（小数点以下を切り捨てているので厳密には異なります。）

## 演習 9

分布関数の逆関数は以下のようになります。

$$
F^{-1}(x) = \log \frac{x}{1 - x}
$$

これに基づいてプログラムを実装します。

```java
float[] values;

void setup() {
  size(600, 600);

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = 10 * randomLogistic() + 50;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, -150, 150, 100);
  drawHistogram(bins);
}

float randomLogistic() {
  float x = random(0, 1);
  return log(x / (1 - x));
}

int[] createBins(float[] values, float min, float max, int numBins) {
  int[] bins = new int[numBins];
  for (int i = 0; i < values.length; ++i) {
    int j = int((values[i] - min) * numBins / (max - min));
    if (0 <= j && j < numBins) {
      bins[j] += 1;
    }
  }
  return bins;
}

void drawHistogram(int[] bins) {
  float binWidth = float(width) / bins.length;

  int binMax = 0;
  for (int i = 0; i < bins.length; ++i) {
    if (bins[i] > binMax) {
      binMax = bins[i];
    }
  }

  fill(0, 0, 255);
  for (int i = 0; i < bins.length; ++i) {
    int binHeight = height * bins[i] / binMax;
    rect(binWidth * i, height - binHeight, binWidth, binHeight);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 10.04.44.png (14.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/0bd568c3-e662-42f8-9cb2-fd3712a8ce3e.png)

## 演習 10

```java
float[] values;

void setup() {
  size(600, 600);

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = 10 * gaussian() + 50;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 0, 100, 50);
  drawHistogram(bins);
}

float gaussian() {
  float x = random(0, 1);
  float y = random(0, 1);
  return sqrt(-2 * log(x)) * cos(TWO_PI * y);
}

int[] createBins(float[] values, float min, float max, int numBins) {
  int[] bins = new int[numBins];
  for (int i = 0; i < values.length; ++i) {
    int j = int((values[i] - min) * numBins / (max - min));
    if (0 <= j && j < numBins) {
      bins[j] += 1;
    }
  }
  return bins;
}

void drawHistogram(int[] bins) {
  float binWidth = float(width) / bins.length;

  int binMax = 0;
  for (int i = 0; i < bins.length; ++i) {
    if (bins[i] > binMax) {
      binMax = bins[i];
    }
  }

  fill(0, 0, 255);
  for (int i = 0; i < bins.length; ++i) {
    int binHeight = height * bins[i] / binMax;
    rect(binWidth * i, height - binHeight, binWidth, binHeight);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 10.01.06.png (14.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/b47ced0f-74ae-4ab3-a916-979e859ef21d.png)

## 演習 11

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int k = 0; k < values.length; ++k) {
    int n = 100;
    float[] arrived = new float[n];
    float[] serviceTime = new float[n];

    float lambda = 3;
    arrived[0] = randomExponential(lambda);
    for (int i = 1; i < n; ++i) {
      arrived[i] = arrived[i - 1] + randomExponential(lambda);
    }

    float mu = 5;
    for (int i = 0; i < n; ++i) {
      serviceTime[i] = randomExponential(mu);
    }

    float[] finished = new float[n];
    finished[0] = arrived[0] + serviceTime[0];
    for (int i = 1; i < n; ++i) {
      float started;
      if (arrived[i] > finished[i - 1]) {
        started = arrived[i];
      } else {
        started = finished[i - 1];
      }
      finished[i] = started + serviceTime[i];
    }

    float waitTime = 0;
    for (int i = 0; i < n; ++i) {
      waitTime += finished[i] - arrived[i];
    }
    values[k] = waitTime / n;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 0, 2, 100);
  drawHistogram(bins);
}

float randomExponential(float lambda) {
  float x = random(0, 1);
  return -log(1 - x) / lambda;
}

int[] createBins(float[] values, float min, float max, int numBins) {
  int[] bins = new int[numBins];
  for (int i = 0; i < values.length; ++i) {
    int j = int((values[i] - min) * numBins / (max - min));
    if (0 <= j && j < numBins) {
      bins[j] += 1;
    }
  }
  return bins;
}

void drawHistogram(int[] bins) {
  float binWidth = float(width) / bins.length;

  int binMax = 0;
  for (int i = 0; i < bins.length; ++i) {
    if (bins[i] > binMax) {
      binMax = bins[i];
    }
  }

  fill(0, 0, 255);
  for (int i = 0; i < bins.length; ++i) {
    int binHeight = height * bins[i] / binMax;
    rect(binWidth * i, height - binHeight, binWidth, binHeight);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 12.42.59.png (14.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/06cba07c-ab1e-4c51-8499-f97035118416.png)

## 演習 12

```java
void setup() {
  size(600, 600);
  noLoop();
}

void draw() {
  background(255);
  for (int j = 0; j < 10; ++j) {
    float y0 = height / 2;
    for (int x = 1; x < width; ++x) {
      float y = y0 + 5 * randomGaussian() + 0.3;
      line(x - 1, y0, x, y);
      y0 = y;
    }
  }
}
```

![Screen Shot 2019-06-24 at 11.03.18.png (99.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/eb8cc9c8-7f9b-46e0-a9cb-b1d946b198ed.png)

## 演習 13

```java
float xTotal = 0;
float yTotal = 0;
float count = 0;

void setup() {
  size(600, 600);
  background(255);
}

void draw() {
  int repeat = 100;
  float x = width / 2;
  float y = height / 2;
  for (int i = 0; i < repeat; ++i) {
    x += 5 * randomGaussian() + 1;
    y += 10 * randomGaussian();
  }
  noStroke();
  fill(0, 0, 0, 127);
  ellipse(x, y, 5, 5);
  xTotal += x;
  yTotal += y;
  count += 1;
  println(xTotal / count, yTotal / count);
}
```

![Screen Shot 2019-06-24 at 8.58.31.png (47.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/c89e78d0-2a8f-452b-a67d-c22698565a59.png)

## 演習 14

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    float x = 0;
    for (int j = 0; j < 100; ++j) {
      x += int(random(-10, 11));
    }
    values[i] = x;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, -500, 500, 50);
  drawHistogram(bins);
}

int[] createBins(float[] values, float min, float max, int numBins) {
  int[] bins = new int[numBins];
  for (int i = 0; i < values.length; ++i) {
    int j = int((values[i] - min) * numBins / (max - min));
    if (0 <= j && j < numBins) {
      bins[j] += 1;
    }
  }
  return bins;
}

void drawHistogram(int[] bins) {
  float binWidth = float(width) / bins.length;

  int binMax = 0;
  for (int i = 0; i < bins.length; ++i) {
    if (bins[i] > binMax) {
      binMax = bins[i];
    }
  }

  fill(0, 0, 255);
  for (int i = 0; i < bins.length; ++i) {
    int binHeight = height * bins[i] / binMax;
    rect(binWidth * i, height - binHeight, binWidth, binHeight);
  }
}
```

![Screen Shot 2019-06-24 at 10.32.19.png (13.6 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/577a73e3-acaf-4fe1-a648-3e2722c9f19c.png)
