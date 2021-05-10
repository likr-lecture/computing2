# Runge-Kutta 法

これまでのような解が振動する場合には、Euler 法では十分な精度の解が得られないことが確認できました。
前回は、Taylor 展開を用いて Euler 法を導出しました。
これを一般化し、より高次の項を使用する方法が **Runge-Kutta 法** です。
Runge-Kutta 法の導出や数理的な性質については次回に回しますが、一般的によく使われる 4 次の Runge-Kutta 法では、以下の式によって関数値を更新します。

$$
\begin{aligned}
x(t_1 + h) &= x(t_1) + h \frac{f_1 + 2 f_2 + 2 f_3 + f_4}{6} \\
f_1 &= f \left(t_1, x(t_1) \right) \\
f_2 &= f \left(t_1 + \frac{h}{2}, x(t_1) + \frac{h}{2} f_1 \right) \\
f_3 &= f \left(t_1 + \frac{h}{2}, x(t_1) + \frac{h}{2} f_2 \right) \\
f_4 &= f \left(t_1 + h, x(t_1) + h f_3 \right)
\end{aligned}
$$

この更新式に基づいて前述の連立微分方程式を解いてコンソールに表示するプログラムは以下のようになります。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const steps = 100;
  const t0 = 0;
  const tTarget = 2 * PI;
  const h = (tTarget - t0) / steps;

  let t1 = t0;
  const d = 2;
  const x1 = [1, 0];
  const x2 = new Array(2);

  const tmp = new Array(d);
  const f1 = new Array(d);
  const f2 = new Array(d);
  const f3 = new Array(d);
  const f4 = new Array(d);

  for (let i = 0; i < steps; ++i) {
    const t2 = t1 + h;
    dx(t1, x1, f1);
    for (let j = 0; j < d; ++j) {
      tmp[j] = x1[j] + (h * f1[j]) / 2;
    }
    dx(t1, tmp, f2);
    for (let j = 0; j < d; ++j) {
      tmp[j] = x1[j] + (h * f2[j]) / 2;
    }
    dx(t1, tmp, f3);
    for (let j = 0; j < d; ++j) {
      tmp[j] = x1[j] + h * f3[j];
    }
    dx(t1, tmp, f4);

    for (let j = 0; j < d; ++j) {
      x2[j] = x1[j] + (h * (f1[j] + 2 * f2[j] + 2 * f3[j] + f4[j])) / 6;
    }

    console.log(x2[0], x2[1]);

    t1 = t2;
    for (let j = 0; j < d; ++j) {
      x1[j] = x2[j];
    }
  }
}

function dx(t, xt, dxt) {
  dxt[0] = xt[1];
  dxt[1] = -xt[0];
}
```

Runge-Kutta 法での更新量を決定するために`f1`、`f2`、`f3`、`f4`、`tmp`の 4 つの配列を用意しています。
$$f_i$$ を計算するたびに 1 回 `dx` を呼び出しているので、1 回の更新につき合計で 4 回 `dx` を呼び出します。。
`dx` の実装は変化しないことに注目しましょう。

実行結果は以下のようになります。

```
0.9980267285137223 -0.06279051136955548
0.992114702509753 -0.1253332172877223
0.982287254051086 -0.18738129033160678
0.9685831675803243 -0.24868985516799763
0.9510565268552302 -0.3090169549641805
0.9297765015048848 -0.36812450628076665
0.9048270740488197 -0.42577923867801076
0.8763067084564548 -0.48175361532742245
0.8443279615548829 -0.5358267309954384
0.8090170388185989 -0.5877851838552253
0.7705132962942676 -0.6374239176859706
0.7289686906262047 -0.6845470311358883

…
```

さらにその結果をチャートに表示するプログラムと結果は以下の通りです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="ExWjEbQ" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-2-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/ExWjEbQ">
  コンピューティング2 3-2-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

ほぼ完全に一致しているので、数値解を表す赤線はほとんど見えません。

# Runge-Kutta 法の導出

前回までに引き続き、以下のような常微分方程式を考えます。

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

時刻 $$t$$ のときの関数値 $$x(t)$$ がわかっており、$$t$$ から $$h$$ だけ進んだ関数値 $$x(t + h)$$ について知りたいとします。

$$x(t)$$ の $$t_1$$ のまわりでの Taylor 展開は以下の通りでした。

$$
x(t) = \sum_{m=0}^{\infty} \frac{(t - t_1)^m}{m!} x^{(m)}(t_1)
$$

これにより、$$x(t_1 + h)$$ は以下のようになります。

$$
x(t_1 + h) = \sum_{m = 0}^{\infty} \frac{h^m}{m!} x^{(m)}(t_1)
$$

右辺の級数を $$m = s$$ まで求め、残りの項を $$R_s$$ と表しましょう。

すなわち、

$$
x(t_1 + h) = \sum_{m = 0}^{s} \frac{h^m}{m!} x^{(m)}(t_1) + R_s
$$

となります。

さらに、

$$
\frac{d}{dt} x(t) = f(t, x(t))
$$

より、

$$
x(t_1 + h) = x(t_1) + \sum_{m = 1}^{s} \frac{h^m}{m!} f^{(m-1)}(t_1, x(t)) + R_s
$$

となります。

$$R_s$$ を誤差として切り捨てることで、$$x(t_1 + h)$$ の近似値を得ることができました。
この近似値は $$h^s$$ までは Taylor 展開と厳密に一致するはずです。
ある近似値 $$\tilde{x}(t_1 + h)$$ が $$x(t_1 + h)$$ の Taylor 展開の第 $$s$$ 項目まで一致するとき、$$\tilde{x}(t_1 + h)$$ の精度の次数は $$s$$ であると言います。

$$f(t, x(t))$$ の $$s - 1$$ 階微分が得られる場合、上記の式は直ちに計算することができます。
しかしながら、必ずしも与えられた微分方程式から高階の導関数を得られるわけではありません。
そのため別の方法で、精度の次数が $$s$$ となるような近似式を考えなければいけません。

$$x(t_1 + h)$$ を以下のような級数で表してみます。

$$
\begin{aligned}
x(t_1 + h) &= x(t_1) + h \sum_{m = 1}^s a_m k_m + R_s \\
k_m &= f(t_1 + c_m h, x(t_1) + c_m h k_{m - 1}) \qquad 2 \leq m \leq s \\
k_1 &= f(t_1, x(t_1))
\end{aligned}
$$

ここで $$a_1, \cdots, a_s$$ と $$c_2, \cdots, c_s$$ は未知の定数係数です。
この級数が Taylor 展開の第$$s$$項目までとすれば以下のようになります。

$$
x(t_1) + h \sum_{m = 1}^s a_m k_m + R_s = x(t_1) + \sum_{m = 1}^s \frac{h^m}{m!} f^{(m - 1)}(t_1, x(t_1)) + R_s
$$

すなわち、以下の条件を満たすように $$a_1, \cdots, a_s$$ と $$c_2, \cdots, c_s$$ を決めます。

$$
\sum_{m = 1}^s a_m k_m = \sum_{m = 1}^s \frac{h^{m-1}}{m!} f^{(m-1)}(t_1, x(t_1))
$$

実際の係数の求め方は、$$s$$ に応じて変わります。
精度の次数が $$s$$ となるように上記の係数を求めた式を $$s$$ 次の Runge-Kutta 法と呼びます。
前回利用したのは 4 次の Runge-Kutta 法でした。

# 1 次の Runge-Kutta 法

はじめに精度の次数が 1（$$s = 1$$）となるような級数について考えてみましょう。

これは次式のようになり、

$$
a_1 k_1 = f(t_1, x(t_1))
$$

また、

$$
k_1 = f(t_1, x(t_1))
$$

なので、$$a_1 = 1$$ です。

すなわち、

$$
x(t_1 + h) = x(t_1) + h f(t_1, x(t_1))
$$

となります。

これは、Euler 法の近似式と一致しています。

# 2 次の Runge-Kutta 法

$$
a_1 k_1 + a_2 k_2 = f(t_1, x(t_1)) + \frac{h}{2} f^{(1)} (t_1, x(t_1))
$$

$$
\begin{aligned}
k_1 &= f(t, x(t)) \\
k_2 &= f(t + c_2 h, x(t) + c_2 h k_{i - 1})
\end{aligned}
$$

関数 $$g(h)$$ を $$h = 0$$ とした結果を $$\left[g(h)\right]_{h=0}$$ と表すことにすると、以下の条件式が成り立ちます。

$$
\begin{aligned}
a_1 \left[k_1\right]_{h=0} + a_2 \left[k_2\right]_{h=0} &= f(t_1, x(t_1)) \\
a_1 \left[ \frac{d}{dh} k_1\right]_{h=0} + a_2 \left[ \frac{d}{dh} k_2\right]_{h=0} &= \frac{1}{2} \frac{d}{dx} f(t_1, x(t_1))
\end{aligned}
$$

ここで、

$$
(a_1 + a_2) f(t_1, x(t_1)) = f(t_1, x(t_1))
$$

より、

$$
a_1 + a_2 = 1
$$

となります。

また、

$$
c_2 a_2 \frac{d}{dt}f(t, x(t)) = \frac{1}{2} \frac{d}{dt}f(t, x(t))
$$

より、

$$
a_2 c_2 = \frac{1}{2}
$$

となります。

すなわち、以下の条件のもとで係数を決定する必要があります。

$$
\begin{aligned}
a_1 + a_2 &= 1 \\
a_2 c_2 &= \frac{1}{2}
\end{aligned}
$$

これらの係数には自由度があります。
2 次の Runge-Kutta 法は係数の決め方によっていくつかの呼び方があります。

Runge-Kutta の中点法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= \frac{1}{2} \\
a_1 &= 0 \\
a_2  &= 1
\end{aligned}
$$

Heun 法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= \frac{2}{3} \\
a_1 &= \frac{1}{4} \\
a_2  &= \frac{3}{4}
\end{aligned}
$$

改良 Euler 法の係数は以下のようになります。

$$
\begin{aligned}
c_2 &= 1 \\
a_1 &= \frac{1}{2} \\
a_2  &= \frac{1}{2}
\end{aligned}
$$

いずれの場合にも精度の次数が 2 であるための条件が満たされています。

また、3 次以降の Runge-Kutta 法についても同様に導出可能です。
