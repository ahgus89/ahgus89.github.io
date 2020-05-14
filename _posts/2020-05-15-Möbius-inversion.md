---
layout: post
title:  "Möbius inversion"
summary: "gcd 관련 합을 빠르게"
date:   2020-05-15 12:40:00
categories: Algorithm
tags: 'Number-Theory'
author: ahgus89
---

이전 글에서 약수에 관한 몇몇 합을 효율적으로 계산하는 방법을 알아보았다. 

정수론에서 또 중요한 함수인 최대공약수, $gcd$에 관한 합 역시 자주 등장할 수 있는 형태이다. 이번 글에서는 이러한 합을 효율적으로 구하는 방법을 알아볼 것이다.

### Möbius inversion
*관심이 별로 없다면 이 부분은 결과만 알고 가도 괜찮습니다*

뫼비우스 함수 $\mu(n)$은 포함-배제의 원리를 서술하기 좋은 도구이다. 

함수 $f(n), g(n)$에 대해 다음 식이 성립한다고 하자. 

$$g(n) = \displaystyle \sum_{{d \vert n}} f(d)$$

$g(n)$을 알고 있을 때 $f(n)$은 어떻게 구할까? $g(n)$이 $f(d)$들의 합이니 $g(d)$를 "적절히" 더하고 빼면 될텐데, 그 적절함을 담당하는 것이 Möbius inversion이다. $f(n)$은 다음과 같은 식으로 구할 수 있다.

$$f(n) = \displaystyle \sum_{{d \vert n}} g(d) \mu({{n \over d}})$$

특히 다음 식이 성립한다.

$$\displaystyle \sum_{{d \vert n}} \mu(d) = \epsilon(n)$$

이 외에도 다음 식이 성립한다.

$$\displaystyle \sum_{{d \vert n}} \tau(d)\mu({{n \over d}}) = 1$$
$$\displaystyle \sum_{{d \vert n}} \sigma(d)\mu({{n \over d}}) = n$$
$$\displaystyle \sum_{{d \vert n}} d\mu({{n \over d}}) = \phi(n)$$

증명은 양변이 곱셈함수임을 바탕으로 $p^k$꼴에 대해서만 식이 성립함을 보이는 것으로도 충분하다.

### gcd와 합
$$f(n) = \displaystyle \sum_{{d \vert n}} g(d) \mu({{n \over d}})$$

이 식을 이용하면 다양한 $gcd$에 관한 합을 더 빠르게 계산할 수 있는 형태로 바꾸어 계산할 수 있다.

다음 문제를 풀어봅시다. 

> $1 \leq i \leq n, 1 \leq j \leq m, gcd(i, j)=1$인 $(i, j) 쌍의 개수는?

우리가 구해야 하는 값을 합의 형태로 나타내면 다음과 같다.

$$\displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m [gcd(i, j)=1]$$

* [대괄호 표기](https://ahgus89.github.io/algorithm/Notation/)에 헷갈리지 않도록 주의하자 * 

이 식을 naive하게 계산하면 $O(nm \log max(n, m))$ 정도가 걸린다. 더 빠르게 계산하는 법을 알아보자.

$[gcd(i, j)=1] = \epsilon(gcd(i, j))$이고, $\displaystyle \sum_{{d \vert n}} \mu(d) = \epsilon(n)$를 대입하면 다음과 같다.

$$\displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m \displaystyle \sum_{d \vert gcd(i, j)} \mu(d)$$

$gcd$의 정의에 의해, ${d \vert g}cd(i, j)$는 ${d \vert i}, {d \vert j}$와 동치이다. 따라서 식을 다음과 같이 변형할 수 있다.

$$\displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m \displaystyle \sum_{d=1}^{min(n, m)} \mu(d) [{d \vert i}] [{d \vert j}]$$

$d$가 앞에 오도록 순서를 바꾸면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m \mu(d) [{d \vert i}] [{d \vert j}]$$

${d \vert i}, {d \vert j}$인 경우만 $[{d \vert i}] [{d \vert j}]=1$이므로 식을 다음과 같이 변형할 수 있다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^{[{n \over d}]} \displaystyle \sum_{j=1}^{[{m \over d}]} \mu(d)$$

$\mu(d)$는 $i, j$에 대한 상수이므로 앞으로 꺼낼 수 있다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \mu(d) \displaystyle \sum_{i=1}^{[{n \over d}]} \displaystyle \sum_{j=1}^{[{m \over d}]} 1$$

최종적으로 다음과 같은 식을 계산하면 된다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \mu(d){[{n \over d}]}{[{m \over d}]}$$

[Linear sieve](https://ahgus89.github.io/algorithm/Linear_sieve/)를 이용하여 $\mu(d)$ 값을 미리 구하면 $O(min(n, m))$ 시간에 이 합을 구할 수 있다.

$\mu(d)$의 부분합을 $O(min(n, m))$ 시간에 전처리해두면, [Harmonic Lemma](https://ahgus89.github.io/algorithm/Harmonic-Lemma/)를 이용해 전처리 이후 $n, m$이 주어질 때 마다 $O(\sqrt n + \sqrt m)$ 시간에 구할 수 있다. ${[{n \over i}]}, {[{m \over i}]}$ 값이 모두 같은 최대의 $j$는 $min([{n \over [n/i]}}], [{m \over [m/i]}}])$임을 쉽게 알 수 있다.

이를 코드로 구현하면 다음과 같다.

<details markdown="1">
<summary>코드</summary>

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;
int n, m, k, ans;
const int mod=1e9+7;
const int sz=101010;
int sp[sz], mu[sz], psum[sz];
vector<int> p;
int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    int i, j, temp=0;
    mu[1]=1;
    for(i=2;i<sz;i++)
    {
        if(!sp[i]) mu[i]=-1, p.push_back(i);
        for(auto j:p)
        {
            if(i*j>=sz) break;
            sp[i*j]=j;
            if(i%j==0) break;
            mu[i*j]=mu[i]*mu[j];
        }
    }
    for(i=1;i<sz;i++)
        psum[i]=psum[i-1]+mu[i];

    int t;
    cin>>t;
    while(t--)
    {
        cin>>n>>m;
        ans=0;
        for(i=1;i<=min(n, m);i=j+1)
        {
            j=min(n/(n/i), m/(m/i));
            ans+=(n/i)*(m/i)*(psum[j]-psum[i-1]);
        }
        cout<<ans<<'\n';
    }
}
```

</details>

비슷하게, 일반적인 함수 $f(n)$에 대해 $\displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m f(gcd(i, j))$ 형태의 합을 계산할 수 있다.

역시 $gcd(i, j)$를 계속 계산하는 것은 무리가 있으므로, 합을 $gcd(i, j)=d$에 대해 구하는 것으로 생각하자.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m f(d)[gcd(i, j)=d]$$

$i, j$가 $d$의 배수인 경우만 합을 구하면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^{[{n \over d}]} \displaystyle \sum_{j=1}^{[{m \over d}]} f(d)[gcd(i, j)=1]$$

$f(d)$는 $i, j$에 대한 상수이므로 앞으로 꺼낼 수 있다.

$$\displaystyle \sum_{d=1}^{min(n, m)} f(d) \displaystyle \sum_{i=1}^{[{n \over d}]} \displaystyle \sum_{j=1}^{[{m \over d}]} [gcd(i, j)=1]$$

위에서 구한 서로소인 쌍의 개수 식을 대입하면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} f(d) \displaystyle \sum_{e=1}^{min([{n \over d}], [{m \over d}])} \mu(e) [{n \over de}][{m \over de}]$$

$de=k$로 치환하고, $k$를 앞으로 꺼내어 식을 다시 쓰면 다음과 같다. $e = {k \over d}$이다.

$$\displaystyle \sum_{k=1}^{min(n, m)} [{n \over k}][{m \over k}] \displaystyle \sum_{d \vert k} f(d)\mu({k \over d})$$

$g(n) = \displaystyle \sum_{d \vert n} f(d)\mu({k \over d})$은 $f(n)$의 Möbius inversion이다. $f(1)$~$f(n)$을 모두 구한 상황에서는(또는 $O(1)$에 값을 알 수 있는 경우는) $g(1)$~$g(n)$ 의 값을 $O(nlogn)$ 시간에 구해줄 수 있다. 간단하게 $d, e$를 기준으로 반복문을 돌며 $g(de)$의 값에 $f(d) \cdots \mu(e)$를 더해주면 된다.

$f(n)$이 곱셈함수인 경우, 위 과정은 $O(n)$만에 할 수 있다. $f(n)$이 곱셈함수이면 Möbius inversion인 $g(n)$ 역시 곱셈함수이므로, $g({p^k})$꼴의 식만 구하면 Linear sieve를 이용해 구해줄 수 있다. 뫼비우스 함수의 성질을 생각하면, $g({p^k}) = f({p^k})-f({p^{k-1}})$임을 쉽게 알 수 있다.

아무튼 전처리를 통해 $g(n)$의 값을 구했다면, 결국 $\displaystyle \sum_{k=1}^{min(n, m)} [{n \over k}][{m \over k}] g(k)$를 계산하는 것이므로 Harmonic Lemma를 이용해 $O(sqrt(n)+sqrt(m))$ 시간에 합을 구할 수 있다. 물론 전체 시간복잡도는 전처리로 인해 $O(min(n, m))$ 또는 $O(min(n, m)log min(n, m))$이다.

gcd에만 관한 함수가 아니더라도 그와 유사한 경우에 거의 같은 방법으로 식을 정리할 수 있는 경우도 있다. 최소공배수인 lcm이 그 예시이다. $\displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m lcm(i, j)$를 계산하고 글을 마치겠다. 계속해서 유사한 방식으로 식을 변형하니 이제는 이해하기 쉬울 것이다.

$lcm(i, j) = ij \over gcd(i, j)$를 대입하고, 역시 합을 $gcd(i, j)=d$에 대해 구하는 것으로 생각하자.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^n \displaystyle \sum_{j=1}^m {ij \over d}[gcd(i, j)=d]$$

$i, j$가 $d$의 배수인 경우만 합을 구하면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} \displaystyle \sum_{i=1}^{[{n \over d}]} \displaystyle \sum_{j=1}^{[{m \over d}]} dij[gcd(i, j)=1]$$

$d$는 $i, j$에 대한 상수이고, $i$는 $j$에 대한 상수이다. $[gcd(i, j)=1] = \displaystyle \sum_{{e \vert i}, {e \vert j}} \mu(e)$를 대입하면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} d \displaystyle \sum_{i=1}^{[{n \over d}]} i \displaystyle \sum_{j=1}^{[{m \over d}]} j \displaystyle \sum_{{e \vert i}, {e \vert j}} \mu(e)$$

$i=ep, j=eq$를 대입하고 $e$에 대한 합을 앞으로 꺼내면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} d \displaystyle \sum_{e=1}^{min([{n \over d}], [{m \over d}])} e^2 \mu(e) \displaystyle \sum_{i=1}^{[{n \over de}]} i \displaystyle \sum_{j=1}^{[{m \over de}]} j $$

$i, j$에 대한 합은 $\displaystyle \sum_{i=1}^{[{n \over de}]} i = {([{n \over de}])([{n \over de}]+1) \over 2}$, $\displaystyle \sum_{j=1}^{[{m \over de}]} j = {([{m \over de}])([{m \over de}]+1) \over 2}$ 이다. 대입하면 다음과 같다.

$$\displaystyle \sum_{d=1}^{min(n, m)} d \displaystyle \sum_{e=1}^{min([{n \over d}], [{m \over d}])} e^2 \mu(e) {([{n \over de}])([{n \over de}]+1) \over 2}{([{m \over de}])([{m \over de}]+1) \over 2}$$

역시 $k=de$로 치환하고, $k$를 앞으로 꺼내어 식을 다시 쓰면 다음과 같다. 이번에는 $d={k \over e}$이다.

$$ \displaystyle \sum_{k=1}^{min(n, m)} {([{n \over k}])([{n \over k}]+1) \over 2}{([{m \over k}])([{m \over k}]+1) \over 2} \displaystyle \sum_{e \vert k} ke \mu(e) $$

$\displaystyle \sum_{e \vert k} ke \mu(e)$가 어떤 함수인지 알 필요는 없다. 결국 저 함수는 곱셈함수이고, 따라서 $p^k$꼴에 대해서만 계산해주면 된다. 계산해보면 이 함수는 $f(p^k) = p^{k} - p^{k+1}$인 곱셈함수가 된다. 남은 부분은 Harmonic Lemma를 이용해 식을 빠르게 계산하는 것이고, 복잡도 등은 위의 경우와 같다.

한가지 아쉬운 점이 있다. 전처리를 해두면 쿼리당 $O(sqrt(MAX))$ 정도에 계산할 수 있다지만, 결국 전처리 과정의 시간, 공간 복잡도가 $O(MAX)$이다. 특히나 메모리 때문에 $n, m$이 $10^9$ 정도만 되어도 이 방법을 쓸 수 없다. 다음 글에서는 이 문제들을 $O(n^{2/3})$ 시간, 공간 복잡도를 가지도록 최적화하는 방법을 알아볼 것이다.

**연습문제**

[BOJ 11691 LCM(i, j)](https://www.acmicpc.net/problem/11691) - 위에서 설명한 것 그대로이다. 아니 심지어 $n=m$이다.

[BOJ 14860 GCD 곱](https://www.acmicpc.net/problem/14860) - Möbius inversion을 쓰지 않는 쉬운 풀이도 있다.

[BOJ 14861 LCM 더하기](https://www.acmicpc.net/problem/14861) - 식 변형 난이도가 조금 있다.

$\displaystyle \sum$ 개수가 여러개여도 완벽히 같은 방법으로 식을 변형할 수 있다.

[BOJ 13890 Big Bang](https://www.acmicpc.net/problem/13890)

구간이 꼭 1부터 시작하지 않아도 된다. 직접 식을 변형해보면 쉽게 알 수 있을 것이다.

[BOJ 16409 Coprime Integers](https://www.acmicpc.net/problem/16409)

[BOJ 1792 공약수](https://www.acmicpc.net/problem/1792)

[BOJ 14862 최대공약수 기댓값](https://www.acmicpc.net/problem/14862)

[BOJ 10916 Xtreme gcd sum](https://www.acmicpc.net/problem/10916) 

