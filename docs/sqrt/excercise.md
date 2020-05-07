# 演習

## 演習 1

以下の線形探索法のプログラムを完成させましょう。

```java
float e = 0.0001;
float n = 2;
float x = 0;

// ここを考える

println("x = " + x);
```

## 演習 2

以下の二分法のプログラムを完成させましょう。

```java
float e = 0.0001;
float n = 2;
float x0 = 1;
float x1 = 2;
float x;

do {
  // ここを考える
} while (x1 - x0 > e);

println("x = " + x);
```

## 演習 3

以下のニュートン法のプログラムを完成させましょう。

```java
float e = 0.0001;
float n = 2;
float x = n;

while ((x - e) * (x - e) > n) {
  // ここを考える
}

println("x = " + x);
```
