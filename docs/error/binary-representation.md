# 2 進数による小数の表現

10 進数の実数 $$v$$ は、以下のように級数を用いて 2 進数 $$b_n \cdots b_1 b_0 . b_{-1} \cdots b_{-m}$$ として表されました。（$$n, m \geq 0$$）

$$
v = b_n 2^n + \cdots + b_1 2^1 + b_0 2^0 + b_{-1} 2^{-1} + \cdots + b_{-m} 2^{-m}
$$

10 進数では有限の桁数で表される実数でも、2 進数では循環小数となり、有限の桁数で表すことができなくなる場合があります。

0 に 0.1 を 10 回足し合わせると、合計値は 1.0 であることが期待されますが、コンピュータ上ではその通りになりません。

Processing で以下のプログラムを実行してみましょう。

```java
float s = 0;
for (int i = 0; i < 10; ++i) {
  s += 0.1;
}
println(s);
```

実行結果は以下のようになります。

```console
1.0000001
```

実行結果は 1.0 ちょうどではなく、誤差が生じています。

0.1 ではなく、$$0.03125 = \frac{1}{2^5}$$ で試してみましょう。

```java
float s = 0;
for (int i = 0; i < 32; ++i) {
  s += 0.03125;
}
println(s);
```

実行結果は以下のようになります。

```console
1.0
```

これは、0.03125 が 2 進数でも有限の桁数で表される、すなわち誤差が発生しないためです。