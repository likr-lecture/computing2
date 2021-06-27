## 演習 1

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="NWjKEgo" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/NWjKEgo">
  コンピューティング2 5-8</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

上記のプログラムにおいて、衛星の初速 `vx`, `vy` や初期距離 `d0` を変更した時の衛星の軌道を観察しましょう。

## 演習 2

運動方程式に基づいて、自由な物体の運動をシミュレーションしてみましょう。

例）単振り子運動

## 演習 3

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="JjNPaoM" data-preview="true" data-editable="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/JjNPaoM">
  コンピューティング2 5-7</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

上記のプログラムを改変し、3 つの球体が同時に運動するプログラムを作成しましょう。
色相の異なる 3 つの色をそれぞれの球体に割り当てるようにしましょう。

## 演習 4

1 つのおもりに 2 本のバネがつながっていて、バネのもう片側がそれぞれ (0, 0) と (600, 300) に固定されているとします。
おもりはバネの力と重力、空気抵抗力の影響を受けます。
ここで重力加速度を 1、バネの自然長を 300、バネ定数を 0.005、空気抵抗係数を 0.01 とします。
おもりを (100, 150) で静かに離したときの運動の様子を幅 600、高さ 600 の画面に描画するプログラムを作成しましょう。
また、おもりが静止する点の座標を求めてみましょう。

## 演習 5

10 頂点の完全グラフ $$K_{10}$$ をバネモデルによって可視化しましょう。
完全グラフ $$K_n$$ とは、全ての頂点の組の間に辺が存在するグラフです。

## 演習 6

グラフ $$G$$ において全てのノードが少なくても $$n$$ 本の辺を持つとき、$$G$$ を $$n$$連結グラフと呼びます。
2 連結グラフをランダムに生成し、Force-directed アルゴリズムを用いて可視化しましょう。

各ノードにつき、$$n$$ 個の自分以外のノードを重複なく選び、リンクで結ぶこと$$n$$連結グラフをつくることができます。

## 演習 7

アニーリングとノードの中心化を取り入れて非連結なツリーの可視化を行いましょう。
連結なツリーからランダムにリンクを削除することで非連結なツリーを生成できます。

## 演習 8

ノード数 40、リンク密度 0.05 のランダムグラフにおいて、最初に全てのノードについて `values[i]` を 0 以上 100 未満の範囲でランダムに割り当てます。
Force-directed アルゴリズムを用いて生成したランダムグラフを可視化しましょう。
その際にノードの色は色相は青色（240）で、彩度（HSB の 2 つ目の値）は `values[i]` の大きさに応じて割り当てましょう。
また、ノードの直径はノードの次数に比例するようにし、最も小さい次数のノードの直径は 40、最も大きい次数のノードの直径は 80 となるようにしましょう。
