# バネの運動

バネによってかかる力は、バネの自然長からの伸びに比例します。
これを **フックの法則** と呼びます。
バネの現在の長さを $$d$$ 、自然長を $$L$$ 、バネ定数を $$k$$ としたときのフックの法則に従う力は以下のように表されます。

$$
F_s = k (L - d)
$$

片側が固定され、もう片側におもりがついたバネの運動の Verlet 法によるシミュレーションのプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="YzVKOZd" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/YzVKOZd">
  コンピューティング2 5-11</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

上のプログラムに重力の影響を加えると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="PomYyoz" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/PomYyoz">
  コンピューティング2 5-12</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

また、空気抵抗力を考慮すると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="XWRrxdy" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/XWRrxdy">
  コンピューティング2 5-13</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

空気抵抗を考慮すると、バネの伸びが自然長に近づくように収束していきます。

# 複数のバネの運動

一つのおもりに複数のバネがつながっている場合でも、それぞれのバネからかかる力を合成することで運動のシミュレーションを行うことができます。
以下は、画面の左上、左下、右下、右上の 4 点に片側が固定されたバネがあり、もう一方につながったおもりを (100, 150) の点で静かに離したときの運動のシミュレーションです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="qBmWJNz" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/qBmWJNz">
  コンピューティング2 5-14</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 両端におもりをつけたバネの運動

バネの片側を固定せず、移動可能なおもりをバネの両端につけた場合、それぞれのおもりに力が働きます。
一方のおもりにかかる力と同じ大きさの力が逆方向にもう一方のおもりへかかります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="xxdKygN" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/xxdKygN">
  コンピューティング2 5-15</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>