# モンテカルロシミュレーション

**モンテカルロシミュレーション** は、乱数を用いた試行を十分な回数繰り返すことによってシステムの統計的な振る舞いを調べるシミュレーションの手法です。

以下は 2 個の 6 面ダイスを振って、出た目の合計値を調べるプログラムです。
これも一種のモンテカルロシミュレーションと見做すことができます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="eYvMaPR" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-4">
  <span>See the Pen <a href="https://codepen.io/likr/pen/eYvMaPR">
  コンピューティング2 4-4</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# モンティ・ホール問題

次のような問題を考えます。
3 つのドアがあり、そのうち 1 つのドアが当たりになっています。
プレイヤーは 3 つのドアから 1 つを選びます。
司会者は、プレイヤーがドアを選んだ後、残った２つのドアのうち外れのドアを１つ開きます。
司会者がドアを選んだ後、プレイヤーはドアを選び直すことができます。
ドアを選び直した場合とそうじゃない場合で当たりを引く確率に違いはあるでしょうか？
これは、**モンティ・ホール問題** と呼ばれる確率論の有名な問題です。

ドアを選び直さない場合当たりの確率は 1/3 ですが、ドアを選び直すと当たりの確率は 2/3 になります。
これをモンテカルロシミュレーションで確かめてみましょう。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="wvJmbRB" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-5">
  <span>See the Pen <a href="https://codepen.io/likr/pen/wvJmbRB">
  コンピューティング2 4-5</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 円の面積の計算

モンテカルロシミュレーションの有名な応用として、円周率の近似があります。

原点(0, 0)を中心とした半径 1 の円の方程式は以下のように表されます。

$$
x^2 + y^2 = 1
$$

この円の面積は円周率と等しくなります。
ここで、この円の $$x \geq 0$$、$$y \geq 0$$ の領域 A を考えます。
A の面積は元の円の面積の 1/4 です。
また A は、(0, 0), (0, 1), (1, 0), (1, 1)を頂点とした一辺の長さが 1 の正方形で囲むことができます。
正方形の中の点をランダムに選んだとき、その点が A に含まれる確率は A の面積に等しくなります。
すなわち、$$0 \leq x \leq 1$$、$$0 \leq y \leq 1$$の範囲で乱数 $$(x, y)$$ を発生させ、その点が A に含まれた回数を数えることで A の面積を近似できます。
そして、A の面積を 4 倍することで元の円の面積、すなわち円周率を得ることができます。

これに基づいて Processing でプログラムを作成すると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="XWMEwGz" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-6">
  <span>See the Pen <a href="https://codepen.io/likr/pen/XWMEwGz">
  コンピューティング2 4-6</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

大体円周率に近い値を得ることができています。
