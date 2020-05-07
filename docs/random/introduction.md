# 擬似乱数

コンピュータで何らかのアプリケーションを実現する上で **乱数** は様々な場面で出現します。
ゲームなどでゲーム性を高めるためにランダム要素が取り入れられます。
また、厳密に解くことが難しい問題でも、乱数を利用した **乱択アルゴリズム** によって比較的容易に解くことができるようになります。

私たちが普段使うコンピュータは決定性のある計算機なので、本来入力が同じであれば出力も常に同じになります。
しかし、乱数を用いた計算は、入力が同じであっても出力が毎回異なります。
これは、乱数関数が決定的な計算からランダムに見えるような数列を生成し、呼び出されるたびに新たな数列の値を返すことで実現しています。
このような、ランダムに見える関数を使って擬似的に乱数を生成する方法を **擬似乱数** と呼びます。
コンピュータ上の乱数は基本的には擬似乱数が用いられます。

Processing の乱数関数 `random` について復習しましょう。
`random` 関数は一つまたは二つの実数型の引数を受け取ります。
1 つの引数で `random(high)` と呼び出された場合、`random` 関数は 0 以上 `high` 未満の実数値をランダムに返します。
2 つの引数で `random(low, high)` と呼び出された場合、 `random` 関数は `low` 以上 `high` 未満の実数値をランダムに返します。
`random` 関数は一様乱数を返すため、`low`（または 0）と `high` の間の値が等確率で出現します。

例えば、確率 $$0 \leq p \leq 1$$ である処理を実行したい場合には、以下のようなプログラムを書くことができます。

```java
float p = 0.1;
if (random(1) < p) {
  // 10%の確率で実行する処理
}
```

コインを投げたときに表が出る確率を 50%とし、10000 回コインを投げたときに表が出た回数を数えるプログラムは以下のようになります。

```java
int repeat = 10000;
int count = 0;
for (int i = 0; i < repeat; ++i) {
  if (random(1) < 0.5) {
    count += 1;
  }
}
println(count + " / " + repeat);
```

実行結果は以下のようになります。

```console
4979 / 10000
```

# モンテカルロシミュレーション

**モンテカルロシミュレーション** は、乱数を用いた試行を十分な回数繰り返すことによってシステムの統計的な振る舞いを調べるシミュレーションの手法です。

以下は 2 個の 6 面ダイスを振って、出た目の合計値を調べるプログラムです。
これも一種のモンテカルロシミュレーションと見做すことができます。

```java
int[] count = new int[11];

int repeat = 10000;
for (int i = 0; i < repeat; ++i) {
  int a = int(random(1, 7));
  int b = int(random(1, 7));
  int sum = a + b;
  count[sum - 2] += 1;
}

for (int i = 0; i < count.length; ++i) {
  println((i + 2) + " : " + count[i]);
}
```

実行結果は以下のようになります。

```console
2 : 261
3 : 569
4 : 879
5 : 1116
6 : 1422
7 : 1603
8 : 1377
9 : 1117
10 : 822
11 : 554
12 : 280
```

## 演習 1

5 回コインを投げて、ちょうど 3 回表が出る確率をモンテカルロシミュレーションによって求めましょう。

# モンティ・ホール問題

次のような問題を考えます。
3 つのドアがあり、そのうち 1 つのドアが当たりになっています。
プレイヤーは 3 つのドアから 1 つを選びます。
司会者は、プレイヤーがドアを選んだ後、残った２つのドアのうち外れのドアを１つ開きます。
司会者がドアを選んだ後、プレイヤーはドアを選び直すことができます。
ドアを選び直した場合とそうじゃない場合で当たりを引く確率に違いはあるでしょうか？
これは、**モンティ・ホール問題** と呼ばれる確率論の有名な問題です。

ドアを選び直さない場合当たりの確率は 1/3 ですが、ドアを選び直すと当たりの確率は 2/3 になります。
これをモンテカルロシミュレーションで確かめてみましょう。

```java
int repeat = 10000;
int count;

count = 0;
for (int i = 0; i < repeat; ++i) {
  int hit = int(random(0, 3));
  int answer = int(random(0, 3));
  if (hit == answer) {
    count += 1;
  }
}
println(float(count) / repeat);

count = 0;
for (int i = 0; i < repeat; ++i) {
  int hit = int(random(0, 3));
  int answer = int(random(0, 3));
  if (hit == 0) {
    if (answer == 0) {
      if (random(1) < 0.5) {
        answer = 1;
      } else {
        answer = 2;
      }
    } else if (answer == 1) {
      answer = 0;
    } else {
      answer = 0;
    }
  } else if (hit == 1) {
    if (answer == 0) {
      answer = 1;
    } else if (answer == 1) {
      if (random(1) < 0.5) {
        answer = 0;
      } else {
        answer = 2;
      }
    } else {
      answer = 1;
    }
  } else {
    if (answer == 0) {
      answer = 2;
    } else if (answer == 1) {
      answer = 2;
    } else {
      if (random(1) < 0.5) {
        answer = 0;
      } else {
        answer = 1;
      }
    }
  }
  if (answer == hit) {
    count += 1;
  }
}
println(float(count) / repeat);
```

## 演習 2

以下の問題をモンテカルロシミュレーションによって解きましょう。

> ある工場では，同じ製品を独立した二つのライン A，B で製造している。ライン A では製品全体の 60%を製造し，ライン B では 40%を製造している。ライン A で製造された製品の 2%が不良品であり，ライン B で製造された製品の 1%が不良品であることが分かっている。いま，この工場で製造された製品の一つを無作為に抽出して調べたところ，それは不良品であった。その製品がライン A で製造された確率は何%か。[基本情報技術者試験平成 28 年秋期　問 2]

# 円の面積の計算

モンテカルロシミュレーションの有名な応用として、円周率の近似があります。

原点(0, 0)を中心とした半径 1 の円の方程式は以下のように表されます。

$$
x^2 + y^2 = 1
$$

この円の面積は円周率と等しくなります。
ここで、この円の $$x \geq 0$$、$$y \geq 0$$ の領域 A を考えます。
A の面積は元の円の面積の 1/4 です。
また A は、(0, 0), (0, 1), (1, 0), (1, 1)を頂点とした一辺の長さが 1 の正方形で囲むことができます。
正方形の中の点をランダムに選んだとき、その点が A に含まれる確率は A の面積に等しくなります。
すなわち、$$0 \leq x \leq 1$$、$$0 \leq y \leq 1$$の範囲で乱数 $$(x, y)$$ を発生させ、その点が A に含まれた回数を数えることで A の面積を近似できます。
そして、A の面積を 4 倍することで元の円の面積、すなわち円周率を得ることができます。

これに基づいて Processing でプログラムを作成すると以下のようになります。

```java
int count = 0;
int repeat = 100000;
for (int i = 0; i < repeat; ++i) {
  float x = random(1);
  float y = random(1);
  if (x * x + y * y < 1) {
    count += 1;
  }
}
println(float(count) / repeat * 4);
```

実行結果は以下のようになります。

```console
3.14032
```

大体円周率に近い値を得ることができています。

## 演習 3

以下の式で表される 4 次元球の体積をモンテカルロシミュレーションで求めましょう。

$$
x^2 + y^2 + z^2 + w^2 = 1
$$

# まとめ

Processing の乱数関数を復習し、モンテカルロシミュレーションによって大まかな確率の計算ができることを確認しました。
次回から、擬似乱数の仕組みや非一様乱数の生成法について学習します。

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

# ヒストグラムの描画

ある範囲の値が何回現れたかは **ヒストグラム** （**度数分布表**）で可視化します。
ヒストグラムにおける棒のことはビンと呼ばれます。

0 以上 100 未満の実数の分布を表示する Processing のプログラムは以下のようになります。

```java
float[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new float[10000];
  for (int i = 0; i < values.length; ++i) {
    values[i] = random(100);
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 0, 100, 10);
  drawHistogram(bins);
}

int[] createBins(float[] values, float min, float max, int numBins) {
  int[] bins = new int[numBins];
  for (int i = 0; i < values.length; ++i) {
    int j = int((values[i] - min) * numBins / (max - min));
    bins[j] += 1;
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

上記のプログラムは、それぞれのビンに含まれる実数の個数を数えます。

整数値をとるような事象を数える場合は、`createBins` 関数が以下のようになります。
ここでは、コインを投げて表と裏が出た回数をそれぞれ数えてヒストグラム化しています。

```java
int[] values;

void setup() {
  size(600, 600);
  noLoop();

  values = new int[10000];
  for (int i = 0; i < values.length; ++i) {
    if (random(1) < 0.5) {
      values[i] = 0;
    } else {
      values[i] = 1;
    }
  }
}

void draw() {
  background(255);
  int[] bins = createBins(values, 0, 1);
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

## 演習 1

2 個の 6 面ダイスを振って、出た目の合計値の分布をヒストグラムで描画しましょう。

# 乱数の種

擬似乱数によって生成される数列を制御するためには乱数の **種** （seed）が用いられます。
Processing での乱数種の設定は `randomSeed` 関数を用います。
`randomSeed` 関数は引数として受け取った整数値を乱数種として設定します。

以下は乱数種を用いずに 5 個の実数値を表示するプログラムです。

```java
for (int i = 0; i < 5; ++i) {
  println(random(1));
}
```

1 回目の実行結果は以下の通りです。

```console
0.901366
0.9354197
0.4483686
0.34030527
0.87377113
```

2 回目の実行結果は以下の通りです。

```console
0.034834802
0.20180964
0.73463714
0.8813292
0.7115238
```

1 回目と 2 回目で実行結果が異なっていることが確認できます。

以下のように `randomSeed` 関数で乱数の種を 0 に設定してみます。

```java
randomSeed(0);
for (int i = 0; i < 5; ++i) {
  println(random(1));
}
```

1 回目の実行結果は以下の通りです。

```console
0.73096776
0.831441
0.24053639
0.6063452
0.6374174
```

2 回目の実行結果は以下の通りです。

```console
0.73096776
0.831441
0.24053639
0.6063452
0.6374174
```

乱数種を同じにすることで 1 回目と 2 回目で同じ実行結果になりました。

一般的には、実行時の時間などを乱数種に利用することで実行毎に異なる結果を発生させます。

# 線形合同法

**線形合同法**（LCG; Linear Congruential Generators）は擬似乱数生成のアルゴリズムです。
線形合同法は一様乱数を生成します。

線形合同法の生成する乱数列 $$X_0, X_1, X_2, \cdots$$ は以下の式で表されます。

$$
X_{n + 1} = a X_n + b \mod m
$$

$$a, b, m$$ は乱数列を決定するためのパラメータです。
剰余演算を用いるため、$$X_n$$ の範囲は $$0 \leq X_n < m$$ となります。
また、$$X_0$$は乱数種となります。

$$X_n$$ を用いて $$Y_L$$ 以上 $$Y_U$$ 未満の実数値 $$Y_n$$ を生成するためには以下の変換式を用いることができます。

$$
Y_n = \frac{X_n}{m} (Y_U - Y_L) + Y_L
$$

# 線形合同法の実装

線形合同法による乱数生成プログラムの例は以下の通りです。
パラメーターは $$a = 5, b = 3, m = 16$$ に設定されています。

```java
int seed = 1;

int rand() {
  int a = 5;
  int b = 3;
  int m = 16;
  seed = (a * seed + b) % m;

  return seed;
}

void setup() {
  noLoop();
}

void draw() {
  int repeat = 20;
  for (int i = 0; i < repeat; ++i) {
    println(rand());
  }
}
```

実行結果は以下のようになります。

```console
8
11
10
5
12
15
14
9
0
3
2
13
4
7
6
1
8
11
10
5
```

$$m=16$$であるため、0 以上 16 未満の整数値が順不同に出力されています。
しかし、17 回目から 20 回目の出力結果が 1 回目から 4 回目の出力結果が一致していることに注目しましょう。
線形合同法では剰余演算を用いているため、このような法則性が現れます。

# 線形合同法の周期性

線形合同法は最大で $$m$$ 通りの整数値を生成します。
$$a, b, m$$ の選び方によっては、生成される値の種類が $$m$$ より小さくなる場合があります。

パラメーターを $$a = 7, b = 3, m = 16$$ に設定した場合の出力結果は以下のようになります。

```console
10
9
2
1
10
9
2
1
10
9
2
1
10
9
2
1
10
9
2
1
```

生成される値は 1, 2, 9, 10 の 4 通りのみとなっています。
また、10, 9, 2, 1, 10, 9, 2, 1 と 4 回ごとに同じパターンが出力されています。
このときに、この乱数列の周期が 4 であると言います。
乱数列の周期は長い方が望ましく、最大で $$m$$ となります。
周期はパラメーター $$a, b, m$$ の値によって決まります。

線形合同法を用いた乱数列で、6 面ダイスを振ったときの出た目の分布をヒストグラムで描画します。
パラメーターは $$a = 601, b=111, m=1024$$ としています。

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

実行結果は以下のようになります。

![Screen Shot 2019-06-10 at 10.17.22.png (13.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/e2813b0e-fbc9-4c2a-9452-d3b8ec5b305f.png)

$$a = 607$$ に変更したときの結果は以下のようになります。

![Screen Shot 2019-06-10 at 10.17.10.png (12.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/fe5e5a22-5675-4f63-9c11-5f22c838f0d2.png)

3 と 6 の出た回数が目に見えて少なくなっています。
パラメーターの設定の仕方によっては、このように正しく一様分布を生成することができません。

さらに `seed` を 5 に変更したときの結果は以下のようになります。

![Screen Shot 2019-06-10 at 10.17.39.png (13.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/06/10/28750/83e98430-8b95-4547-ad26-44fd19692618.png)

`seed` を変えても、分布は違うものの偏りが発生しています。

線形合同法には以下のようなメリット・デメリットが存在します。

- メリット
  - 実装が容易
  - 生成が高速
- デメリット
  - 生成される乱数列に法則性がある

よく知られている線形合同法の実装では $$m = 2^{32}$$ となっています。
それより多い個数の乱数が必要な場合は、その周期性に注意しましょう。
より予測困難な乱数が必要な場合は **メルセンヌツイスタ** などの他の乱数生成法を使用する必要があります。

## 演習 2

`lcg_histogram.pde` を改変し、$$a, b, m$$ および乱数種を変えたときにどのような変化があるか確認してみましょう。

## 演習 3

線形合同法を用いて（Processing の `random` 関数を用いずに）モンテカルロ法による円周率の計算を行いましょう。
生成する点の数は 1000 とします。
その際に、$$0 \leq x \leq 1$$、$$0 \leq y \leq 1$$の範囲で生成した点を画面に描画しましょう。

線形合同法のパラメーターは $$a = 601, b=111, m=1024$$ とします。
また、パラメーター $$a = 607, b=111, m=1024$$ でも試してみましょう。

# オーバーフローを考慮した線形合同法の実装

線形合同法は 0 以上 $$m$$ 未満の整数を発生させます。
線形合同法をプログラムで実装する場合には整数のオーバーフローに注意する必要があります。
$$m$$ が 2 のべき乗の場合、符号無し整数ではオーバーフロー部分を無視することができます。
しかし、Processing には符号無し整数がないため実装上の工夫を行う必要があります。

Processing の int 型は 32bit の符号付整数です。
そして、Processing の int 型で表すことができる最大の値は 2147483647 です。
$$m = 2147483647$$ のとき、0 以上 2147483646 以下の整数値が生成されます。
$$b = 0$$ の場合の乱数生成について考えてみましょう。

$$T$$ を以下のように定義します。

$$
\begin{aligned}
q &= \left\lfloor \frac{m}{a} \right\rfloor \\
r &= m - a q \\
H &= \left\lfloor \frac{X_n}{q} \right\rfloor \\
L &= X_n - q H \\
T &= a L - r H
\end{aligned}
$$

このとき、

$$
a X_n \equiv T \mod m
$$

を満たします。

また、$$m > T \geq -m$$ となるので、$$X_{n + 1}$$ の更新式は以下のようになります。

$$
\begin{align}
X_{n + 1} \ = \begin{cases}
T & (T \geq 0) \\
T + m & (T < 0)
\end{cases}
\end{align}
$$

$$a = 48271$$ の場合のプログラムは以下のようになります。

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

void setup() {
  noLoop();
}

void draw() {
  int repeat = 10;
  for (int i = 0; i < repeat; ++i) {
    println(rand());
  }
}
```

## 演習 4

線形合同法のパラメーター$$a = 48271, b = 0, m = 2147483647$$ を用いて演習 3 のシミュレーションを行いましょう。

# まとめ

一様乱数の生成法である線形合同法について学習し、その Processing での実装について確認しました。
ヒストグラムの描画により、適切にパラメーター設定した線形合同法の実装が Processing 組み込みの `random` 関数と同様の振る舞いをすることを確認しました。

本稿のプログラムは乱数種をグローバル変数として格納しています。
プログラムの保守性を高めるために、興味のある人はクラスを利用した乱数生成器の実装について考えてみましょう。

# 演習問題の解答例

## 演習１

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

## 演習 2

省略

## 演習 3

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

## 演習 4

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

## 演習 1

3 つの科目 A、B、C の得点の分布はそれぞれ独立に正規分布 $$N(60, 5^2)$$、$$N(70,5^2)$$、$$N(65,10^2)$$ に従っています。
80 人の学生が科目 A、B、C を受けているとき、3 つの科目の合計点の平均と標準偏差を計算しましょう。

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

## 演習 2

ロジスティック分布のヒストグラムを描画しましょう。
ロジスティック分布の分布関数は以下の通りです。

$$
F(x) = \frac{1}{1 + e^{-x}}
$$

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

## 演習 3

`randomGaussian()` 関数を使用せずに正規分布 $$N(50, 10^2)$$ のヒストグラムを描画しましょう。

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

## 演習 4

$$\lambda = 3, \mu = 5$$ の M/M/1 モデルの待ち行列の平均待ち時間の分布をヒストグラムで描画しましょう。
（待ち行列のシミュレーションを十分な回数行い、その分布を調べましょう。）

# まとめ

一様乱数から、指数分布や正規分布などの他の確率分布を求める方法を学びました。
一様乱数以外の確率分布を用いることで様々な現象をシミュレーションできるようになります。

# 演習問題の解答例

## 演習 1

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

## 演習 2

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

## 演習 3

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

## 演習 4

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

## 演習 1

横軸に時間 $$x$$、縦軸に $$N(0.3, 5^2)$$ に従ってランダムウォークする値 $$y$$ を取った折れ線グラフ（$$0 \leq x < \text{width}$$）を 10 本描画するプログラムを作成しましょう。

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

## 演習 2

$$(x, y)$$ の変化量がそれぞれ $$\Delta x \simeq N(1, 5^2), \Delta y \simeq N(0, 10^2)$$ で表されるランダムウォークについて、初期値を (`width / 2`, `height / 2`) としたとき、100 ステップ後の座標の期待値をモンテカルロシミュレーションによって計算しましょう。

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

## 演習 3

$$x$$ の初期値が 0、変化量が $$\{-10, -9, \cdots, 9, 10\}$$ の値が等確率で現れる離散一様分布に従う 100 ステップのランダムウォークにおいて、最終的な $$x$$ の値のヒストグラムを表示するプログラムを作成しましょう。

# まとめ

乱数を用いたランダムウォークのシミュレーションを行いました。
また、モンテカルロシミュレーションを用いてランダムウォークの主要な性質を確かめました。

微粒子のブラウン運動のように、ミクロには力学的に決定な振る舞いをするシステムが、マクロにはランダムウォークをする場合もあります。
経済活動も、ミクロには様々なステークホルダーが様々な思惑で意思決定をしていますが、マクロに見るとランダムウォークと大差ない場合もあるでしょう。

# 演習問題の解答例

## 演習 1

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

## 演習 2

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

## 演習 3

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
