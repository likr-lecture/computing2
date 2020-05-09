# 演習の解答例

## 演習 1

0.1 の 2 進数表現は $$0.00011001100 \cdots $$ となります。
これに IEEE 754 の正規化をすると $$1.10011001100 \cdots \times 2 ^ {-4}$$ となり、指数部は 123 = 0x7B、仮数部は 0x4CCCCD となります。

Processing で確認してみます。

```java
float v = 0.1;
int bits = Float.floatToIntBits(v);

int s = bits < 0 ? 1 : 0;
int e = (bits & 0x7f800000) >> 23;
int f = bits & 0x007fffff;

println(s, hex(e), hex(f));
```

実行結果は以下のようになります。

```console
0 0000007B 004CCCCD
```

## 演習 2

省略

## 演習 3

32 bit では、指数部の最大値が 16 進数表現で `FE` のとき $$q = 127 $$ 、仮数部の最大値が 16 進数表現で `7FFFFF` のとき $$c = 16777215 \times 2^{-23}$$ となります。
$$v = c \times 2 ^ q = 16777215 \times 2^{104} = 340282346638528859811704183484516925440$$ となりますが、丸め誤差により `3.40282347E+38` が最大値となります。

`3.40282347E+38` の 16 進数表現をプログラムで確認してみましょう。

```java
float v =  3.40282347E+38;
int bits = Float.floatToIntBits(v);

int s = bits < 0 ? 1 : 0;
int e = (bits & 0x7f800000) >> 23;
int f = bits & 0x007fffff;

println(s, hex(e), hex(f));
```

実行結果は以下のようになり、最初の 16 進数表現と一致していることが確認できます。

```console
0 000000FE 007FFFFF
```
