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
