# スカラー場とベクトル場

空間中の任意の点に何らかのスカラー値が定義されているとき、その空間を **スカラー場** と呼びます。
例えば、室内の温度は室内のどこでも観測することができます。
すなわち、温度は室内という空間中のどこにでも定義されており、温度の分布はスカラー場であると言えます。

同じように、空間中の任意の点に何らかのベクトル値が定義されている空間を **ベクトル場** と呼びます。
物理学における電磁場や重力場はベクトル場の一種です。
電磁場では電磁力が、重力場では重力が空間上の任意の点で定義されます。

# 流れ場

**流れ場** は、空間上の任意の点で流速が定義されているようなベクトル場の一種です。
大気や室内の空気の流れ、海の海流などが流れ場として見ることができるでしょう。

具体例を考えてみましょう。
二次元空間上のある点 $$(x, y)$$ の速度 $$(v_x, v_y)$$ が次式で与えられているとします。

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= 2 x \\
v_y = \frac{d}{dt} y &= - \frac{1}{2} y
\end{aligned}
$$

この式により好きな座標 $$(x, y)$$ における流速を知ることができます。

いくつかの点における流速の向きと大きさをプログラムで描画してみましょう。
$$0 \leq x \leq 5$$、$$0 \leq y \leq 5$$ の範囲で $$x$$ と $$y$$ を 0.25 ずつずらした点での流速を `line` で表示します。

実行結果は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="vYxxMrx" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-4-2">
  <span>See the Pen <a href="https://codepen.io/likr/pen/vYxxMrx">
  コンピューティング2 3-4-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

線分の向きが流速の向きを、長さが流速の大きさを表しています。

# 流線

流れのある川の中に、軽くて小さいボールを浮かばれば、ボールは川の流れに沿って移動していくことが想像できるでしょう。
流れ場 $$X$$ において、ある点 $$(x_0, y_0)$$ に粒子を置いたとき、粒子が流れ場に沿って移動する軌道は微分方程式の初期値問題を解くことで得られます。
そのようにして得られた粒子の軌跡を **流線** と呼びます。

先ほどの流れ場

$$
\begin{aligned}
v_x = \frac{d}{dt} x &= 2 x \\
v_y = \frac{d}{dt} y &= - \frac{1}{2} y
\end{aligned}
$$

において、$$(1.0, 4.0)$$ を初期値とした流線を描いてみましょう。
基本的には微分方程式を解くだけです。

流線のイメージとして、軌跡を青色の線でアニメーションさせてみます。
プログラムと実行結果は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="vYxxwOW" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-4-3">
  <span>See the Pen <a href="https://codepen.io/likr/pen/vYxxwOW">
  コンピューティング2 3-4-3</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## ユーザーインタラクション

初期値を変えれば異なる流線が描かれることが確認できるでしょう。
しかし、毎回プログラム中の初期値を変更するのは面倒です。
例えば、マウスをクリックした位置から流線を描けると面白いでしょう。

一つ前のプログラムに以下のコードを加えてみましょう。

```javascript
function mouseClicked() {
  x1[0] = ((xRight - xLeft) * float(mouseX)) / width + xLeft;
  x1[1] = ((yBottom - yTop) * float(mouseY)) / height + yTop;
}
```

`mouseClicked` 関数は、画面中をマウスでクリックされるたびに呼ばれるイベントハンドラです。
描画時とは逆の座標変換を用いて、画面空間の `mouseX` と `mouseY` から計算空間の座標を計算しています。

実行結果は以下のようになります。
描画領域を適当にクリックしてみましょう。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="WNppBOB" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-4-4">
  <span>See the Pen <a href="https://codepen.io/likr/pen/WNppBOB">
  コンピューティング2 3-4-4</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 数値補間

ある流れ場について、微分方程式はわかっていないが、いくつかの点での流速が観測によって得られるような場合があります。
室内の空気の流れを厳密に微分方程式として表すことは難しいですが、何らかの観測装置を使えばある一点での空気の流れを測ることができるでしょう。
そして、観測点から少し離れた点での流速は、観測点での流速と極端に違うということはあまりないでしょう。
つまり、何点かの観測点があれば、観測点の間の観測していない点の流速を大体予測することができます。
このように、離散的な観測値からその間の値を求めることを **補間** と言います。

補間の方法は数多く存在しています。
以下では簡単な 1 次元と 2 次元の線形補間について説明します。

## 1 次元の線形補間

1 次元上の点 $$x_a$$ における観測値を $$v_a$$、点 $$x_b$$ における観測値を $$v_b$$ とします。
ある点 $$x$$ （$$ x_a \leq x \leq x_b$$ とする）の値 $$v$$ は次の式で表されます。

$$
v = \frac{x - x_a}{x_b - x_a} (v_b - v_a) + v_a
$$

以下のように整理してもいいでしょう。

$$
v = \frac{(x - x_a) v_b - (x - x_b) v_a}{x_b - x_a}
$$

$$v$$ がスカラー値であってもベクトル値であっても同じ式になります。
$$v$$ をスカラー値として見れば、この式は座標変換と一致します。
具体的に計算する場合は、ベクトル値の各要素をスカラー値として補間することでベクトル値の補間が可能です。

## 2 次元の線形補間

2 次元空間状の 4 点 $$(x_a, y_a), (x_b, y_b), (x_c, y_c), (x_d, y_d)$$ を考えます。
単純な場合として、ここでは 4 点が長方形の頂点となっている場合を考えます。
すなわち、$$x_a = x_c$$、$$x_b = x_d$$、$$y_a = y_b$$、$$y_c = y_d$$ とします。
また、それぞれの点の値を $$v_a, v_b, v_c, v_d$$ とします。
長方形内の点 $$(x, y)$$ の値 $$v$$ は次のように補間できます。

$$
\begin{aligned}
v &= \frac{(y - y_a) v_2 - (y - y_c) v_1}{y_c - y_a} \\
v_1 &= \frac{(x - x_a) v_b - (x - x_b) v_a}{x_b - x_a} \\
v_2 &= \frac{(x - x_c) v_d - (x - x_d) v_c}{x_d - x_c}
\end{aligned}
$$

考え方としては、まず 2 点$$(x_a, y_a), (x_b, y_b)$$を結ぶ直前上で 1 次元の線形補間を行い $$v_1$$ を得ます。
同様に、$$(x_c, y_c), (x_d, y_d)$$の間の $$v_2$$ を得ます。
今度は、$$y_a, y_c$$の間を $$v_1$$ と $$v_2$$ で線形補間することで $$v$$ を得ます。

## 補間を用いた流線の計算

それでは、微分方程式の代わりに、2 次元正方形格子上の観測点での流速が与えられたときの流線の描画を行ってみましょう。

$$i = \{ 0, 1, 2, 3, 4 \}$$、$$j = \{ 0, 1, 2, 3, 4 \}$$ について、 $$x = j, y = i$$ の点の流速が以下の配列の `i * 5 + j` 番目の要素で表されているとします。

```javascript
const vx = [
  [-2.0, -1.0, 0.0, 1.0, 2.0],
  [-2.0, -1.0, 0.0, 1.0, 2.0],
  [-2.0, -1.0, 0.0, 1.0, 2.0],
  [-2.0, -1.0, 0.0, 1.0, 2.0],
  [-2.0, -1.0, 0.0, 1.0, 2.0],
];
const vy = [
  [2.0, 2.0, 2.0, 2.0, 2.0],
  [1.0, 1.0, 1.0, 1.0, 1.0],
  [0.0, 0.0, 0.0, 0.0, 0.0],
  [-1.0, -1.0, -1.0, -1.0, -1.0],
  [-2.0, -2.0, -2.0, -2.0, -2.0],
];
```

関数 `dx` は、現在の座標 $$(x, y)$$ から線形補間を用いて流速を計算する必要があります。
はじめに $$(x, y)$$を含むような観測点 4 つを見つけます。
すなわち、 $$i = \{ 0, 1, 2, 3 \}$$、$$j = \{ 0, 1, 2, 3 \}$$ について、$$j \leq x < j + 1$$、$$i \leq y < i + 1$$ となるような $$(j, i)$$ を見つける必要があります。
そして、$$(j, i), (j, i + 1), (j + 1, i), (j + 1, i + 1)$$ の 4 点を用いて二次元の線形補間を `vx` と `vy` それぞれで行います。
実装例は以下のようになります。

```javascript
function dx(t, xt, dxt) {
  const vx = [
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
    [-2.0, -1.0, 0.0, 1.0, 2.0],
  ];
  const vy = [
    [2.0, 2.0, 2.0, 2.0, 2.0],
    [1.0, 1.0, 1.0, 1.0, 1.0],
    [0.0, 0.0, 0.0, 0.0, 0.0],
    [-1.0, -1.0, -1.0, -1.0, -1.0],
    [-2.0, -2.0, -2.0, -2.0, -2.0],
  ];
  const i = constrain(int(xt[1]), 0, 3);
  const j = constrain(int(xt[0]), 0, 3);
  const vx1 = (xt[0] - i) * vx[i + 1][j] - (xt[0] - (i + 1)) * vx[i][j];
  const vx2 = (xt[0] - i) * vx[i + 1][j + 1] - (xt[0] - (i + 1)) * vx[i][j + 1];
  dxt[0] = (xt[1] - j) * vx2 - (xt[1] - (j + 1)) * vx1;
  const vy1 = (xt[0] - i) * vy[i + 1][j] - (xt[0] - (i + 1)) * vy[i][j];
  const vy2 = (xt[0] - i) * vy[i + 1][j + 1] - (xt[0] - (i + 1)) * vy[i][j + 1];
  dxt[1] = (xt[1] - j) * vy2 - (xt[1] - (j + 1)) * vy1;
}
```

プログラムと実行結果は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="MWppdxj" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-4-5">
  <span>See the Pen <a href="https://codepen.io/likr/pen/MWppdxj">
  コンピューティング2 3-4-5</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
