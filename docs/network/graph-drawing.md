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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="poPzxrK" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/poPzxrK">
  コンピューティング2 5-16</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

ノードとリンクの情報を修正して別のグラフを可視化してみます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="qBmWJxB" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/qBmWJxB">
  コンピューティング2 5-17</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

うまくいけば以下のような配置になります。

![fig1](./figs/fig1)

一方で、ノードの初期位置によっては以下のように重なりが生じたりします。

![fig2](./figs/fig2)

# ネットワーク可視化へのクーロン力の導入

前回、バネの力だけではネットワークのノードに重なりが生じて、ノード配置がうまくいかない場合がありました。
クーロン力を用いてノード間の反発力を計算することでノードの重なりを回避してみましょう。
ノードが全て同じ符号の電荷を持っているとします。
i 番目のノードには、j 番目のノードから、電荷に比例して距離の 2 乗に反比例する加速度が働きます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="qBmWJJd" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/qBmWJJd">
  コンピューティング2 5-18</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

ノード間の反発力を導入することでノードを均等に配置できるようになりました。

# グラフの生成

ランダムに生成したグラフを Force-directed アルゴリズムで可視化してみます。
以下のプログラムでは、10 頂点のグラフにおいて、全ての頂点ペアが 0.5 の確率でリンクを持つようにランダムにグラフを生成しています。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="JjNPmwd" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/JjNPmwd">
  コンピューティング2 5-19</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

閉路を持たないグラフを木と呼びます。
以下は 100 ノードの木をランダムに生成し、Force-directed アルゴリズムによって可視化するプログラムです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="dyWbgrp" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/dyWbgrp">
  コンピューティング2 5-20</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="rNmBqXX" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/rNmBqXX">
  コンピューティング2 5-21</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# ノードの中心化

以下のように画面の中心に引き寄せる力をかけることで、非連結なグラフにおいても画面内に収めることができます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="NWjKEWL" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/NWjKEWL">
  コンピューティング2 5-22</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 視覚表現の工夫

ネットワークのグラフやノードの持つ情報を、視覚的表現と対応づけることで、それらの情報の可視化が可能です。

以下はノードの次数を計算し、次数が低いノードは青色、高いノードは赤色でその間を連続的に色相を変化させています。
色相を 0 から 360 の範囲で表すとき、青色の色相は 240、赤色の色相は 0、その中間の緑色の色相は 120 です。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="MWmgzwM" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/MWmgzwM">
  コンピューティング2 5-23</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>