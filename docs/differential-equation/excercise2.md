## 演習 1

次の初期値問題の解析解を求めましょう。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= x(t) \\
x(0) &= 0 \\
\frac{d}{dt} x(0) &= -1
\end{aligned}
$$

そして、解析解と Euler 法による数値解をそれぞれ、$$0 \leq t \leq 10 \pi$$、$$-5 \leq x \leq 5$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

## 演習 2

次の初期値問題の Euler 法による数値解を $$0 \leq t \leq 100$$、$$0 \leq x, y \leq 150$$の範囲で幅 600、高さ 600 のウィンドウに描画しましょう。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= \alpha x(t) - \beta x(t) y(t) \\
\frac{d}{dt} y(t) &= -\gamma y(t) + \delta x(t) y(t) \\
x(0) &= 80 \\
y(0) &= 30 \\
\alpha &= 0.25 \\
\beta &= 0.01 \\
\gamma &= 1.0 \\
\delta &= 0.01
\end{aligned}
$$

なお、この連立微分方程式は **Lotka-Volterra の方程式** として知られています。
Lotka-Volterra の方程式は生物の捕食被食関係による個体数の増減を表現する数理モデルです。

## 演習 3

演習 1 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。

## 演習 4

演習 2 を Runge-Kutta 法を用いて解き、Euler 法での結果と比較しましょう。
