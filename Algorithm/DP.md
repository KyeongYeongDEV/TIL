# 동적계획법(DP)란?

- 큰 문제를 작은 문제로 나누고 , 그 작은 문제의 정답을 저장해서 재사용하는 방법
- 같은 문제를 반복해서 풀지 않게 해준다.

# 예시 : 피보나치 수열

## 1. 재귀 ( 비효율적인 방법)

- 중복 호출이 너무 많다
- 시간복잡도 O(2^n)

```java
int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```

## 2. DP ( Top-down 방식 : 재귀 + 메모이제이션)

- 이미 계산한 값은배열에 저장해두고, 다시 계산하지 않는다.
- 시간복잡도 O(n)

```java
int[] dp = new int[100];
int fib(int n) {
    if (n <= 1) return n;
    if (dp[n] != 0) return dp[n];
    return dp[n] = fib(n - 1) + fib(n - 2);
}
```

## 3. DP ( Bottom-up 방식 : 반복문 + 메모이제이션)

- 반복문 기반 ⇒ 스택오버플로우 위험 없다

```java
int fib(int n) {
    int[] dp = new int[n + 1];
    dp[0] = 0; dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n];
}

```

# Reference

https://m.blog.naver.com/ndb796/221233570962?recommendTrackingCode=2

[https://m.blog.naver.com/ndb796/221233586932?recommendTrackingCode=2](https://m.blog.naver.com/ndb796/221233586932?recommendTrackingCode=2)
