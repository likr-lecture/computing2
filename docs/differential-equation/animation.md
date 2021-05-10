# 微分方程式とアニメーションの描画

これまでは、 $$t$$ を横軸にとったグラフとして微分方程式の初期値問題の解を描画してきました。
今度は、微分方程式で表される量の時間変化を Processing のアニメーションで表してみましょう。

前回扱った以下の初期値問題を考えます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

これは以下の連立微分方程式の初期値問題に書き換えることができました。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

これをアニメーション表示するためには、$$t$$ が 0 から始まり、$$h$$ ずつ $$t$$ と $$x(t)$$ を更新していく様子を描画する必要があります。。
球の x 座標を $$x(t)$$、y 座標を 0 として、$$-2 \leq x \leq 2$$、$$-2 \leq y \leq 2$$の範囲で幅 300、高さ 300 のウィンドウに球の運動を描画するプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="VwpLxMv" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-3-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/VwpLxMv">
  コンピューティング2 3-3-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

ポイントは、現在の $$t$$ と $$x(t)$$ の状態を保持する変数 `t1` と `x1` をグローバル変数に移し、1 回の `draw` 関数の実行ごとに `h` だけ `t1` を更新するようにしたところです。
`h` の値を変えるとどうなるか確認してみましょう。

また、グローバル変数を利用することで、プログラム全体の見通しが悪くなってしまう場合があります。
このようなプログラムを、クラスなどを使用して整理する方法は各自で考えてみましょう。
