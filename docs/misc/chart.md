# チャートの描画

本資料では、グラフ理論におけるグラフと区別するためにデータを図示したものを **チャート** と呼ぶことにします。
Processing を用いた二次元チャートの描画について考えてみましょう。

例えば、$$y = f(x) = x^2$$を$$-1 \leq x \leq 1$$、$$-1 \leq y \leq 1$$の範囲で幅 600、高さ 600 のウィンドウに描画したいとします。
ここで、計算している空間と描画される画面の空間が異なることに注意しなければなりません。
それぞれの空間を **計算空間** と **画面空間** と呼ぶことにしましょう。

画面空間では左上が(0, 0)、右下が(width, height)であるのに対して、計算空間では左上が(-1, 1)、右下(1, -1)です。
Processing の描画命令は画面空間上の座標で行わなければいけないので、計算空間の座標を画面空間の座標に変換しなければいけません。
画面空間上での放物線 $$y = x^2$$ を 3 つのパラメータ$$a, b, c$$を用いて以下のように表しましょう。

$$
y = a (x - b)^2 + c
$$

この放物線は(width / 2, height / 2)を頂点とし、(0, 0)を通るので、$$a, b, c$$はそれぞれ以下のように求まります。

$$
\begin{aligned}
a &=  -\frac{2 \text{height}}{\text{width}^2} \\
b &= \frac{\text{width}}{2} \\
c &= \frac{\text{height}}{2}
\end{aligned}
$$

これに基づいてチャートの描画をするプログラムを作成すると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="wvgNRYP" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 演習misc-1-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/wvgNRYP">
  コンピューティング2 演習misc-1-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

$$y = x^2$$が$$-1 \leq x \leq 1$$、$$-1 \leq y \leq 1$$の範囲で正しく描画できていることが確認できます。

今回は二次関数だったので、画面空間上での一般式を導出することが容易でした。
どのような関数でも適用できるもう一つのアプローチとして、計算空間と画面空間の間の **座標変換** を考えてみましょう。

まずは一般的な議論として、一変数の座標変換を考えます。
$$a_l$$から$$a_r$$の範囲の$$a$$を$$b_l$$から$$b_r$$の範囲の$$b$$に変換します。
このとき、それぞれの範囲における$$a$$と$$b$$の比率は等しくなければいけません。
すなわち、$$a$$ と $$b$$ の関係は以下のようになります。

$$
\frac{a - a_l}{a_r - a_l} = \frac{b - b_l}{b_r - b_l}
$$

これを整理した次式によって$$a$$の空間の値を$$b$$の空間の値へと変換することができます。

$$
b = (b_r - b_l) \frac{a - a_l}{a_r - a_l} + b_l
$$

これを利用して、次式のように計算空間上の $$x_C (\text{xLeft} \leq x_C \leq \text{xRight}$$ を画面空間上の $$x_D (0 \leq x_D \leq width)$$ へ対応付けることができます。

$$
x_D = \frac{\text{width}}{\text{xRight} - \text{xLeft}} (x_C - \text{xLeft})
$$

$$y$$についても同様に、計算空間上の $$y_C (\text{yTop} \leq y_C \leq \text{yBottom}$$を画面空間上の $$y_D (0 \leq y_D \leq height)$$ へ対応付けることができます。

$$
y_D = \frac{\text{height}}{\text{yBottom} - \text{yTop}} (y_C - \text{yTop})
$$

$$y$$については、画面空間の上下が反転しているため、yBottom には height が、yTop には 0 が対応することに注意しましょう。

Processing では、 `applyMatrix` 関数を用いることで上述の座標変換を行うことができます。`applyMatrix(a, b, c, d, e, f)` は次式で表される **アフィン座標変換** を行います。

$$
\left( \begin{array}{c}
x_D \\
y_D \\
1
\end{array}\right)
=
\left( \begin{array}{ccc}
a & c & e \\
b & d & f \\
0 & 0 & 1
\end{array}\right)
\left( \begin{array}{c}
x_C \\
y_C \\
1
\end{array}\right)
$$

計算空間から画面空間への座標変換は アフィン変換を用いて次のように表されます。

$$
\begin{aligned}
\left( \begin{array}{c}
x_D \\
y_D \\
1
\end{array} \right)
&=
\left( \begin{array}{ccc}
\frac{\text{width}}{\text{xRight} - \text{xLeft}} & 0 & -\frac{\text{width}}{\text{xRight} - \text{xLeft}}\text{xLeft}\\
0 & \frac{\text{height}}{\text{yBottom} - \text{yTop}} & -\frac{\text{height}}{\text{yBottom} - \text{yTop}} \text{yTop}\\
0 & 0 & 1
\end{array} \right)
\left( \begin{array}{c}
x_C \\
y_C \\
1
\end{array} \right) \\
&=
\left( \begin{array}{ccc}
\frac{\text{width}}{\text{xRight} - \text{xLeft}} & 0 & 0 \\
0 & \frac{\text{height}}{\text{yBottom} - \text{yTop}} & 0 \\
0 & 0 & 1
\end{array}\right)
\left( \begin{array}{ccc}
1 & 0 & -\text{xLeft} \\
0 & 1 & -\text{yTop} \\
0 & 0 & 1
\end{array}\right)
\left( \begin{array}{c}
x_C \\
y_C \\
1
\end{array}\right)
\end{aligned}
$$

`applyMatrix(sx, 0, 0, sy, 0, 0)` と `applyMatrix(1, 0, 0, 1, tx, ty)` はそれぞれショートカット関数の `scale(sx, sy)` と `translate(tx, ty)` を用いることができます。

座標変換を用いた二次関数の描画プログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="yLgZGwQ" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 演習misc-1-2">
  <span>See the Pen <a href="https://codepen.io/likr/pen/yLgZGwQ">
  コンピューティング2 演習misc-1-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
