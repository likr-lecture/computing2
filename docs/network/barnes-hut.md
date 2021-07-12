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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="jOmVObo" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/jOmVObo">
  Graph Drawing - Les Miserables</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

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

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="oNWYNze" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/oNWYNze">
  コンピューティング2 5-24</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

(100, 100) から遠いところでは、大きな領域の部分木の重心で力の計算が近似できることを確認できます。

それでは、速度 Verlet 法に Barnes-Hut アルゴリズムを取り入れて計算をしてみましょう。
以下のプログラムは赤色で表した負電荷と青色で表した正電荷の運動のシミュレーションです。
負電荷にかかるクーロン力を上記のプログラムと同様に可視化しています。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="VwbmwrX" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/VwbmwrX">
  コンピューティング2 5-25</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# ネットワーク可視化への Barnes-Hut アルゴリズムの導入

Verlet 法によるネットワーク可視化において、ノード間の反発力の計算に Barnes-Hut アルゴリズムを導入しましょう。
プログラムの全体は以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="yLbVLRO" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/yLbVLRO">
  コンピューティング2 5-26</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

可視化結果自体は前回と大きく変わりません。

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
