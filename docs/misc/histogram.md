# ヒストグラムの描画

ある範囲の値が何回現れたかは **ヒストグラム** （**度数分布表**）で可視化します。
ヒストグラムにおける棒のことはビンと呼ばれます。

0 以上 100 未満の実数の分布を表示する Processing のプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="GRWOBrM" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 misc-2-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/GRWOBrM">
  コンピューティング2 misc-2-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

上記のプログラムは、それぞれのビンに含まれる実数の個数を数えます。

整数値をとるような離散的な事象を数える場合は、最大値の設定に注意しましょう。
生成される乱数は 0 または 1 なので 0 以上 2 未満の範囲を 2 個のビンで表します。
ここでは、コインを投げて表と裏が出た回数をそれぞれ数えてヒストグラム化しています。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="KKWyBWb" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 misc-2-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/KKWyBWb">
  コンピューティング2 misc-2-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
