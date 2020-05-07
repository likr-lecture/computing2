# 演習の解答例

## 演習 1

```java
float e = 0.0001;
float n = 2;
float x = 0;

while ((x + e) * (x + e) < n) {
  x += e;
}

println("x = " + x);
```

## 演習 2

```java
float e = 0.0001;
float n = 2;
float x0 = 1;
float x1 = 2;
float x;

do {
  x = (x0 + x1) / 2;
  if (x * x > n) {
    x1 = x;
  } else {
    x0 = x;
  }
} while (x1 - x0 > e);

println("x = " + x);
```

## 演習 3

```java
float e = 0.0001;
float n = 2;
float x = n;

while (n < (x - e) * (x - e)) {
  x = (x * x + n) / (2 * x);
}

println("x = " + x);
```
