# グラフ描画

グラフ理論のグラフはノードの集合とリンクの集合によって表されます。
グラフに含まれてるノード同士がリンクによって接続されています。

グラフはそのようなつながりの関係のみを持ちますが、グラフがどのような構造を持っているかを視覚的に表現したい場合には、2 次元や 3 次元空間でノードとリンクを描画する必要があります。
直接的に、画面にノードを描画し、ノード間につながるリンクを線でつなぐような表現を **ノードリンク図** と呼びます。
ノードリンク図を描くためには、ノードをどこに配置するかどうかを決定する必要があります。
このような、ノード配置を行う方法を **グラフ描画** （Graph Drawing）と呼びます。
グラフのどのような特徴を強調するかに応じて、様々なグラフ描画手法が考えられています。
グラフ構造の可視化は、世の中に存在する様々なネットワーク構造を持ったデータの理解に役立てることができます。

グラフ描画の一つのアプローチとして、ノード間に働く力をシミュレーションすることでノードの座標を決定する方法があります。
このようなアプローチは **Force-directed アルゴリズム** と呼ばれます。
Force-directed アルゴリズムのもっとも基本的な方法の一つは、ノード間をつなぐリンクをバネと見立ててその運動をシミュレーションすることです。
このような方法は **バネモデル** とも呼ばれます。
バネモデルを用いたグラフ描画としては Eades の方法が有名です。
しかし、本講義では Verlet 法により容易に実装できるようにバネモデルをアレンジしています。

バネモデルのシミュレーションの前に、グラフのプログラム上での表現を考えましょう。
ノードについては、ノードの個数分の座標と速度を配列として保持しておく必要があります。
これは複数の物体の運動と同様になります。
リンクについては、どのノードとどのノードがつながっているか、一方のノードの添字 `source` ともう一方のノードの添字 `target` をそれぞれ整数型の配列で表すことができます。

次にバネモデルのシミュレーションについて考えます。
まずはじめに、ノードの初期座標を適当に与えておく必要があります。
これは`random`関数を用いてランダムに決めることが可能です。
次に、全てのリンクに対してフックの法則に従う力の計算を行いますが、それだけでは座標がうまく収束しません。
そのため、空気抵抗の影響を与えることでノードの座標が静止状態になるまで繰り返しを行います。
また、いずれのノードも固定されていないため、ノードの位置が表示可能な画面の領域から遠く離れていく場合も考えられます。
これを回避するために、速度の更新の際に、全てのノードの重心が画面中央にくるように全ての座標の平行移動を行います。

完全グラフ $$K_3$$ のバネモデルによって可視化するプログラムは以下のようになります。

```java
int n = 3;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

int m = 3;
int[] source = {0, 0, 1};
int[] target = {1, 2, 2};

void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 200);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = dist(x[s], y[s], x[t], y[t]);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}
```

実行結果は以下の通りです。

![Screen Shot 2019-07-08 at 12.25.58.png (21.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/b8e071e9-a34f-43ef-a067-d93e21a24cc2.png)

リンクを以下のように変更してみます。

```java
int m = 7;
int[] source = {0, 0, 1, 1, 2, 3, 3};
int[] target = {1, 4, 2, 4, 3, 4, 5};
```

うまくいけば以下のような配置になります。

![Screen Shot 2019-07-08 at 12.30.16.png (32.1 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/72b9b25f-a4d5-450f-b8b8-04950d056864.png)

一方で、ノードの初期位置によっては以下のように重なりが生じたりします。

![Screen Shot 2019-07-08 at 12.31.49.png (26.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/08/28750/7c48044f-b676-4683-933d-1d6c58d35e61.png)

# ネットワーク可視化へのクーロン力の導入

前回、バネの力だけではネットワークのノードに重なりが生じて、ノード配置がうまくいかない場合がありました。
クーロン力を用いてノード間の反発力を計算することでノードの重なりを回避してみましょう。
ノードが全て同じ符号の電荷を持っているとします。
i 番目のノードには、j 番目のノードから、電荷に比例して距離の 2 乗に反比例する加速度が働きます。

```java
int n = 6;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float r = 25;

int m = 7;
int[] source = {0, 0, 1, 1, 2, 3, 3};
int[] target = {1, 4, 2, 4, 3, 4, 5};

float minDistance = 1;

void setup() {
  size(600, 600);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 10);
  applyRepulsiveForce(10);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 4.56.34.png (28.9 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/b50be7ec-9ba2-4e4f-a8a4-824684c4725d.png)

ノード間の反発力を導入することでノードを均等に配置できるようになりました。

ネットワーク可視化において、ノード間の反発力が最初に導入されたのは **Fruchterman-Reingold のアルゴリズム** です。

# グラフの生成

ランダムに生成したグラフを Force-directed アルゴリズムで可視化してみます。
以下のプログラムでは、10 頂点のグラフにおいて、全ての頂点ペアが 0.5 の確率でリンクを持つようにランダムにグラフを生成しています。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

void setup() {
  size(600, 600);
  initializeGraph(10);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int k) {
  n = k;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.5) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.001, 10);
  applyRepulsiveForce(10);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

閉路を持たないグラフを木と呼びます。
以下は 100 ノードの木をランダムに生成し、Force-directed アルゴリズムによって可視化するプログラムです。

```java
int n, m;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] source;
int[] target;

float r = 5;
float minDistance = 10;

void setup() {
  size(600, 600);
  initializeGraph(100);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int k) {
  n = k;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = n - 1;
  source = new int[m];
  target = new int[m];
  for (int i = 0; i < m; ++i) {
    source[i] = i;
    target[i] = int(random(i + 1, n));
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.1, 10);
  applyRepulsiveForce(1);
  applyResistanceForce(0.01);
  applyCentering();
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 12.43.44.png (38.0 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/de456f4f-2642-44e6-a7bd-c289f049d19c.png)

ここで、以下の 2 つの問題に気づきます。

- ノード数が多いとノード座標が収束しにくい
- グラフが非連結の場合に、それぞれの連結成分が離れていく

次にこれらの解決方法を考えていきます。

# アニーリング（焼きなまし）

空気抵抗力を受けることでノードの座標は収束していきます。
そのため、空気抵抗係数を大きくすればノードは速く停止します。
しかし、最初から空気抵抗係数が大きければノードが綺麗に配置される前に停止してしまいます。
そこで、最初は空気抵抗係数を小さくして、徐々に大きくしていく方法をとります。
このような方法を **アニーリング** と呼びます。

空気抵抗係数を徐々に大きくしていくには様々な方法が考えられます。
ここでは、係数 $$0 \leq \alpha \leq 1$$ に対して空気抵抗係数を $$k = 1 - \alpha$$ とします。
そして、$$\alpha = 1$$ からはじまり、 $$\alpha \leftarrow e \alpha \quad (0 < e < 1)$$ と更新していくことで空気抵抗係数を大きくしていきます。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(20);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.3) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(255);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(50);
  applyResistanceForce(1 - alpha);
  applyCentering();
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCentering() {
  float cx = 0;
  float cy = 0;
  for (int i = 0; i < n; ++i) {
    cx += x[i];
    cy += y[i];
  }
  cx /= n;
  cy /= n;
  for (int i = 0; i < n; ++i) {
    x[i] += -cx + width / 2;
    y[i] += -cy + height / 2;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

# ノードの中心化

以下のように画面の中心に引き寄せる力をかけることで、非連結なグラフにおいても画面内に収めることができます。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(40);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.01) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
}

void drawObjects() {
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    fill(0);
    ellipse(x[i], y[i], 2 * r, 2 * r);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(200);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

![Screen Shot 2019-07-15 at 12.47.57.png (58.8 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/02010e08-2410-4664-8dc4-08aa908f89d1.png)

# 視覚表現の工夫

ネットワークのグラフやノードの持つ情報を、視覚的表現と対応づけることで、それらの情報の可視化が可能です。

以下はノードの次数を計算し、次数が低いノードは青色、高いノードは赤色でその間を連続的に色相を変化させています。
色相を 0 から 360 の範囲で表すとき、青色の色相は 240、赤色の色相は 0、その中間の緑色の色相は 120 です。

```java
int n;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] degree;

int m;
int[] source;
int[] target;

float r = 25;
float minDistance = 10;
int maxDegree;

float alpha = 1;
float decay = 0.99;

void setup() {
  size(600, 600);
  initializeGraph(40);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  updateVelocity();
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  degree = new int[n];
  m = 0;
  source = new int[n * n];
  target = new int[n * n];
  for (int i = 0; i < n; ++i) {
    for (int j = i + 1; j < n; ++j) {
      if (random(1) < 0.05) {
        source[m] = i;
        target[m] = j;
        m += 1;
      }
    }
  }
  for (int i = 0; i < m; ++i) {
    degree[source[i]] += 1;
    degree[target[i]] += 1;
  }
  for (int i = 0; i < n; ++i) {
    if (degree[i] > maxDegree) {
      maxDegree = degree[i];
    }
  }
}

void drawObjects() {
  colorMode(RGB, 255, 255, 255);
  background(255);
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    fill((1 - float(degree[i]) / maxDegree) * 240, 100, 100);
    ellipse(x[i], y[i], 2 * r, 2 * r);
  colorMode(RGB, 255, 255, 255);
    fill(0);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(i, x[i], y[i]);
  }
}

void updatePosition() {
  for (int i = 0; i < n; ++i) {
    x[i] += vx[i];
    y[i] += vy[i];
  }
}

void updateVelocity() {
  applySpringForce(0.01, 50);
  applyRepulsiveForce(200);
  applyCenteringForce(0.1);
  applyResistanceForce(1 - alpha);
  alpha *= decay;
}

void applySpringForce(float k, float L) {
  for (int i = 0; i < m; ++i) {
    int s = source[i];
    int t = target[i];
    float d = distance(s, t);
    float theta = atan2(y[s] - y[t], x[s] - x[t]);
    float w = k * (L - d);
    vx[s] += w * cos(theta);
    vy[s] += w * sin(theta);
    vx[t] -= w * cos(theta);
    vy[t] -= w * sin(theta);
  }
}

void applyResistanceForce(float k) {
  for (int i = 0; i < n; ++i) {
    vx[i] += -k * vx[i];
    vy[i] += -k * vy[i];
  }
}

void applyRepulsiveForce(float q) {
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
      if (i == j) {
        continue;
      }
      float d = distance(i, j);
      float w = -q / (d * d);
      vx[i] += (x[j] - x[i]) * w;
      vy[i] += (y[j] - y[i]) * w;
    }
  }
}

void applyCenteringForce(float strength) {
  for (int i = 0; i < n; ++i) {
    vx[i] += (width / 2 - x[i]) * strength;
    vy[i] += (height / 2 - y[i]) * strength;
  }
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-15 at 7.16.03.png (118.5 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/15/28750/77b0c342-2d37-480e-8265-42f24f1665f2.png)
