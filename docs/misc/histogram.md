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
