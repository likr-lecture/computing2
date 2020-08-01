# 色空間と colorMode

色をランダムに生成することを考えてみましょう。
例えば以下のように RGB の各成分を `random` 関数によって生成することができます。

```java
size(600, 600);
background(255);
int n = 10;
int d = width / n;
for (int i = 0; i < n; ++i) {
  for (int j = 0; j < n; ++j) {
    fill(random(255), random(255), random(255));
    ellipse(d * j + d / 2, d * i + d / 2, d, d);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 8.50.51.png (164.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/f824248f-5f2d-4887-a9f5-d609fb09356b.png)

色の生成には成功していますが、どちらかというとくすんだ色の方が目立ちます。
また、色に統一感がないように見えます。
これを解決するために、**HSB 色空間** を使用することが考えられます。

Processing では、`fill` や `stroke` で設定する色はデフォルトで **RGB 色空間** が用いられます。
RGB 色空間では、一つの色を 3 次元のベクトル、すなわち赤色、緑色、青色の 3 つの成分で表します。

以下は横軸に赤色、縦軸に緑色成分をとり、青色成分を時間変化させて描画するプログラムです。

```java
int z = 0;

void setup() {
  size(256, 256);
}

void draw() {
  for (int y = 0; y < height; ++y) {
    for (int x = 0; x < width; ++x) {
      stroke(x, y, z);
      point(x, y);
    }
  }
  z = (z + 16) % 256;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.36.34.png (4.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/028d1449-646a-41c9-9496-74f836d426a1.png)

HSB 色空間では、RGB と同様に一つの色を 3 次元のベクトルで表しますが、それぞれの成分は色相（Hue）、彩度（Saturation）、明度（Brightness）を表します。

Processing では、 `colorMode` 関数を使用することで、HSB 色空間を使用することができます。
`colorMode` 関数は`colorMode(mode)` または `colorMode(mode, max1, max2, max3)` のように呼び出します。
`mode` には定数 `RGB` または `HSB` を渡すことができます。
`max1`、`max2`、`max3` は色の 3 成分の最大値を設定します。

RGB と同様に、彩度を横軸、明度を縦軸、色相を時間軸にとって描画するプログラムを以下に提示します。

```java
int z = 0;

void setup() {
  size(256, 256);
  colorMode(HSB);
}

void draw() {
  for (int y = 0; y < height; ++y) {
    for (int x = 0; x < width; ++x) {
      stroke(z, x, y);
      point(x, y);
    }
  }
  z = (z + 1) % 256;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 8.47.56.png (34.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/96c47d88-e0be-421a-ac75-64b7348e8cfb.png)

HSB 色空間における色彩は、円柱の断面の円の角度として表されます。
以下は色相の変化を円上に可視化したプログラムです。

```java
size(400, 400);
background(255);
colorMode(HSB, 360, 100, 100);
int x0 = width / 2;
int y0 = height / 2;
int r = width / 2;
for (float t = 0; t < TWO_PI; t += 0.001) {
  float h = degrees(t);
  stroke(h, 100, 100);
  line(x0, y0, x0 + r * cos(t), y0 + r * sin(t));
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-08 at 6.44.58.png (110.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/ae48d742-85ea-4b3e-bb5a-004f85c304f1.png)

HSB 色空間において、彩度と明度を固定したまま色相をランダムに決めることで生成される色に統一感を持たせることができます。

```java
size(600, 600);
background(255);
colorMode(HSB, 360, 100, 100);
int n = 10;
int d = width / n;
for (int i = 0; i < n; ++i) {
  for (int j = 0; j < n; ++j) {
    fill(random(360), 100, 100);
    ellipse(d * j + d / 2, d * i + d / 2, d, d);
  }
}
```

![Screen Shot 2019-07-08 at 8.51.39.png (146.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/eef0f36a-5b4a-4773-9f63-e8478b0b713f.png)

HSB は連続的な値の大小表現にも使用することができます。
