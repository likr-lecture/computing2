# 擬似乱数

コンピュータで何らかのアプリケーションを実現する上で **乱数** は様々な場面で出現します。
ゲームなどでゲーム性を高めるためにランダム要素が取り入れられます。
また、厳密に解くことが難しい問題でも、乱数を利用した **乱択アルゴリズム** によって比較的容易に解くことができるようになります。

私たちが普段使うコンピュータは決定性のある計算機なので、本来入力が同じであれば出力も常に同じになります。
しかし、乱数を用いた計算は、入力が同じであっても出力が毎回異なります。
これは、乱数関数が決定的な計算からランダムに見えるような数列を生成し、呼び出されるたびに新たな数列の値を返すことで実現しています。
このような、ランダムに見える関数を使って擬似的に乱数を生成する方法を **擬似乱数** と呼びます。
コンピュータ上の乱数は基本的には擬似乱数が用いられます。

Processing の乱数関数 `random` について復習しましょう。
`random` 関数は一つまたは二つの実数型の引数を受け取ります。
1 つの引数で `random(high)` と呼び出された場合、`random` 関数は 0 以上 `high` 未満の実数値をランダムに返します。
2 つの引数で `random(low, high)` と呼び出された場合、 `random` 関数は `low` 以上 `high` 未満の実数値をランダムに返します。
`random` 関数は一様乱数を返すため、`low`（または 0）と `high` の間の値が等確率で出現します。

なお、JavaScript には 0 以上 1 未満の一様乱数を返す `Math.random` 関数もありますが、本資料では Processing の `random` 関数を使用します。

例えば、確率 $$0 \leq p \leq 1$$ である処理を実行したい場合には、以下のようなプログラムを書くことができます。

```javascript
function setup() {
  createCanvas(300, 300);
  noLoop();
}

function draw() {
  if (random(1) < 0.1) {
    // 10%の確率で実行する処理
  }
}
```

コインを投げたときに表が出る確率を 50%とし、10000 回コインを投げたときに表が出た回数を数えるプログラムは以下のようになります。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="poedZjQ" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-1">
  <span>See the Pen <a href="https://codepen.io/likr/pen/poedZjQ">
  コンピューティング2 4-1</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

# 乱数の種

擬似乱数によって生成される数列を制御するためには乱数の **種** （seed）が用いられます。
Processing での乱数種の設定は `randomSeed` 関数を用います。
`randomSeed` 関数は引数として受け取った整数値を乱数種として設定します。

以下は乱数種を用いずに 5 個の実数値を表示するプログラムです。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="XWMzBmL" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-2">
  <span>See the Pen <a href="https://codepen.io/likr/pen/XWMzBmL">
  コンピューティング2 4-2</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

1 回目の実行例は以下の通りです。

```console
0.4314729699929136
0.49402074546951913
0.051202191546919185
0.08532266630981766
0.6955639919548684
```

2 回目の実行例は以下の通りです。

```console
0.6484318172616956
0.29618445984396824
0.756902001756752
0.771352089654068
0.1431124359660807
```

1 回目と 2 回目で実行結果が異なっていることが確認できます。
もちろん、実行するたびに結果は異なります。

以下のように `randomSeed` 関数で乱数の種を 0 に設定してみます。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="likr" data-slug-hash="QWpOByB" data-preview="true" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="コンピューティング2 4-3">
  <span>See the Pen <a href="https://codepen.io/likr/pen/QWpOByB">
  コンピューティング2 4-3</a> by Yosuke Onoue (<a href="https://codepen.io/likr">@likr</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

1 回目の実行結果は以下の通りです。

```console
0.23606797284446657
0.278566908556968
0.8195337599609047
0.6678668977692723
0.3840773708652705
```

2 回目の実行結果は以下の通りです。

```console
0.23606797284446657
0.278566908556968
0.8195337599609047
0.6678668977692723
0.3840773708652705
```

乱数種を同じにすることで 1 回目と 2 回目で同じ実行結果になりました。

一般的には、実行時の時間などを乱数種に利用することで実行毎に異なる結果を発生させます。
