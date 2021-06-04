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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="gOmeNxP" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-16">
  <span>See the Pen <a href="https://codepen.io/likr/pen/gOmeNxP">
  コンピューティング2 4-16</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

また、上記のプログラムを 1 フレームごとに 1 ステップ進むようなアニメーションに改変したプログラムは以下のようになります。
キー入力によって新たな線を引くようになっています。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="jOBzjaG" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-17">
  <span>See the Pen <a href="https://codepen.io/likr/pen/jOBzjaG">
  コンピューティング2 4-17</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## 2 次元のランダムウォーク

次に、$$x, y$$ がそれぞれ独立にランダムウォークする場合を試してみます。
$$x, y$$ の初期値が (`width / 2`, `height / 2`) で、変化量がそれぞれ $$\Delta x \simeq N(0, 5^2), \Delta y \simeq N(0, 5^2)$$ として表されているとき、2 次元空間上に座標の変化をアニメーション表示するプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="YzZaoez" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-18">
  <span>See the Pen <a href="https://codepen.io/likr/pen/YzZaoez">
  コンピューティング2 4-18</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

画面の中心から線が不規則に動き回っていることが確認できます。

# ブラウン運動

溶媒中の微粒子が、分子の運動によって不規則に動くような現象を **ブラウン運動** と呼びます。
微粒子や分子は力学的な運動を行なっていますが、大量の分子が微粒子に不規則に衝突することで、微粒子の動きも不規則となります。
このとき、微粒子の動きがランダムウォークとなることが証明されています。
Einstein によるブラウン運動の理論的な解明は、分子や原子の存在の証明につながりました。

参考：

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/c2sYdKfR4tY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

以下は 500 個の点がランダムウォークするプログラムです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="qBrozxG" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-19">
  <span>See the Pen <a href="https://codepen.io/likr/pen/qBrozxG">
  コンピューティング2 4-19</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="XWMELqx" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-20">
  <span>See the Pen <a href="https://codepen.io/likr/pen/XWMELqx">
  コンピューティング2 4-20</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

100 ステップ後の座標は、期待値である (`width / 2`, `height / 2`) を中心に分布していることが確認できます。
赤い点で表される値も大体期待値通りとなっています。

また、分散はステップ数に比例するので、ステップ数が小さければバラツキが小さく、ステップ数が大きければバラツキが大きくなります。

# ランダムウォークと中心極限定理

**中心極限定理** によると、期待値 $$\mu$$、分散 $$\sigma^2$$ の独立な確率変数列 $$X_1, X_2, \cdots$$ において、 $$S_n = \sum_{i=1}^n X_i$$ は $$n$$ が十分に大きい時に正規分布 $$N(n \mu, n \sigma^2)$$ に従います。
すなわち、ある値の変化量が中心極限定理が成り立つような確率分布に従っていて、十分なステップ数をとるようなランダムウォークでは、確率分布によらず最終的な値は正規分布に従うことができます。

例えば、$$x$$ の初期値が 0、変化量が $$\Delta x \simeq U(-10, 10)$$ の 100 ステップのランダムウォークについて考えます。
最終的な $$x$$ の値のヒストグラムを描画するプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="gOmeNQL" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-21">
  <span>See the Pen <a href="https://codepen.io/likr/pen/gOmeNQL">
  コンピューティング2 4-21</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

$$x$$ の変化量自体は一様分布であるにも関わらず、100 ステップ後の座標は正規分布のようになっていることが確認できます。
