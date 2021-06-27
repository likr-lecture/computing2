# 等速直線運動

まず最初の例として、加速度が常に 0 であるとき、すなわち等速直線運動について考えます。
Verlet 法の更新式に基づいて Processing のプログラムを作成します。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="ZEKzjEd" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/ZEKzjEd">
  コンピューティング2 5-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>


# 放物運動

次に放物運動を考えます。
例えばボールを真上に放り投げたとき、しばらく上昇してから下降を始めます。
これはボールが重力によって下方向に加速し続けるためです。

地球上の重力加速度は約 $$9.8 m/s^2$$ です。
しかし、1 ピクセルの距離や 1 フレームの時間を適切に定めることで、プログラム上では加速度の値は計算のしやすい値に設定することができます。

Processing でのプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="BaRBPNV" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/BaRBPNV">
  コンピューティング2 5-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

さらに、横方向の初速度を加え、投射運動をシミュレーションしてみます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="vYmBaLJ" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/vYmBaLJ">
  コンピューティング2 5-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>


# 壁への衝突

画面の端を壁と見立てて、壁に当たったときに物体が反射する動作を上のプログラムに加えます。
反射は速度を-1 倍することで実現されます。

プログラムにすると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="qBmWyep" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/qBmWyep">
  コンピューティング2 5-3</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

上のプログラムでは、壁への衝突によって減速が起こりませんでした。
衝突の度に減速の効果を加えると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="OJmLwKG" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/OJmLwKG">
  コンピューティング2 5-4</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 力の加算

物体に複数の力がかかっている場合、物体の加速度はそれぞれの力の影響力の加算で表すことができます。

例えば、空気抵抗を考慮した自由落下では、物体にかかる力は以下のように表されます。

$$
\begin{aligned}
F &= F_1 + F_2 \\
F_1 &= mg \\
F_2 &= kv
\end{aligned}
$$

物体の運動をシミュレーションする際には、$$F_1$$ と $$F_2$$ による加速度をそれぞれ別で計算して足し合わせることができます。
プログラムにすると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="bGWbxGb" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/bGWbxGb">
  コンピューティング2 5-5</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# Verlet 法プログラムの整理

Verlet 法のプログラムは以下の部分から構成されています。

1. オブジェクトの描画
1. 座標の更新
1. 速度の更新

速度の更新は、さらにオブジェクトにかかる力をそれぞれ個別に適用することで処理を分割できます。
より複雑なシミュレーションを行うため、プログラムを関数化して整理します。

以下のプログラムは、重力、壁の反射、空気抵抗を考慮した球体の運動を関数化して整理したプログラムです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="JjNPaoM" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/JjNPaoM">
  コンピューティング2 5-6</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

以降はこのプログラムをベースにしてシミュレーションを行います。
