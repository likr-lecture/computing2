# イントロダクション 〜平方根の計算〜

電卓は、ある数の平方根の具体的な値をボタン一つで計算してくれます。
数学的な証明だけであれば、文字式を用いた代数的な操作だけで十分かもしれませんが、建造物の設計やコンピュータグラフィックスなどの工学的な問題解決においては具体的な数値で計算することが必要不可欠です。

p5.js においても`sqrt`関数を使うことで引数 $$n$$ の値の平方根 $$\sqrt{n}$$ を得ることができます。
しかし、`sqrt`関数を作った人は、`sqrt`関数を使わずに`sqrt`関数を定義したはずです。
その仕組みはどうなっているのでしょうか？
$$\sqrt{2}$$ の値は `1.41421356...`と知られていますが、その数字はどのように計算されたのでしょうか？

$$\sqrt{2}$$ は無理数なので、その具体的な数値は無限に小さい値まで求めることができます。
しかし、工学的な問題解決においては、**十分な精度** の値が得られれば良いとしましょう。
例えば、最適な容器のサイズが 1 ナノメートルの精度で計算できても、実際にその精度で加工することが不可能であればその精度に意味はありません。
誤差が 0.1 ミリメートル以内であればそれで十分かもしれません。
そこで、平方根などの関数の具体的な値を、加減乗除などの基本的な演算を用いて十分な精度まで **近似的** に得る方法を考えてみましょう。

十分な精度とはどのような条件か考えてみます。
許容可能な誤差を $$\epsilon$$ （$$\epsilon > 0$$）、真値を $$T$$ 、近似値を $$A$$ としたとき、以下の式が満たされたとき $$A$$ が十分な精度であると言うことにしましょう。

$$
\epsilon \geq \left| T - A \right|
$$

具体的に平方根の計算で考えてみます。
$$\sqrt{n}$$ （$$n \geq \epsilon$$）の近似値を $$x$$ とします。
ここでは $$\sqrt{n}$$ が真値です。
近似値 $$x$$ は以下の条件を満たさなければなりません。

$$
(x + \epsilon)^2 \geq n \geq (x - \epsilon)^2
$$

この条件には平方根が含まれておらず、四則演算だけであることに注目しましょう。
これに基づいて、具体的な数値の算出方法を考えてみましょう。
