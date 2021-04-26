# Euler 法

これまでに引き続き、常微分方程式が以下のように表されているとします。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

ただし、$$x(t)$$ はテイラー展開可能であるとします。

時刻 $$t_1$$ における関数値 $$x(t_1)$$ に対して、時間が $$h$$ 変化したときの関数値 $$x(t_1 + h)$$ について考えます。
$$x(t)$$ の $$t_1$$ のまわりでのテイラー展開を第 2 項まで表すと次式になります。

$$
x(t) = x(t_1) +  (t - t_1) x'(t_1)
$$

これは $$x(t)$$ を近似的に表しています。

そして、$$t = t_1 + h$$ のとき次式が得られます。

$$
x(t_1 + h) = x(t_1) + h f(t_1, x(t_1))
$$

これにより現在の時刻 $$t_1$$ の $$x(t_1)$$ がわかっているとき、時刻が$$h$$変化したときの値$$x(t_1 + h)$$ を近似的に求めることができます。
この式に基づいて、初期値$$x(t_0) = x_0$$ からはじまり、 $$x(t_0), x(t_0 + h), x(t_0 + 2h), \cdots, x(t_0 + nh)$$を求める方法を **Euler 法** と呼びます。

# Euler 法の実装

Euler 法を用いて以下の初期値問題を計算してみます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

以下のプログラムでは、Euler 法によって得られた近似解と解析解をコンソールに表示しています。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const steps = 1000;
  const t0 = 0;
  const tTarget = 1;
  const h = (tTarget - t0) / steps;

  let t1 = t0;
  let x1 = 1;
  for (let i = 0; i < steps; i += 1) {
    const t2 = t1 + h;
    const x2 = x1 + h * dx(t1, x1);
    t1 = t2;
    x1 = x2;
  }
  console.log(x1);
  console.log(exp(-1));
}

function dx(t, xt) {
  return -xt;
}
```

実行結果は以下のようになります。

```console
0.36769542477096373
0.36787944117144233
```

さらにこれを改変して、$$x(t)$$のグラフを$$0 \leq t \leq 1$$、$$0 \leq x \leq 1$$の範囲で幅 300、高さ 300 のウィンドウに描画するプログラムと実行結果は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="jOydozo" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2  3-1-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/jOydozo">
  コンピューティング2  3-1-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 連立微分方程式への Euler 法の適用

連立微分方程式についても、必ずしも一般解が容易に得られるわけではありません。
そのため、前回と同様に数値計算によって連立微分方程式を解くことを考えてみましょう。
まずは前回と同様に Euler 法を使用します。

$$n$$ 個の未知関数 $$x(t) = \left( x_0(t), x_1(t), \cdots, x_{n-1}(t) \right)$$ を次のように表します。

$$
\frac{d}{dt} x_i(t) = f_i (t, x(t)) \qquad i = 0, 1, \cdots, n - 1
$$

Euler 法における更新式は次のように表されます。

$$
x_i (t_1 + h) = x_i (t_1) + h f_i (t_1, x(t_1)) \qquad i = 0, 1, \cdots, n - 1
$$

すなわち、ある時点 $$t_1$$ において、全ての勾配 $$f(t_1, x(t_1))$$ を計算し、その後全ての未知関数を更新します。

実装上の工夫として、勾配を計算する関数 `dx` は `x` の配列を受け取り、勾配の配列 `dxt` に新たな値を書き込むようにします。
（勾配の配列を返す関数にすることもできますが、オブジェクトの生成が頻繁に行われるため実行効率が悪いです。）

先ほどの微分方程式、

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

を Euler 法によって解き、各時点の $$x(t), y(t)$$ の値をコンソールに表示するプログラムは以下のようになります。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const steps = 300;
  const t0 = xLeft;
  const tTarget = xRight;
  const h = (tTarget - t0) / steps;

  let t1 = t0;
  const x1 = [1, 0];
  const d = x1.length;
  const x2 = new Array(d);
  const dx1 = new Array(d);

  for (let i = 0; i < steps; i += 1) {
    const t2 = t1 + h;
    dx(t1, x1, dx1);
    for (let i = 0; i < d; ++i) {
      x2[i] = x1[i] + h * dx1[i];
    }

    console.log(x2[0], x2[1]);

    t1 = t2;
    for (let i = 0; i < d; ++i) {
      x1[i] = x2[i];
    }
  }
}

function dx(t, xt, dxt) {
  dxt[0] = xt[1];
  dxt[1] = -xt[0];
}
```

実行結果は以下のようになります。

```
1 -0.06283185307179587
0.9960521582395643 -0.12566370614359174
0.9881564747186928 -0.1882475090019452
0.976328534891951 -0.25033521143341386
0.9605995096684699 -0.31167974248754665
0.9410160938830372 -0.3720359897398751
0.9176403832382812 -0.43116177468892936
0.8905496899608517 -0.4888188204213035
0.8598362976574118 -0.5447737076940571
0.8256071560982013 -0.5987988156142645
0.7879835168959605 -0.65067324314125
0.7471005112851605 -0.7001837076978539

…

```

さらに、この結果をグラフで表すようにプログラムを変更します。
Euler 法によって得られた数値解を赤色、解析解を青色の線で表します。

プログラムと実行結果は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="OJWdYBB" data-preview="true" data-editable="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2  3-1-2">
  <span>See the Pen <a href="https://codepen.io/likr/pen/OJWdYBB">
  コンピューティング2  3-1-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

$$t$$ が進むにつれて、数値解と解析解のズレが大きくなっていくことが確認できます。

変数 `steps` の値を変えて `h` の値を変化させたときの結果を確認してみましょう。 `h` の値が大きければ（`steps` が小さければ）誤差は大きく、`h` の値が小さければ（`steps` が大きければ）誤差が大きくなることが確認できます。

# 2 階常微分方程式への Euler 法の適用

連立微分方程式を応用することで、高階の常微分方程式も数値的に解くことが可能です。

次のような 2 階常微分方程式を考えます。

$$
\frac{d^2}{dt^2} x(t) + \alpha \frac{d}{dt} x(t) + \beta x(t) = 0
$$

ここで、

$$
\frac{d}{dt} x(t) = y(t)
$$

とおくことで、元の微分方程式は次のような連立微分方程式に書き換えることができます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -\beta x(t) -\alpha y(t)
\end{aligned}
$$

次の初期値問題を考えます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1 \\
\frac{d}{dt} x(0) &= 0
\end{aligned}
$$

これは次の初期値問題に書き換えられます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

なお、これは前節の連立微分方程式と全く同じです。

これを用いて $$x(t)$$ のグラフを表示するプログラムと実行結果は以下の通りです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="oNBmROo" data-preview="true" data-editable="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2  3-1-3">
  <span>See the Pen <a href="https://codepen.io/likr/pen/oNBmROo">
  コンピューティング2  3-1-3</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
