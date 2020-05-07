# 演習

## 演習 1

2 次の Runge-Kutta 法（Runge-Kutta の中点法、Heun 法、改良オイラー法）を用いて次の初期値問題を解きましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= -x(t) \\
x(0) &= 1
\end{aligned}
$$

## 演習 2

以下の連立微分方程式の初期値問題を考えます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

$$t = 0$$ から始まり、球の中心の x 座標を $$x(t)$$、y 座標を $$y(t)$$ とした時の球の運動のアニメーションを、$$-2 \leq x \leq 2$$、$$-2 \leq y \leq 2$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

## 演習 3

2 つの天体が座標 $$(0, 0)$$ と $$(r_0, 0)$$ に固定されていて、それぞれの質量を $$M_1$$ と $$M_2$$ とします。
これらの天体の周りを巡る質量 $$m$$ の衛星の運動方程式は以下の微分方程式で表されます。

$$
\begin{aligned}
m \frac{d^2}{dt^2} x(t) &= -G\frac{M_1 m x(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -G\frac{M_2 m (x(t) - r_0)}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}} \\
m \frac{d^2}{dt^2} y(t) &= -G\frac{M_1 m y(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -G\frac{M_2 m y_(t)}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}}
\end{aligned}
$$

$$GM_1 = GM_2 = 1$$、$$r_0 = 3.0$$ とし、新たな未知関数 $$u(t)$$ と $$v(t)$$ を導入すると、以下のように書き直すことができます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= u \\
\frac{d}{dt} y(t) &= v \\
\frac{d}{dt} u(t) &= -\frac{x(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} - \frac{x(t) - 3}{((x(t) - r_0)^2 + y(t)^2)^{\frac{3}{2}}} \\
\frac{d}{dt} v(t) &= -\frac{y(t)}{(x(t)^2 + y(t)^2)^{\frac{3}{2}}} -\frac{y_(t)}{((x(t) - 3)^2 + y(t)^2)^{\frac{3}{2}}}
\end{aligned}
$$

以下の初期値が与えられたときの衛星の運動のアニメーションを、$$-0.5 \leq x \leq 3.5$$、$$-2 \leq y \leq 2$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

$$
\begin{aligned}
x(0) &= 1.5 \\
y(0) &= 0 \\
u(0) &= 0.6 \\
v(0) &= 0.6
\end{aligned}
$$
