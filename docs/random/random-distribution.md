# 確率変数と確率分布

どのような値を取るかが確率的に決まる変数のことを **確率変数** と呼びます。
**確率分布関数** は、確率変数がある値を取る確率の分布を表したものです。
具体的には、確率分布関数は **確率密度関数** の積分で定義されます。

確率変数 $$X$$ が確率分布関数 $$F(x)$$ に従うことを $$X \simeq F(x)$$ と表記します。

# 正規分布

**正規分布** （ **ガウス分布** ）は、平均値を中心に左右対称な山型の分布をする確率分布です。
正規分布では、平均値から遠い値ほど発生する確率が小さくなります。
平均が $$\mu$$ 、標準偏差が $$\sigma$$ の正規分布を $$N(\mu, \sigma^2)$$ と表記します。

平均が 0、標準偏差が 1 の正規分布を **標準正規分布** と呼びます。
$$X \simeq N(0, 1)$$ と $$Y \simeq N(\mu, \sigma^2)$$ の関係は $$Y = \sigma X + \mu$$ となります。
すなわち、標準正規分布を生成することができれば、任意の正規分布を生成することができます。

Processing では、`randomGaussian()` 関数によって標準正規分布に従う乱数列を生成することができます。
`randomGaussian()` 関数は引数を取りません。

以下は Processing で標準正規分布に従う値を 10 個コンソールに表示するプログラムです。

```java
for (int i = 0; i < 10; ++i) {
  println(randomGaussian());
}
```

出力結果は以下のようになります。

```console
1.0645853
-0.121457554
-2.4501405
0.3811973
1.0769348
-2.1460996
0.073974945
0.5043901
-3.0981495
-0.6422674
```

標準正規分布のヒストグラムを描画するプログラムは以下の通りです。

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = randomGaussian() ;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, -10, 10, 50);
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

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 9.10.44.png (14.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/b8afabcf-2203-4120-9472-33d27b3f60a8.png)

# 逆関数法

Processing が提供している確率分布は一様分布と正規分布のみです。
それ以外の確率分布は自分で実装する必要があります。
一様分布の乱数を用いて、それ以外の確率分布に従った乱数を生成する方法について考えてみましょう。

確率変数 $$X$$ の確率分布関数が $$F(x)$$ として与えられてるとします。
確率分布関数の性質から $$0 \leq F(x) \leq 1$$ となります。
また、$$F(x) = y$$ のとき、$$-\infty$$ から $$x$$ までの累積確率が $$y$$ となっています。
すなわち、$$F(x)$$ の逆関数 $$F^{-1}(y)$$ と確率変数 $$Y \sim U(0,1)$$ が与えられているとき、 $$X = F^{-1}(Y)$$ によって確率分布関数 $$F(x)$$ に従う乱数を得ることができます。
この方法を **逆関数法** と呼びます。

## 一様分布に従った乱数の生成

$$a$$ 以上 $$b$$ 未満の範囲の一様乱数を $$U(a, b)$$ と表します。
まずは $$X \simeq U(0, 1)$$ から $$U(a, b)$$ に従う乱数列を生成することを考えてみましょう。

$$U(a, b)$$ の確率分布関数 $$F(x)$$ は以下のようになります。

$$
F(x) = \frac{x - a}{b - a} \qquad (a \leq x < b)
$$

このとき $$F(x)$$ の逆関数は以下のようになります。

$$
F^{-1}(x) = (b - a) x + a \qquad (0 \leq x < 1)
$$

逆関数法で $$U(0, 1)$$ から $$U(-10, 10)$$ に従った乱数を生成し、ヒストグラムを描画するプログラムは以下のようになります。

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = 20 * random(0, 1) -10;
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, -10, 10, 20);
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

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 9.20.39.png (13.6 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/d866fa79-6f8c-4df8-a034-93492c754fe8.png)

## 指数分布に従った乱数の生成

次に指数分布に従った乱数を逆関数法で生成します。
あるイベントが時間的に一様に発生するとき、イベントの発生間隔は **指数分布** $$\text{Exp}(\lambda)$$ に従います。
このとき、イベント発生間隔の期待値は $$\frac{1}{\lambda}$$ となります。

以下は $$\text{Exp}(\lambda)$$ の分布関数 $$F(x)$$ です。

$$
F(x) = 1 - e^{-\lambda x}
$$

逆関数は以下のようになります。

$$
F^{-1}(x) = - \frac{\log(1 - x)}{\lambda}
$$

逆関数法によって指数分布に従う乱数を生成するプログラムは以下のようになります。

```java
void setup() {
  noLoop();
}

void draw() {
  background(255);
  for (int i = 0; i < 10; ++i) {
    println(randomExponential(2));
  }
}

float randomExponential(float lambda) {
  float x = random(0, 1);
  return -log(1 - x) / lambda;
}
```

実行結果は以下のようになります。

```console
0.8439477
0.08957177
0.4351533
0.050842267
0.16265357
0.7450313
0.27376103
0.8175968
0.96898216
1.5987735
```

# 正規分布に従った乱数の生成

正規分布の分布関数の逆関数は求めることができないため、逆関数法を直接適用することができません。
しかし、標準正規分布は独立な確率変数 $$R \simeq \text{Exp}(2)$$ と $$\Theta \simeq U(0, 2 \pi)$$ を用いて以下のように表されます。

$$
N(0, 1) = \sqrt{R} \cos(\Theta)
$$

すなわち、逆関数法によって $$R$$ と $$\Theta$$ が得られれば標準正規分布を得ることができます。
この方法は **Box-Muller 法** と呼ばれています。

以下は Box-Muller 法によって $$N(0, 1)$$ に従う乱数を 10 個コンソールに表示するプログラムです。

```java
void setup() {
  noLoop();
}

void draw() {
  background(255);
  for (int i = 0; i < 10; ++i) {
    println(gaussian());
  }
}

float gaussian() {
  float x = random(0, 1);
  float y = random(0, 1);
  return sqrt(-2 * log(x)) * cos(TWO_PI * y);
}
```

実行結果は以下のようになります。

```console
-0.4542584
-0.85491806
-0.825849
0.22304444
0.06334269
2.0772574
0.026376083
0.23405305
-1.9164213
0.07818792
```

# Poisson 分布に従った乱数の生成

あるイベントが時間的に一様に発生するとき、単位時間あたりにイベントが発生する回数は **Poisson 分布** に従います。
Poisson 分布の確率分布を $$Po(\lambda)$$ と表したとき、その期待値は $$\lambda$$ となります。

Poisson 分布 $$Po(\lambda)$$ に従った乱数は、指数分布 $$\text{Exp}(\lambda)$$ を用いて生成することができます。
すなわち、指数分布に従ったイベントが一定時間の間に何回発生したかを数えることで Poisson 分布に従った乱数を得ることができます。

Poisson 分布のヒストグラムを描画するプログラムは以下のようになります。
Poisson 分布は離散的な確率分布であることに注意しましょう。

```java
int[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new int[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = poisson(2);
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 0, 10);
  drawHistogram(bins);
}

int poisson(float lambda) {
  float xp;
  int k = 0;
  xp = random(0, 1);
  while (xp >= exp(-lambda)) {
    xp = xp * random(0, 1);
    k = k + 1;
  }
  return (k);
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

実行結果は以下のようになります。

![Screen Shot 2019-06-17 at 2.48.28.png (13.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/17/28750/49516503-4dc5-4574-9846-ebfc812892ad.png)

# 待ち行列のシミュレーション

**待ち行列理論** では、顧客がサービスを受けるために列に並び、その待ち時間などの性質を調べます。
待ち行列の最も基本的なモデルは M/M/1 モデルと呼ばれます。
**M/M/1 モデル** では、サービスを提供する窓口が 1 つあり、サービスを受けるために顧客は待ち行列に並びます。
単位時間あたりに到着する顧客数は、期待値が $$\frac{1}{\lambda}$$ の指数分布に従います。
また、一人の顧客にかかるサービス時間は、期待値が $$\frac{1}{\mu}$$ の指数分布に従います。

$$\lambda = 3, \mu = 5$$ としたときの、平均待ち時間（顧客が列に到着してからサービスを受け終わるまでにかかる時間）をモンテカルロシミュレーションによって求める以下のプログラムを完成させましょう。

`arrived[i]` は `i` 番目の顧客が到着した時刻、`serviceTime[i]` は `i` 番目の顧客のサービスにかかった時間、`finished[i]` は `i` 番目の顧客がサービスを受け終わった時刻を表しています。
平均待ち時間は `finished[i] - arrived[i]` の平均値となります。

```java
void setup() {
  noLoop();
}

void draw() {
  int n = 100000;
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
  waitTime /= n;
  println(waitTime);
}

float randomExponential(float lambda) {
  float x = random(0, 1);
  return -log(1 - x) / lambda;
}
```

実行結果は以下のようになります。

```console
0.4989177
```

M/M/1 モデルの場合は解析解が知られており、それは $$\frac{1}{\mu - \lambda} = \frac{1}{2}$$ であるため、大体一致していることが確認できます。
