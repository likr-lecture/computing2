# ランダムウォーク

ある値が、一定時間ごとに、これまでの状態には依存せずにランダムに（確率分布に従って）変動するようなプロセスを **ランダムウォーク** と呼びます。
ランダムウォークでは、ある値 $$S$$ が $$S_0$$ からはじまり、時刻 $$i - 1$$ と $$i$$ の間の $$S$$ の変化量が確率変数 $$X_i \simeq F$$ と表される時、時刻 $$t$$ における $$S$$ の値 $$S_t$$ は次のように表されます。

$$
S_t = S_0 + \sum_{i=1}^t X_i
$$

ブラウン運動といった物理現象はランダムウォークと結びつけて解析が行われています。
また、金融工学では、株価の変動をランダムウォークとみなして利得やリスクの推定を行う理論があります。

## 1 次元のランダムウォーク

Processing で横軸に時間 $$x$$、縦軸にランダムウォークする値 $$y$$ を取ったグラフを描画してみましょう。
y の変化量は $$\delta \simeq N(0, 10^2)$$ とします。
以下のプログラムは、$$x$$ を 0 以上 `width` 未満の範囲で変化させ、$$y$$ を折れ線グラフでプロットします。

```java
float[] y;

void setup() {
  size(600, 600);
  float stdev = 10;
  y = new float[width];
  y[0] = height / 2;
  for (int x = 1; x < width; ++x) {
    y[x] = y[x - 1] + stdev * randomGaussian();
  }
}

void draw() {
  background(255);
  for (int x = 1; x < width; ++x) {
    line(x - 1, y[x - 1], x, y[x]);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-23 at 23.23.20.png (36.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/23/28750/6937d603-33b4-4cbb-bfad-f31bd0e87bc9.png)

また、上記のプログラムを 1 フレームごとに 1 ステップ進むようなアニメーションに改変したプログラムは以下のようになります。
キー入力によって新たな線を引くようになっています。

```java
float x0;
float y0;

void setup() {
  size(600, 600);
  background(255);
  x0 = 0;
  y0 = height / 2;
}

void draw() {
  float stdev = 10;
  float x1 = x0 + 1;
  float y1 = y0 + stdev * randomGaussian();

  line(x0, y0, x1, y1);

  x0 = x1;
  y0 = y1;
}

void keyPressed() {
  x0 = 0;
  y0 = height / 2;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-24 at 9.13.45.png (120.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/49ef758a-4f52-4d04-bada-1b940d35b3a3.png)

## 2 次元のランダムウォーク

次に、$$x, y$$ がそれぞれ独立にランダムウォークする場合を試してみます。
$$x, y$$ の初期値が (`width / 2`, `height / 2`) で、変化量がそれぞれ $$\Delta x \simeq N(0, 5^2), \Delta y \simeq N(0, 5^2)$$ として表されているとき、2 次元空間上に座標の変化をアニメーション表示するプログラムは以下のようになります。

```java
float x0;
float y0;

void setup() {
  size(600, 600);
  background(255);
  x0 = width / 2;
  y0 = height / 2;
}

void draw() {
  float stdev = 5;
  float x1 = x0 + stdev * randomGaussian();
  float y1 = y0 + stdev * randomGaussian();

  line(x0, y0, x1, y1);

  x0 = x1;
  y0 = y1;
}
```

実行結果の軌跡以下のようになります。

![Screen Shot 2019-06-24 at 9.20.15.png (88.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/ae80b89d-d8ec-4d2c-9d3b-9ed92f5dd4b4.png)

画面の中心から線が不規則に動き回っていることが確認できます。

# ブラウン運動

溶媒中の微粒子が、分子の運動によって不規則に動くような現象を **ブラウン運動** と呼びます。
微粒子や分子は力学的な運動を行なっていますが、大量の分子が微粒子に不規則に衝突することで、微粒子の動きも不規則となります。
このとき、微粒子の動きがランダムウォークとなることが証明されています。
Einstein によるブラウン運動の理論的な解明は、分子や原子の存在の証明につながりました。

参考：

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/c2sYdKfR4tY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

以下は 500 個の点がランダムウォークするプログラムです。

```java
int n = 500;
float[] x = new float[n];
float[] y = new float[n];
void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}
void draw() {
  background(255);
  float stdev = 2;
  float d = 10;

  fill(0);
  for (int i = 0; i < n; ++i) {
    ellipse(x[i], y[i], d, d);
    x[i] += stdev * randomGaussian();
    y[i] += stdev * randomGaussian();
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-06-24 at 10.37.26.png (80.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/5847099e-2327-4c44-84c6-d5706b00b762.png)

点の動きはブラウン運動と同じような振る舞いをしていることが確認できます。

# ランダムウォークの期待値

ランダムウォークの性質から、$$X_i$$ と $$X_j$$ ($$i \neq j$$) は独立です。
そのため、$$E[X_i] = \mu, V[X_i] = \sigma ^2 \quad (i \in \{1, \cdots, t\})$$ とすると $$S_t$$ の期待値と分散は以下のように表されます。

$$
\begin{aligned}
E[S_t] &= S_0 + t \mu \\
V[S_t] &= t \sigma^2
\end{aligned}
$$

$$(x, y)$$ の変化量がそれぞれ $$\Delta x \simeq N(0, 5^2), \Delta y \simeq N(0, 5^2)$$ で表されるランダムウォークについて考えます。
初期値を (`width / 2`, `height / 2`) としたとき、100 ステップ後の座標の期待値をモンテカルロシミュレーションによって計算してみます。
1 フレームごとに 1 つの点をプロットし、期待値を計算していきます。

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
  float stdev = 5;
  float x = width / 2;
  float y = height / 2;
  for (int i = 0; i < repeat; ++i) {
    x += stdev * randomGaussian();
    y += stdev * randomGaussian();
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

実行結果は以下のようになります。

![Screen Shot 2019-06-23 at 23.42.54.png (82.2 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/0111211d-866c-47b9-8ff0-fc76b8436f6e.png)

100 ステップ後の座標は、期待値である (`width / 2`, `height / 2`) を中心に分布していることが確認できます。
以下のように、コンソールに出力される値も大体期待値通りとなっています。

```console
299.91098 298.39056
```

また、分散はステップ数に比例するので、以下のようにステップ数が小さければバラツキが小さく、ステップ数が大きければバラツキが大きくなります。

ステップ数 10

![Screen Shot 2019-06-24 at 8.45.59.png (23.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/31ca366f-0892-4290-b92a-2b44ac840673.png)

ステップ数 50

![Screen Shot 2019-06-24 at 8.46.15.png (35.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/b7555ad3-6497-4d17-ba1f-9acc1b285a34.png)

ステップ数 200

![Screen Shot 2019-06-24 at 8.46.31.png (54.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/e2971c7a-7dfe-48d6-905c-3178cf057739.png)

# ランダムウォークと中心極限定理

**中心極限定理** によると、期待値 $$\mu$$、分散 $$\sigma^2$$ の独立な確率変数列 $$X_1, X_2, \cdots$$ において、 $$S_n = \sum_{i=1}^n X_i$$ は $$n$$ が十分に大きい時に正規分布 $$N(n \mu, n \sigma^2)$$ に従います。
すなわち、ある値の変化量が中心極限定理が成り立つような確率分布に従っていて、十分なステップ数をとるようなランダムウォークでは、確率分布によらず最終的な値は正規分布に従うことができます。

例えば、$$x$$ の初期値が 0、変化量が $$\Delta x \simeq U(-10, 10)$$ の 100 ステップのランダムウォークについて考えます。
最終的な $$x$$ の値のヒストグラムを描画するプログラムは以下のようになります。

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    float x = 0;
    for (int j = 0; j < 100; ++j) {
      x += random(-10, 10);
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

実行結果は以下のようになります。

![Screen Shot 2019-06-24 at 9.48.38.png (14.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/24/28750/c7234eb0-7df4-442f-9509-270a8821b407.png)

$$x$$ の変化量自体は一様分布であるにも関わらず、100 ステップ後の座標は正規分布のようになっていることが確認できます。
