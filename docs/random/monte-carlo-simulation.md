# モンテカルロシミュレーション

**モンテカルロシミュレーション** は、乱数を用いた試行を十分な回数繰り返すことによってシステムの統計的な振る舞いを調べるシミュレーションの手法です。

以下は 2 個の 6 面ダイスを振って、出た目の合計値を調べるプログラムです。
これも一種のモンテカルロシミュレーションと見做すことができます。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const count = new Array(11);

  const repeat = 10000;
  for (let i = 0; i < repeat; ++i) {
    const a = int(random(1, 7));
    const b = int(random(1, 7));
    const sum = a + b;
    count[sum - 2] += 1;
  }

  for (let i = 0; i < count.length; ++i) {
    console.log(`${i + 2} : ${count[i]}`);
  }
}
```

実行結果は以下のようになります。

```console
2 : 261
3 : 569
4 : 879
5 : 1116
6 : 1422
7 : 1603
8 : 1377
9 : 1117
10 : 822
11 : 554
12 : 280
```

# モンティ・ホール問題

次のような問題を考えます。
3 つのドアがあり、そのうち 1 つのドアが当たりになっています。
プレイヤーは 3 つのドアから 1 つを選びます。
司会者は、プレイヤーがドアを選んだ後、残った２つのドアのうち外れのドアを１つ開きます。
司会者がドアを選んだ後、プレイヤーはドアを選び直すことができます。
ドアを選び直した場合とそうじゃない場合で当たりを引く確率に違いはあるでしょうか？
これは、**モンティ・ホール問題** と呼ばれる確率論の有名な問題です。

ドアを選び直さない場合当たりの確率は 1/3 ですが、ドアを選び直すと当たりの確率は 2/3 になります。
これをモンテカルロシミュレーションで確かめてみましょう。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const repeat = 10000;
  let count;

  count = 0;
  for (let i = 0; i < repeat; ++i) {
    const hit = int(random(0, 3));
    const answer = int(random(0, 3));
    if (hit == answer) {
      count += 1;
    }
  }
  println(count / repeat);

  count = 0;
  for (let i = 0; i < repeat; ++i) {
    const hit = int(random(0, 3));
    let answer = int(random(0, 3));
    if (hit == 0) {
      if (answer == 0) {
        if (random(1) < 0.5) {
          answer = 1;
        } else {
          answer = 2;
        }
      } else if (answer == 1) {
        answer = 0;
      } else {
        answer = 0;
      }
    } else if (hit == 1) {
      if (answer == 0) {
        answer = 1;
      } else if (answer == 1) {
        if (random(1) < 0.5) {
          answer = 0;
        } else {
          answer = 2;
        }
      } else {
        answer = 1;
      }
    } else {
      if (answer == 0) {
        answer = 2;
      } else if (answer == 1) {
        answer = 2;
      } else {
        if (random(1) < 0.5) {
          answer = 0;
        } else {
          answer = 1;
        }
      }
    }
    if (answer == hit) {
      count += 1;
    }
  }
  console.log(count / repeat);
}
```

# 円の面積の計算

モンテカルロシミュレーションの有名な応用として、円周率の近似があります。

原点(0, 0)を中心とした半径 1 の円の方程式は以下のように表されます。

$$
x^2 + y^2 = 1
$$

この円の面積は円周率と等しくなります。
ここで、この円の $$x \geq 0$$、$$y \geq 0$$ の領域 A を考えます。
A の面積は元の円の面積の 1/4 です。
また A は、(0, 0), (0, 1), (1, 0), (1, 1)を頂点とした一辺の長さが 1 の正方形で囲むことができます。
正方形の中の点をランダムに選んだとき、その点が A に含まれる確率は A の面積に等しくなります。
すなわち、$$0 \leq x \leq 1$$、$$0 \leq y \leq 1$$の範囲で乱数 $$(x, y)$$ を発生させ、その点が A に含まれた回数を数えることで A の面積を近似できます。
そして、A の面積を 4 倍することで元の円の面積、すなわち円周率を得ることができます。

これに基づいて Processing でプログラムを作成すると以下のようになります。

```javascript
function setup() {
  noLoop();
}

function draw() {
  const repeat = 100000;
  let count = 0;
  for (let i = 0; i < repeat; ++i) {
    const x = random(1);
    const y = random(1);
    if (x * x + y * y < 1) {
      count += 1;
    }
  }
  console.log((count / repeat) * 4);
}
```

実行結果は以下のようになります。

```console
3.14032
```

大体円周率に近い値を得ることができています。
