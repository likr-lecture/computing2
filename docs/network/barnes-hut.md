# 多体計算の近似

粒子間の重力やクーロン力の計算には、全ての粒子のペアでそれらの間に働く力を計算する必要がありました。
そのため、粒子の数を $$n$$ とすると、その計算量は $$O(n^2)$$ となります。
計算量を減らすための近似について考えてみましょう。

具体的にクーロン力で考えます。
$$i$$ 番目の粒子が他の粒子群 $$P$$ から受けるクーロン力は以下のようになります。

$$
F = k q_i \sum_{P}^j \frac{q_j}{(x_i - x_j)^2 + (y_i - y_j)^2}
$$

他の粒子が $$i$$ 番目の粒子から離れたところにかたまって位置している場合を考えます。
このとき、他の粒子の塊をそれらの重心 $$(\bar{x}, \bar{y})$$ に位置し、電荷が粒子の総和 $$Q$$ を持つような一つの粒子と見做すことができるでしょう。
その重心の粒子から受けるクーロン力は以下のように表されます。

$$
F = k q_i \frac{Q}{(x_i - \bar{x})^2 + (y_i - \bar{y})^2}
$$

すなわち、ある粒子から遠く離れた粒子の塊を効率的に見つけることができれば、粒子間の重力やクーロン力をより少ない計算量で近似できます。
次は、それを実現するためのデータ構造について考えてみます。

# Barnes-Hut 木

それぞれのノードが高々 4 つの子を持つ根付き木を **4 分木** と呼びます。
ある長方形領域を左上、左下、右上、右下の 4 つに再帰的に分割し、4 分木に割り当てることを考えます。
さらに、1 つの粒子と 4 分木の葉を対応づけます。
すなわち、全ての粒子が 4 分木の葉に割り当てられるように、粒子の存在する領域を再帰的に分割していきます。
（ただし全く同じ座標に複数の粒子が存在する場合は、葉が複数の粒子を持つことを許します。）
このような 4 分木を **Barnes-Hut 木** と呼びます。
Barnes-Hut 木の葉でないノードにおいて、その子孫に含まれる粒子の重心を格納しておくことで、ある領域に存在する粒子の重心を効率よく求めることができます。

Barnes-Hut 木を構築するアルゴリズムを考えます。
既にある Barnes-Hut 木に新たに粒子 $$p$$ を追加することを考えてみましょう。
Barnes-Hut 木の根が葉である場合とそうでない場合を分けて考えます。
根が葉でない場合、粒子 $$p$$ の座標が根の領域の左上、左下、右上、右下のどこに位置するかを判断し、その部分領域に対応する部分木に粒子 $$p$$ を追加します。
根が葉の場合、根が既に持っている粒子 $$q$$ と新たな粒子 $$p$$ を、それぞれが位置する領域に対応する子ノードを作成して追加します。
以上の手順を部分木に対して再帰的に行うことで、Barnes-Hut 木に粒子 $$p$$ を追加することができます。
そのため、1 粒子のみを持った Barnes-Hut 木から始まり、全ての粒子を順番に木に追加していくことで全ての粒子を含んだ Barnes-Hut 木を構築することができます。

粒子数を $$n$$ とし、空間中に偏りなく粒子が存在している場合は、すなわち、Barnes-Hut 木が完全 4 分木になっている場合を考えます。
このとき Barnes-Hut 木からある座標に対応する葉を見つけるための計算量は $$O(\log n)$$ となります。
すなわち、全ての粒子を含んだ Barnes-Hut 木を構築するのに必要な最良時の計算量は $$O(n \log n)$$ となります。

以下は、画面中にランダムに生成した粒子を格納した Barnes-Hut 木を構築し、その領域を可視化するプログラムです。
再帰的なデータ構造を表すためにクラスを使用しています。

```java
class Point {
  float x;
  float y;

  Point (float x, float y) {
    this.x = x;
    this.y = y;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

Point[] points = new Point[100];
QuadTree root;

void setup() {
  size(600, 600);
  root = new QuadTree(0, 0, width - 1, height - 1);
  for (int i = 0; i < points.length; ++i) {
    println(i);
    points[i] = new Point(random(width), random(height));
    root.addPoint(points[i]);
  }
  for (int i = 0; i < points.length; ++i) {
    root.addPoint(points[i]);
  }
}

void draw() {
  background(255);
  drawTree(root);
  for (int i = 0; i < points.length; ++i) {
    fill(0);
    noStroke();
    ellipse(points[i].x, points[i].y, 5, 5);
  }
}

void drawTree(QuadTree tree) {
  if (tree.isLeaf()) {
    fill(200, 200, 200);
  } else {
    noFill();
  }
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  for (QuadTree child : tree.children()) {
    drawTree(child);
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-22 at 9.29.48.png (26.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/c9f8510b-040c-4744-96f3-01d21d361e80.png)

葉の領域は灰色で塗りつぶしています。
葉と一つの粒子が対応していることが確認できます。

# Barnes-Hut アルゴリズム

次に、Barnes-Hut 木を用いて粒子間にはたらく力を粒子群の重心によって近似できる条件を考えてみましょう。
粒子 $$p$$ と粒子群の重心 $$gc$$ は十分離れている必要があります。
閾値を $$\lambda$$ 、 $$p$$ と $$gc$$ の距離を $$d$$、$$gc$$ を持つ部分木の面積を $$A$$ とします。
ここでは近似の条件を、以下の式で表します。

$$
\lambda^2  d^2 > A
$$

これは、部分木の幅と高さの幾何平均 $$\sqrt{A}$$ と $$d$$ との比が閾値 $$\lambda$$ を超えたときを意味しています。
Barnes-Hut 木を用いて多体問題の計算を近似する方法を **Barnes-Hut アルゴリズム** と呼びます。

以下は (100, 100) に存在する粒子にかかる力の計算の様子を可視化するプログラムです。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

Point[] points = new Point[100];
QuadTree root;
float threshold = 0.81;

void setup() {
  size(600, 600);
  root = new QuadTree(0, 0, width - 1, height - 1);
  for (int i = 0; i < points.length; ++i) {
    points[i] = new Point(random(width), random(height), 1);
    root.addPoint(points[i]);
  }
  for (int i = 0; i < points.length; ++i) {
    root.addPoint(points[i]);
  }
}

void draw() {
  background(255);
  Point p = new Point(100, 100, 1);
  drawGravityCenter(p, root);
  for (int i = 0; i < points.length; ++i) {
    fill(0);
    noStroke();
    ellipse(points[i].x, points[i].y, 5, 5);
  }
  fill(255, 0, 0);
  ellipse(p.x, p.y, 10, 10);
}

void drawGravityCenter(Point p, QuadTree tree) {
  noFill();
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  Point gc = tree.gravityCenter();
  float d = dist(p.x, p.y, gc.x, gc.y);
  if (tree.isLeaf() || threshold * d * d > tree.boxWidth * tree.boxHeight) {
    fill(0, 0, 255);
    noStroke();
    ellipse(gc.x, gc.y, 10, 10);
    stroke(0, 0, 255);
    line(p.x, p.y, gc.x, gc.y);
  } else {
    for (QuadTree child : tree.children()) {
      drawGravityCenter(p, child);
    }
  }
}
```

実行結果は以下のようになります。

![Screen Shot 2019-07-22 at 10.11.38.png (43.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/242ced83-e266-42f7-8fa1-0e9ac905c389.png)

(100, 100) から遠いところでは、大きな領域の部分木の重心で力の計算が近似できることを確認できます。

それでは、速度 Verlet 法に Barnes-Hut アルゴリズムを取り入れて計算をしてみましょう。
以下のプログラムは赤色で表した負電荷と青色で表した正電荷の運動のシミュレーションです。
負電荷にかかるクーロン力を上記のプログラムと同様に可視化しています。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

int n = 100;
float[] x = new float[n];
float[] y = new float[n];
float[] vx = new float[n];
float[] vy = new float[n];
float[] q = new float[n];
float r = 15;

float minDistance = 10;

void setup() {
  size(600, 600);
  background(255);

  x[0] = width / 2;
  y[0] = height / 2;
  q[0] = -5;

  for (int i = 1; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
    q[i] = 0.1;
  }
}

void draw() {
  drawObjects(false);
  updatePosition();
  updateVelocity();
}

void drawObjects(boolean showMotion) {
  float alpha;
  if (!showMotion) {
    background(255);
    alpha = 255;
  } else {
    alpha = 10;
  }
  noStroke();
  for (int i = 0; i < n; ++i) {
    if (q[i] < 0) {
      fill(255, 0, 0, alpha);
    } else {
      fill(0, 0, 255, alpha);
    }
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
  applyRepulsiveForce(1);
}

void applyRepulsiveForce(float k, QuadTree tree, int i) {
  Point gc = tree.gravityCenter();
  float d = dist(x[i], y[i], gc.x, gc.y);
  if (d < minDistance) {
    return;
  }
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    float w = -k * q[i] * gc.value / (d * d);
    vx[i] += (gc.x - x[i]) * w;
    vy[i] += (gc.y - y[i]) * w;
  } else {
    for (QuadTree child : tree.children()) {
      applyRepulsiveForce(k, child, i);
    }
  }
}

void applyRepulsiveForce(float k) {
  float left = x[0];
  float right = x[0];
  float top = y[0];
  float bottom = y[0];
  for (int i = 1; i < n; ++i) {
    if (x[i] < left) {
      left = x[i];
    }
    if (x[i] > right) {
      right = x[i];
    }
    if (y[i] < top) {
      top = y[i];
    }
    if (y[i] > bottom) {
      bottom = y[i];
    }
  }
  float size = right - left > bottom - top ? right - left : bottom - top;
  QuadTree root = new QuadTree(left, top, size, size);
  for (int i = 0; i < n; ++i) {
    root.addPoint(new Point(x[i], y[i], q[i]));
  }
  for (int i = 0; i < n; ++i) {
    applyRepulsiveForce(k, root, i);
  }
  drawTree(root);
}

float distance(int i, int j) {
  float d = dist(x[i], y[i], x[j], y[j]);
  if (d < minDistance) {
    d = minDistance;
  }
  return d;
}

void drawTree(QuadTree tree) {
  noFill();
  stroke(255, 0, 0);
  rect(tree.boxX, tree.boxY, tree.boxWidth, tree.boxHeight);
  Point gc = tree.gravityCenter();
  float d = dist(x[0], y[0], gc.x, gc.y);
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    fill(0, 0, 255);
    noStroke();
    ellipse(gc.x, gc.y, 10, 10);
    stroke(0, 0, 255);
    line(x[0], y[0], gc.x, gc.y);
  } else {
    for (QuadTree child : tree.children()) {
      drawTree(child);
    }
  }
}
```

![Screen Shot 2019-07-22 at 10.48.39.png (86.7 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/d080418c-ca85-4a4f-982b-f19c7ba04db9.png)

# ネットワーク可視化への Barnes-Hut アルゴリズムの導入

Verlet 法によるネットワーク可視化において、ノード間の反発力の計算に Barnes-Hut アルゴリズムを導入しましょう。
プログラムの全体は以下のようになります。

```java
class Point {
  float x;
  float y;
  float value;

  Point (float x, float y, float value) {
    this.x = x;
    this.y = y;
    this.value = value;
  }
}

class QuadTree {
  float value;
  float boxX;
  float boxY;
  float boxWidth;
  float boxHeight;
  QuadTree topLeft;
  QuadTree topRight;
  QuadTree bottomLeft;
  QuadTree bottomRight;
  Point point;
  int n;
  Point gc;

  QuadTree (float x, float y, float w, float h) {
    boxX = x;
    boxY = y;
    boxWidth = w;
    boxHeight = h;
  }

  void addPoint(Point point) {
    addPoint(point, 1);
  }

  void addPoint(Point point, int n) {
    if (isLeaf() && this.point == null) {
      this.point = point;
      this.n = n;
      return;
    }
    if (this.point != null && this.point.x == point.x && this.point.y == point.y) {
      this.n += n;
      return;
    }
    if (point.x < boxX + boxWidth / 2) {
      if (point.y < boxY + boxHeight / 2) {
        if (topLeft == null) {
          topLeft = new QuadTree(boxX, boxY, boxWidth / 2, boxHeight / 2);
        }
        topLeft.addPoint(point);
      } else {
        if (bottomLeft == null) {
          bottomLeft = new QuadTree(boxX, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomLeft.addPoint(point);
      }
    } else {
      if (point.y < boxY + boxHeight / 2) {
        if (topRight == null) {
          topRight = new QuadTree(boxX + boxWidth / 2, boxY, boxWidth / 2, boxHeight / 2);
        }
        topRight.addPoint(point);
      } else {
        if (bottomRight == null) {
          bottomRight = new QuadTree(boxX + boxWidth / 2, boxY + boxHeight / 2, boxWidth / 2, boxHeight / 2);
        }
        bottomRight.addPoint(point);
      }
    }
    if (this.point != null) {
      Point p = this.point;
      this.point = null;
      addPoint(p, this.n);
      this.n = 0;
    }
  }

  boolean isLeaf() {
    return topLeft == null && bottomLeft == null && topRight == null && bottomRight == null;
  }

  Point gravityCenter() {
    if (gc == null) {
      float cx = 0;
      float cy = 0;
      float value = 0;
      int count = 0;
      if (point != null) {
        cx = point.x;
        cy = point.y;
        value = point.value * n;
        count += 1;
      }
      for (QuadTree child : children()) {
        Point p = child.gravityCenter();
        cx += p.x;
        cy += p.y;
        value += p.value;
        count += 1;
      }
      gc = new Point(cx / count, cy / count, value);
    }
    return gc;
  }

  ArrayList<QuadTree> children() {
    ArrayList<QuadTree> list = new ArrayList<QuadTree>();
    if (topLeft != null) {
      list.add(topLeft);
    }
    if (bottomLeft != null) {
      list.add(bottomLeft);
    }
    if (topRight != null) {
      list.add(topRight);
    }
    if (bottomRight != null) {
      list.add(bottomRight);
    }
    return list;
  }
}

int n;
float[] x;
float[] y;
float[] vx;
float[] vy;
int[] degree;

int m;
int[] source;
int[] target;

float r = 5;
float minDistance = 10;
int maxDegree;

float alpha = 1;
float decay = 0.995;
float totalTime = 0;
int count = 0;

void setup() {
  size(600, 600);
  randomSeed(0);
  initializeGraph(250);
  for (int i = 0; i < n; ++i) {
    x[i] = random(width);
    y[i] = random(height);
  }
}

void draw() {
  drawObjects();
  updatePosition();
  float start = millis();
  updateVelocity();
  float end = millis();
  totalTime += end - start;
  count += 1;
  if (count == 100) {
    println(totalTime / count);
  }
}

void initializeGraph(int nodeCount) {
  n = nodeCount;
  x = new float[n];
  y = new float[n];
  vx = new float[n];
  vy = new float[n];
  degree = new int[n];
  m = 0;
  source = new int[n - 1];
  target = new int[n - 1];
  for (int i = 0; i < n - 1; ++i) {
    if (random(1) < 0.9) {
      source[m] = i;
      target[m] = int(random(i + 1, n));
      m += 1;
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
    stroke(0);
    line(x[s], y[s], x[t], y[t]);
  }
  for (int i = 0; i < n; ++i) {
    colorMode(HSB, 360, 100, 100);
    noStroke();
    fill((1 - float(degree[i]) / maxDegree) * 240, 100, 100);
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
  applyRepulsiveForce(30, true);
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

void applyRepulsiveForce(float q, QuadTree tree, int i) {
  Point gc = tree.gravityCenter();
  float d = dist(x[i], y[i], gc.x, gc.y);
  if (d < minDistance) {
    return;
  }
  if (tree.isLeaf() || 0.81 * d * d > tree.boxWidth * tree.boxHeight) {
    float w = -gc.value / (d * d);
    vx[i] += (gc.x - x[i]) * w;
    vy[i] += (gc.y - y[i]) * w;
  } else {
    for (QuadTree child : tree.children()) {
      applyRepulsiveForce(q, child, i);
    }
  }
}

void applyRepulsiveForce(float q, boolean bh) {
  if (!bh) {
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
    return;
  }
  float left = x[0];
  float right = x[0];
  float top = y[0];
  float bottom = y[0];
  for (int i = 1; i < n; ++i) {
    if (x[i] < left) {
      left = x[i];
    }
    if (x[i] > right) {
      right = x[i];
    }
    if (y[i] < top) {
      top = y[i];
    }
    if (y[i] > bottom) {
      bottom = y[i];
    }
  }
  QuadTree root = new QuadTree(left, top, right - left, bottom - top);
  for (int i = 0; i < n; ++i) {
    root.addPoint(new Point(x[i], y[i], q));
  }
  for (int i = 0; i < n; ++i) {
    applyRepulsiveForce(q, root, i);
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

以下は実行結果です。
可視化結果自体は前回と大きく変わりません。

![Screen Shot 2019-07-22 at 12.06.22.png (89.3 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/a630e1d2-b157-488e-a2ea-d2c4f68a3fba.png)

以下は、グラフのノード数 $$n$$ を {250, 500, 1000, 2000, 4000} としたときの計算時間の比較です。
赤色が Barnes-Hut アルゴリズム（barnes-hut）、青色がそうでない場合（all pairs）を表しています。

![chart (3).png (12.4 kB)](https://img.esa.io/uploads/production/attachments/8704/2019/07/22/28750/2e22fc02-5202-4d5b-ac30-cde2931edf7e.png)

|      | all pairs | barnes-hut |
| ---- | --------- | ---------- |
| 250  | 1.08      | 1.78       |
| 500  | 2.87      | 3.05       |
| 1000 | 10.01     | 5.59       |
| 2000 | 39.68     | 10.48      |
| 4000 | 161.47    | 23.95      |

all pairs では、ノード数が倍になったときに計算時間が約 4 倍になっています。
これは、all pairs の計算量が $$O(n^2)$$ であるためです。
一方で、barnes-hut では、ノード数が倍になっても計算時間はほぼ倍程度で収まっています。
Barnes-Hut アルゴリズムの計算量はおおよそ $$O(n \log n)$$ となっています。
ただし、Barnes-Hut 木を構築するためのオーバーヘッドがあるため、ノード数が少ないと Barnes-Hut アルゴリズムの方が遅くなる場合もあります。
