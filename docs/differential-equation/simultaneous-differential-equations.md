# 連立微分方程式

二つの変数が相互に影響しあっている現象について考えてみましょう。
そのような現象は例えば次のような **連立微分方程式** として表されます。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= \alpha x(t) + \beta y(t) \\
\frac{d}{dt} y(t) &= \gamma x(t) + \delta y(t)
\end{aligned}
$$

最初はこの一般解を考えてみましょう。

ここから $$t$$ と $$x(t)$$ だけの微分方程式を導きます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) &= \alpha \frac{d}{dt} x(t) + \beta \frac{d}{dt} y(t) \\
\frac{d}{dt} y(t) &= \frac{1}{\beta} \left( \frac{d^2}{dt^2} x(t) - \alpha \frac{d}{dt} x(t) \right)
\end{aligned}
$$

と、

$$
y(t) = \frac{1}{\beta} \left( \frac{d}{dt} x(t) - \alpha x(t) \right)
$$

より、

$$
\frac{1}{\beta} \left( \frac{d^2}{dt^2} x(t) - \alpha \frac{d}{dt} x(t) \right) = \gamma x(t) + \frac{\delta}{\beta} \left( \frac{d}{dt} x(t) - \alpha x(t) \right)
$$

となります。

これを整理すると、

$$
\frac{d^2}{dt^2} x(t) - (\alpha + \delta) \frac{d}{dt} x(t) + (\alpha \delta - \beta \gamma) x(t) = 0
$$

となり、2 階同次常微分方程式が得られました。

$$y(t)$$ についても同様に、

$$
\frac{d^2}{dt^2} y(t) - (\alpha + \delta) \frac{d}{dt} y(t) + (\alpha \delta - \beta \gamma) y(t) = 0
$$

となります。

2 階同次常微分方程式の一般解を用いてこれらの連立方程式の一般解を得ることができます。

具体的に次のような初期値問題を考えてみましょう。

$$
\begin{aligned}
\frac{d}{dt} x(t) &= y(t) \\
\frac{d}{dt} y(t) &= -x(t) \\
x(0) &= 1 \\
y(0) &= 0
\end{aligned}
$$

$$x(t)$$ と $$y(t)$$ それぞれについて以下のような 2 階常微分方程式を得ることができます。

$$
\begin{aligned}
\frac{d^2}{dt^2} x(t) + x(t) &= 0 \\
\frac{d^2}{dt^2} y(t) + y(t) &= 0
\end{aligned}
$$

また、

$$
\begin{aligned}
\frac{d}{dt} x(0) &= 0 \\
\frac{d}{dt} y(0) &= -1
\end{aligned}
$$

です。

2 階同次常微分方程式の解析解と同様の計算を行うことで、

$$
\begin{aligned}
x(t) &= \cos(t) \\
y(t) &= -\sin(t)
\end{aligned}
$$

となります。
