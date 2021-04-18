# 演習の解答例

## 演習 1

0.1 の ビット表現は $$0.00011001100 \cdots $$ となります。
これに IEEE 754 の正規化をすると $$1.10011001100 \cdots \times 2 ^ {-4}$$ となり、指数部は 123 = 0x7B、仮数部は 0x4CCCCD となります。

JavaScript で確認してみます。

```java
const buffer = new ArrayBuffer(4);
const view = new DataView(buffer);
view.setFloat32(0, 0.1);
const bits = view.getUint32(0);
console.log(bits.toString(2).padStart(32, "0"));
```

実行結果は以下のようになります。

```console
00111101110011001100110011001101
```

## 演習 2

省略

## 演習 3

32 ビット では、指数部の最大値が ビット表現で `11111110` のとき $$q = 127 $$ 、仮数部の最大値が ビット表現で `11111111111111111111111` のとき $$c = 16777215 \times 2^{-23}$$ となります。
$$v = c \times 2 ^ q = 16777215 \times 2^{104} = 340282346638528859811704183484516925440$$ となりますが、丸め誤差により `3.40282347E+38` が最大値となります。

`3.40282347E+38` の ビット表現をプログラムで確認してみましょう。

```java
const buffer = new ArrayBuffer(4);
const view = new DataView(buffer);
view.setFloat32(0, 3.40282347E+38);
const bits = view.getUint32(0);
console.log(bits.toString(2).padStart(32, "0"));
```

実行結果は以下のようになり、最初の ビット表現と一致していることが確認できます。

```console
01111111011111111111111111111111
```
