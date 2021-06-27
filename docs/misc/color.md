# 色空間と colorMode

色をランダムに生成することを考えてみましょう。
例えば以下のように RGB の各成分を `random` 関数によって生成することができます。

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="abWoQBN" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/abWoQBN">
  コンピューティング2 misc-3-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

色の生成には成功していますが、どちらかというとくすんだ色の方が目立ちます。
また、色に統一感がないように見えます。
これを解決するために、**HSB 色空間** を使用することが考えられます。

Processing では、`fill` や `stroke` で設定する色はデフォルトで **RGB 色空間** が用いられます。
RGB 色空間では、一つの色を 3 次元のベクトル、すなわち赤色、緑色、青色の 3 つの成分で表します。

以下は横軸に赤色、縦軸に緑色成分をとり、青色成分を時間変化させて描画するプログラムです。

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="OJmLabE" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/OJmLabE">
  コンピューティング2 misc-3-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

HSB 色空間では、RGB と同様に一つの色を 3 次元のベクトルで表しますが、それぞれの成分は色相（Hue）、彩度（Saturation）、明度（Brightness）を表します。

Processing では、 `colorMode` 関数を使用することで、HSB 色空間を使用することができます。
`colorMode` 関数は`colorMode(mode)` または `colorMode(mode, max1, max2, max3)` のように呼び出します。
`mode` には定数 `RGB` または `HSB` を渡すことができます。
`max1`、`max2`、`max3` は色の 3 成分の最大値を設定します。

RGB と同様に、彩度を横軸、明度を縦軸、色相を時間軸にとって描画するプログラムを以下に提示します。

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="vYmBQgW" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/vYmBQgW">
  コンピューティング2 misc-3-3</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

HSB 色空間における色彩は、円柱の断面の円の角度として表されます。
以下は色相の変化を円上に可視化したプログラムです。

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="abWoQJZ" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/abWoQJZ">
  コンピューティング2 misc-3-4</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

HSB 色空間において、彩度と明度を固定したまま色相をランダムに決めることで生成される色に統一感を持たせることができます。

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="XWRryRm" data-preview="true" data-user="likr" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/XWRryRm">
  コンピューティング2 misc-3-5</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

HSB は連続的な値の大小表現にも使用することができます。
