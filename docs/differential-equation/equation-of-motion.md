# 運動方程式の数値解法

振り子の運動において、糸が地面の鉛直方向となす角度 $$\theta$$ は以下の微分方程式表されます。

$$
\frac{d^2}{dt^2} \theta = - \frac{g}{l} \sin (\theta)
$$

ここで、$$l$$ は糸の長さ、$$g$$ は重力加速度を表します。
これは、角速度 $$\frac{d}{dt} \theta(t) = \omega(t)$$ を導入することで、以下の連立微分方程式で表されます。

$$
\begin{aligned}
\frac{d}{dt} \omega(t) &= - \frac{g}{l} \sin(\theta(t)) \\
\frac{d}{dt} \theta(t) &= \omega(t)
\end{aligned}
$$

$$\theta$$ の時間変化をアニメーションで描画するプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="dyvoedY" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 3-4-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/dyvoedY">
  コンピューティング2 3-4-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
