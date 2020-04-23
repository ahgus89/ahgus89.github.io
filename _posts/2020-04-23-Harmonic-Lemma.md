---
layout: post
title:  "Harmonic-Lemma"
summary: "O(n)에 해결하자!"
date:   2020-04-23 12:10:00
categories: Algorithm
tags: 'Number-Theory'
author: ahgus89
---

이번 글에서부터는 본격적으로 정수론에서의 다양한 "합"을 구해볼 것이다. 

### 조화수열?
자연수 $n$에 대해, 다음과 같은 수열 ${a_n}$이 있다.

$$a_i = [n \over i]$$

$[n \over i]$는 $n$ 이하의 $i$의 배수의 개수를 의미하기도 한다. 이 수열의 합을 구해보자.

항을 하나씩 계산해 더하는 것은 $O(n)$만에 아주 쉽게 할 수 있다. 더 빠르게 하는 방법을 알아보자.

$n=16$ 정도에서 $a_i$들을 나열해보자. ${16, 8, 5, 4, 3, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1}$ 이다.

이 수열은 $y=n \over x$의 그래프처럼 초반에 급격히 감소하고 점차 원만히 감소하는데, 정수부분만을 취하다 보니 작은 값에서 같은 값이 매우 많이 등장한다. 이 관찰을 구체적으로, 더 쓸모있게(?) 서술하면 다음과 같다.

수열 ${a_n}$의 서로 다른 값의 개수는 $2[\sqrt(n)]$ 종류 이하이다.

p.f. $[\sqrt(n)]$을 기준으로 두 부분으로 나누어 생각하자.
1. $1 \leq i \leq [\sqrt(n)]$ : $i$가 $[\sqrt(n)]$개 이므로 $a_i$ 역시 $[\sqrt(n)]$종류 이하이다.
2. $[\sqrt(n)]+1 \leq i \leq n$ : $a_i \leq [\sqrt(n)]$ 이므로 $a_i$는 총 $[\sqrt(n)]$종류 이하이다.
따라서 수열 ${a_n}$의 서로 다른 값의 개수는 $2[\sqrt(n)]$ 종류 이하이다.

위 사실을 바탕으로 생각해보면, 같은 값을 가지는 구간을 빠르게 알아내서 $[n \over i]$에 구간의 크기, 즉 개수를 곱해 더하면 될 거라는 생각을 할 수 있다. 이를 위해서는, $a_i$ 값이 같은 최대의 $j$만 알면 충분하다.

$[n \over x] = k$인 최대의 실수 $x$는 $n \over k$이므로, $[n \over j] = k$ 인 최대의 정수 $j$는 $[n \over k]$임을 쉽게 알 수 있다. 따라서 $[n \over j] = [n \over i]$인 최대의 정수 $j$는 $[n \over {[n/i]}]$ 이다.

이를 바탕으로 구현한 코드는 아래와 같다.

```cpp
ans=0;
for(i=1;i<=n;i=j+1)
{
	j=n/(n/i);
	ans+=n/i*(j-i+1);
}
```

$i$는 $O(\sqrt(n))$개의 값만을 가지므로, 시간복잡도는 $O(\sqrt(n))$이다.

함수 $f(n)$에 대해 $\displaystyle \sum_{i=1}^{n} f([n \over i])$ 역시 같은 방법으로 구할 수 있다. $[n/i]$ 대신 $f([n/i])$를 곱해주면 된다.

빠르게 부분합을 계산할 수 있는 함수 $g(n)$에 대해 $\displaystyle \sum_{i=1}^{n} f([n \over i])g(i)$ 역시 같은 방법으로 구할 수 있다. $(j-i+1)$을 곱하는 대신 $\displaystyle \sum_{k=1}^{j}g(k) - \displaystyle \sum_{k=1}^{i-1}g(k)$을 곱해주면 된다. 위의 경우는 $g(i) = 1$인 경우이다.


한 가지 자잘한 팁은, 어차피 $i \leq [\sqrt(n)]$ 까지는 같은 $a_i$값이 없으므로, naive하게 계산해주면 정수 연산 중에서 비용이 많이 드는 편인 나눗셈 연산의 횟수를 줄일 수 있어 유의미한 커팅이 된다. $n = 10^12$ 정도만 되어도 $10^6$번의 나눗셈 연산을 줄이는 셈이다.

**연습문제**
[BOJ 15897 잘못 구현한 에라토스테네스의 체](https://www.acmicpc.net/problem/15897)
[BOJ 17417 Optimization is Freaky Fun](https://www.acmicpc.net/problem/17417) (Hard)

### Double Counting

정수론에서 쓰는 함수는 약수나 소인수에 관련된 함수가 많다. 그렇기에 보통 소인수분해하여 그 값을 쉽게 구할 수 있다.

하지만 $\displaystyle \sum_{i=1}^{n} f(i)$ 꼴의 값을 구해야 한다면 어떨까? 보통 $i$를 소인수분해하여 그 값을 구하는 것은 $O(n\sqrt(n))$ 정도의 시간이 걸린다. 몇몇 함수들은 매번 소인수분해하는 과정 없이 이 합을 빠르게 구할 수 있다.

주된 아이디어는 **약수를 세는 것 보다 배수를 세는 게 쉽다**는 것 이다.

$n$의 약수들을 아는 것은 $n$을 소인수분해하는 과정이 필요하지만, $d$의 배수는 자명하게 $d, 2d, 3d, \ldots$ 임을 알기 때문이다. 

$\displaystyle \sum_{i=1}^{n} \tau(i)$를 구해보자. 역시 $\tau(i)$를 각각 구하는 것은 $O(n\sqrt(n))$ 시간이 걸린다.

위에서 이야기한대로, $i$의 약수 $d$의 개수를 세는 게 아닌, $d$의 배수 $i$를 세는 것으로 바꾸면 어떻게 될까? $[1, n]$ 범위에 있는 $d$마다 $[1, n]$ 범위에 있는 $i$의 개수를 각각 세서 더해주면 된다. $n$ 이하의 $d$의 배수의 개수는 $[n \over d]$이므로, 결국 $\displaystyle \sum_{i=1}^{n} \tau(i) = \displaystyle \sum_{d=1}^{n} [n \over d]$가 되어 위에서 이야기한 대로 $O(\sqrt(n))$ 시간에 구할 수 있다.

비슷하게 $\displaystyle \sum_{i=1}^{n} \sigma(i)$ 역시 구할 수 있다. 약수의 개수가 약수의 합으로 바뀌었으므로, 각 약수 $d$를 $[n \over d]$ 번 더하면 된다. $\displaystyle \sum_{d=1}^{n} d [n \over d]$를 계산하는 문제이고, 역시 위의 방법을 이용하면 $O(\sqrt(n))$ 시간에 구할 수 있다.

**연습문제**
[BOJ 2247 실질적 약수](https://www.acmicpc.net/problem/2247) 
[BOJ 1457 정확해](https://www.acmicpc.net/problem/1457) 
[BOJ 15106 Fear Factoring](https://www.acmicpc.net/problem/15106) 
