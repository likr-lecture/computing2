# 演習

## 演習 1

5 回コインを投げて、ちょうど 3 回表が出る確率をモンテカルロシミュレーションによって求めましょう。

## 演習 2

以下の問題をモンテカルロシミュレーションによって解きましょう。

> ある工場では，同じ製品を独立した二つのライン A，B で製造している。ライン A では製品全体の 60%を製造し，ライン B では 40%を製造している。ライン A で製造された製品の 2%が不良品であり，ライン B で製造された製品の 1%が不良品であることが分かっている。いま，この工場で製造された製品の一つを無作為に抽出して調べたところ，それは不良品であった。その製品がライン A で製造された確率は何%か。[基本情報技術者試験平成 28 年秋期　問 2]

## 演習 3

以下の式で表される 4 次元球の体積をモンテカルロシミュレーションで求めましょう。

$$
x^2 + y^2 + z^2 + w^2 = 1
$$

## 演習 4

2 個の 6 面ダイスを振って、出た目の合計値の分布をヒストグラムで描画しましょう。

## 演習 5

以下のプログラムを改変し、$$a, b, m$$ および乱数種を変えたときにどのような変化があるか確認してみましょう。

```java
int seed = 1;

float rand() {
  int a = 601;
  int b = 111;
  int m = 1024;
  seed = (a * seed + b) % m;

  return float(seed) / m * 6 + 1;
}

int[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new int[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = int(rand());
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 1, 6);
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

## 演習 6

線形合同法を用いて（Processing の `random` 関数を用いずに）モンテカルロ法による円周率の計算を行いましょう。
生成する点の数は 1000 とします。
その際に、$$0 \leq x \leq 1$$、$$0 \leq y \leq 1$$の範囲で生成した点を画面に描画しましょう。

線形合同法のパラメーターは $$a = 601, b=111, m=1024$$ とします。
また、パラメーター $$a = 607, b=111, m=1024$$ でも試してみましょう。

## 演習 7

線形合同法のパラメーター$$a = 48271, b = 0, m = 2147483647$$ を用いて演習 6 のシミュレーションを行いましょう。

## 演習 8

3 つの科目 A、B、C の得点の分布はそれぞれ独立に正規分布 $$N(60, 5^2)$$、$$N(70,5^2)$$、$$N(65,10^2)$$ に従っています。
80 人の学生が科目 A、B、C を受けているとき、3 つの科目の合計点の平均と標準偏差を計算しましょう。

## 演習 9

ロジスティック分布のヒストグラムを描画しましょう。
ロジスティック分布の分布関数は以下の通りです。

$$
F(x) = \frac{1}{1 + e^{-x}}
$$

## 演習 10

`randomGaussian()` 関数を使用せずに正規分布 $$N(50, 10^2)$$ のヒストグラムを描画しましょう。

## 演習 11

$$\lambda = 3, \mu = 5$$ の M/M/1 モデルの待ち行列の平均待ち時間の分布をヒストグラムで描画しましょう。
（待ち行列のシミュレーションを十分な回数行い、その分布を調べましょう。）

## 演習 12

横軸に時間 $$x$$、縦軸に $$N(0.3, 5^2)$$ に従ってランダムウォークする値 $$y$$ を取った折れ線グラフ（$$0 \leq x < \text{width}$$）を 10 本描画するプログラムを作成しましょう。

## 演習 13

$$(x, y)$$ の変化量がそれぞれ $$\Delta x \simeq N(1, 5^2), \Delta y \simeq N(0, 10^2)$$ で表されるランダムウォークについて、初期値を (`width / 2`, `height / 2`) としたとき、100 ステップ後の座標の期待値をモンテカルロシミュレーションによって計算しましょう。

## 演習 14

$$x$$ の初期値が 0、変化量が $$\{-10, -9, \cdots, 9, 10\}$$ の値が等確率で現れる離散一様分布に従う 100 ステップのランダムウォークにおいて、最終的な $$x$$ の値のヒストグラムを表示するプログラムを作成しましょう。