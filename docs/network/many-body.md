# 天体の運動

物体にかかる力が刻一刻と変化するような運動の例として、天体の運動を取り上げます。
画面の中心に固定された天体があり、その周りを衛星が周回します。

万有引力定数を $$G$$、中心の天体の質量を $$M$$、中心の天体と衛星の距離を $$d$$ とすると、衛星が中心の天体から受ける力による加速度は以下のように表されます。

$$
a(t) = -\frac{GM}{d^2}
$$

これを用いて衛星の軌道をシミュレーションしてみます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="wvdwEMw" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/wvdwEMw">
  コンピューティング2 5-7</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>


# 多体問題

上のプログラムを複数の天体が重力で引き合うのように改変すると以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="VwbZGmV" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/VwbZGmV">
  コンピューティング2 5-9</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# クーロン力

荷電粒子（電荷を帯びた粒子）には **クーロンの法則** に従う **クーロン力** が働きます。
2 つの荷電粒子間に働くクーロン力は以下の式で表されます。

$$
F = k \frac{q_1 q_2}{r^2}
$$

ここで $$k$$ は比例定数、$$q_1$$ と $$q_2$$ はそれぞれの粒子の電荷、$$r$$ は 2 粒子間の距離とします。
$$q_1$$ と $$q_2$$ が同じ符号であれば 2 粒子は反発し合い、異なる符号であれば 2 粒子は引き合います。

空間に複数の荷電粒子が存在するとしましょう。
$$i$$ 番目の粒子は、他の全ての粒子からクーロン力の影響を受けます。
そのため速度 Verlet 法では、二重ループによって全粒子ペアの影響を計算する必要があります。

以下は 5 つの荷電粒子の運動のシミュレーションを行うプログラムです。
0 番目の粒子は-5 の電荷を、それ以外は+1 の電荷を持っています。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-slug-hash="zYwOJZP" data-preview="true" data-user="likr" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/likr/pen/zYwOJZP">
  コンピューティング2 5-10</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

正の電荷と負の電荷はお互いに引き合い、正の電荷同士は反発し合いながら運動します。
