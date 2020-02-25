---
layout: post
title:  "Linear-sieve"
summary: "Linear-sieve"
date:   2020-02-25 14:40:00
categories: Algorithm
tags: 'Number-Theory'
author: ahgus89
---

본 글은 [Codeforces 블로그 글](https://codeforces.com/blog/entry/54090)을 많이 참고하여 작성된 글이다.

영어가 된다면 원본 글도 읽어보면 좋다.

*이 글에서 쓰이는 $p$는 별 말이 없으면 소수를 나타낸다.*

### 소수 판별
$1$부터 $n$까지, 각 수가 소수인지 아닌지 구하고 싶다. 이 문제를 해결하는 간단하고도 빠른 알고리즘으로는 에라토스테네스의 체가 잘 알려져 있다. 

> $i$를 $2$부터 $n$까지 증가시키면서, $i$가 합성수가 아니라면 소수이고, 그 때 $i$의 배수들은 합성수이다.

이 내용을 그대로 구현하면 다음과 같은 코드를 얻는다.

```cpp
bool isp[sz];
void era(int n)
{
	memset(isp, 1, sizeof isp);
	for(int i=2;i<=n;i++)
		if(isp[i])
			for(int j=2;i*j<=n;j++)
				isp[i*j]=0;
}
```

시간복잡도는 어떻게 될까? $i$가 소수일 때 마다 $n/i$번 내부 반복문을 돌게 된다.

$O(\sum_{p < n} n/p) = O(n \log \log n)$임이 알려져 있고, 따라서 시간복잡도는 O(n \log \log n)이 된다.

### 시간복잡도의 개선
해봐야 크기 $n$짜리 배열을 채우는데 $O(n)$에 할 수는 없을까?

에라토스테네스의 체가 $O(n)$이 아닌 이유는, $2 \times 3 \times 5 \times 7$ 같은 수를 생각해 보자.

> $2$가 소수이므로 $2$의 배수인 $2 \times (3 \times 5 \times 7)$은 합성수이다.
> $3$가 소수이므로 $3$의 배수인 $3 \times (2 \times 5 \times 7)$은 합성수이다.
> $5$가 소수이므로 $5$의 배수인 $5 \times (2 \times 3 \times 7)$은 합성수이다.
> $7$가 소수이므로 $7$의 배수인 $7 \times (2 \times 3 \times 5)$은 합성수이다.

이와 같이 소인수가 많은 합성수는 여러번 합성수라고 판별되고, 중복 판별 때문에 $O(n)$보다 느린 것이다.

중복을 피하는 방법은 뭐가 있을까? $n$의 적당한 소인수 $p$가 존재하여 $n=ip$라 하면, $p \times i$가 합성수라고 딱 한번만 판별해주고, 그 외의 다른 소인수들에 대해서는 $n$이 합성수라고 판별하지 않으면 가능할 것 같다.

$i$에 따라 $p$를 변화시켜보자. 지금까지 구한 소수들을 보면서, $p$가 특정 조건을 만족하지 않는 순간 반복을 멈추면 될 것이다. 이런 조건을 만족하는 좋은 소인수는 **최소소인수** 이다. 

즉, $2i, 3i, 5i, 7i, ...$를 보다가, $p$가 $i \times p$의 최소소인수가 아닌 순간 판별을 멈추면 된다. $i$의 최소소인수는 $p$ 이상이어야 하므로, $p \mid i$일 때 판별을 하고 나면, 그 다음부터는 고르는 소수가 $i$의 최소소인수보다 크게 되므로 $ip$의 최소소인수가 될 수는 없다. 따라서 판별을 종료해주면 된다.

이렇게 되면, 모든 합성수에 대해서 딱 한번씩만(최소소인수는 유일하니까) 판별을 해주게 되고, $O(n)$의 시간복잡도를 얻을 수 있다. 과정을 요약하면 다음과 같다.

> i: 2 ~ n
> i가 합성수가 아니면 소수 목록에 i를 추가한다.
> 소수 목록에 있는 j에 대해, ij는 합성수이고, j \mid i이면 멈춘다.

역시 그대로 구현하면 간단한 코드를 얻는다.

```cpp
vector<int> p; //소수 목록
bool isp[sz];
void linear_sieve(int n)
{
	memset(isp, 1, sizeof isp);
	for(int i=2;i<=n;i++)
	{
		if(isp[i]) p.push_back(i);
		for(auto j: p)
		{
			if(i*j>n) break;
			isp[i*j]=0;
			if(i%j==0) break;
		}
	}
}
```
에라토스테네스의 체에 비해 유의미하게 빠르게 작동한다.

그리고 소수가 담긴 벡터를 덤으로 얻을 수 있다.

이 과정을 생각해보면, 모든 합성수에 대해서 최소소인수를 바로 알아낼 수 있다. $ij$의 최소소인수는 $j$이다. 

```cpp
vector<int> p; 
int sp[sz];
void linear_sieve(int n)
{
	for(int i=2;i<=n;i++)
	{
		if(!sp[i]) p.push_back(i);
		for(auto j: p)
		{
			if(i*j>n) break;
			sp[i*j]=j;
			if(i%j==0) break;
		}
	}
}
```
다음과 같이 코드를 수정하면, $sp[i]$는 $i$가 소수이면 $0$, 아니면 최소 소인수를 담고 있다.

이를 통해 작은 수를 여러 번 소인수분해 하는 것을 쉽게 할 수 있다. $sp[i]$가 0이 아닐 때 까지 $i$를 $sp[i]$로 나누어주면 된다. [이 문제](https://www.acmicpc.net/problem/16563)를 통해 연습해보자.

<details markdown="1">
<summary>정답 코드</summary>

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;
int n, m, k, ans;
const int mod=1e9+7;
vector<int> p;
const int sz=5050505;
int sp[sz];
int main()
{
	ios_base::sync_with_stdio(false);
	cin.tie(0);
	int i, j, temp=0;
	for(i=2;i<sz;i++)
	{
		if(!sp[i]) p.push_back(i);
		for(auto j:p)
		{
			if(i*j>=sz) break;
			sp[i*j]=j;
			if(i%j==0) break;
		}
	}
	cin>>n;
	while(n--)
	{
		cin>>i;
		while(sp[i]) cout<<sp[i]<<' ', i/=sp[i];
		cout<<i<<'\n';
	}
}
```

</details>

### multiplicative function의 계산
Linear sieve는 훨씬 강력한 기능을 할 수 있다. 소수 판별은 덤에 가깝다.
특히 소수를 채우는 것 뿐만 아니라 다양한 정수론적 함수들을 $O(n)$에 계산할 수 있다.

곱셈함수나 몇몇 함수의 정의와 성질에 대해서는 [이전 글](https://ahgus89.github.io/algorithm/Notation/)을 참고하도록 하고, 그러면 이런 함수들을 어떻게 구할지 생각해보자. 

곱셈함수 $f(n)$에 대해 $f(i)$를 전부 채워넣고 싶다. 어떻게 하면 좋을까? 쉬운 경우부터 생각해보자. 

$f(1) = 1$이고, $f(p)$는 보통 $O(1)$에 계산된다.

합성수의 경우는? 서로소인 $n, m$에 대해 $f(nm) = f(n)f(m)$이니 이런 $n, m$만 찾으면 이전에 구한 정보를 가지고 역시 $O(1)$에 계산이 될 것이다. 

위에서 사용한 Linear sieve를 잘 살펴보면, $n=ip$에서 $p \nmid i$이면 당연히 $gcd(i, p)=1$이므로 $f(n) = f(i)f(p)$로 $O(1)$에 계산해줄 수 있다. $p \mid i$인 경우도 $p$가 소수이기 때문에 대부분 간단한 관계식이 성립한다.

예를 들어, $\phi(ip) = p\phi(i)$, $\mu(ip) = 0$ 같은 간단한 관계식이 성립한다. 따라서 $\phi(n)$이나 $\mu(n)$도 $O(n)$에 전부 구해줄 수 있다.

물론 모든 함수가 $p$와 $i$만의 관계식으로 표현되지는 않는다. $\tau(n)$이나 $\sigma(n)$의 경우 $p$에 대한 지수에 의존한다. 이런 경우는 간단하게 그 지수 역시 구해주면 된다. $p \nmid i$ 이면 지수는 $e_n=1$이고, $p \mid i$ 이면 지수는 $e_n=e_i + 1$이다. 

이렇게 구한 지수를 바탕으로 $p \mid i$일 때 $\tau(n) = \tau(i) \times (e_n + 1) \over e_n$, $\sigma(n) = \sigma(i) \times (p^{e_n + 1}-1) \over (p^{e_n}-1)$의 관계식을 이용해 계산해줄 수 있다. 다음은 $\phi(n), \mu(n), \tau(n), \sigma(n)$을 계산하는 코드이다.

<details markdown="1">
<summary>코드</summary>

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<int, int> pii;
int n, m, k, ans, mod=1e9+7;
int pw(int a, int b)
{
	int ret=1;
	while(b)
	{
		if(b&1) ret*=a;
		a*=a;
		b>>=1;
	}
	return ret;
}

vector<int> p;
const int sz=101010;
int sp[sz], e[sz], phi[sz], mu[sz], tau[sz], sigma[sz];

int main()
{
	ios_base::sync_with_stdio(false);
	cin.tie(0);
	int i, j, temp=0;
	phi[1]=mu[1]=tau[1]=sigma[1]=1;
	for(i=2;i<sz;i++)
	{
		if(!sp[i])
		{
			p.push_back(i);
			e[i]=1;
			phi[i]=i-1;
			mu[i]=-1;
			tau[i]=2;
			sigma[i]=i+1;
		}
		for(auto j:p)
		{
			if(i*j>=sz) break;
			sp[i*j]=j;
			if(i%j==0)
			{
				e[i*j]=e[i]+1;
				phi[i*j]=phi[i]*j;
				mu[i*j]=0;
				tau[i*j]=tau[i]/e[i*j]*(e[i*j]+1);
				sigma[i*j]=sigma[i]*(j-1)/(pw(j, e[i*j])-1)*(pw(j, e[i*j]+1)-1)/(j-1);//overflow
				break;
			}
			e[i*j]=1;
			phi[i*j]=phi[i]*phi[j];
			mu[i*j]=mu[i]*mu[j];
			tau[i*j]=tau[i]*tau[j];
			sigma[i*j]=sigma[i]*sigma[j];
		}
	}
	for(i=2;i<sz;i++)
		cout<<i<<' '<<e[i]<<' '<<phi[i]<<' '<<mu[i]<<' '<<tau[i]<<' '<<sigma[i]<<'\n';
}
```

</details>

이렇게 Linear sieve는 $O(n)$에 $1$부터 $n$ 까지의 소수 판별, 곱셈함수 $f(n)$ 구하기를 해낼 수 있는 강력한 도구이다. 특히 곱셈함수가 아니더라도, 비슷하게 $i$와 $p$의 관계에 따라 $f(ip)$를 계산할 수만 있다면 역시 Linear sieve를 사용할 수 있다. (위에서 이야기한 최소소인수의 지수가 그 예시이다) 몇몇 정수론 문제는 미리 함수값들을 저장해두면 문제를 쉽게 풀 수 있는 경우가 많으니, Linear sieve를 익혀두면 도움이 될 것이다.