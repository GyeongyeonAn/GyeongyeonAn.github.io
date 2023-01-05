---
layout: single
title:  "[알고리즘] 기초 개념"
---


## 1) 알고리즘이란?
- 알고리즘은 주어진 문제를 논리적으로 해결하는 과정이다.
- 분석을 통해 작성한 알고리즘의 정확성을 파악할 수 있고, 효율성을 정량적으로 나타낼 수 있다.
- 일반적으로 알고리즘은 프로그래밍 언어에 독립적이다.
- 바람직한 알고리즘
    - 명확해야 한다.
        - 이해하기 쉽고 가능하면 간단 명료 하도록
        - 지나친 기호적 표현은 오히려 명확성을 떨어뜨림
        - 명확성을 해치지 않으면 일반언어의 사용도 무방
    - 효율적이어야 한다.
        - 같은 문제를 해결하는 알고리즘들의 수행시간이 수백만 배 이상 차이가 날 수 있다.
- 정당성
    - 답을 출력할 대는 반드시 그 답이 정확할 것
    - 반드시 유한시간 내에 계산을 끝낼 것
- 효율 분석
    - 시간 복잡도 (time complexity)
        - 입력 크기(n)가 커질수록, 알고리즘의 수행시간
    - 공간 복잡도 (space complexity)


## 2) 알고리즘의 수행시간
``` c++
// n에 관계없이 상수 시간이 소요된다.
sample1(A[], n) 
{ 
    k = n/2; 
    return A[k];
}
```

``` c++
// n에 비례한 시간이 소요된다.
sample2(A[], n) 
{ 
    sum = 0; 
    for i = 1 to n:
        sum = sum + A[i];
    return sum;
} 
```

``` c++
// n*n에 비례한 시간이 소요된다.
sample3(A[], n) 
{ 
    sum = 0; 
    for i = 1 to n:
        for j = 1 to n: 
            sum = sum + A[i] * A[j]; 
    return sum;
}
```


## 3) 점근적 분석
- 입력의 크기가 충분히 큰 경우에 대한 분석
- 이미 알고 있는 점근적 개념의 예
- 입력의 크기 n이 커질 때의 알고리즘 성능
- 상수항 보다는 입력의 크기 n의 최고차수에 좌우
- Ο, Ω, Θ 표기법


### 점근적 표기법
- O(f(n)) : Tight or loose upper bound
![image](https://user-images.githubusercontent.com/55589616/210735263-7b9f80f3-192d-40c2-8e7b-be6caa015811.png)

- Ω(f(n)) : Tight or loose lower bound
![image](https://user-images.githubusercontent.com/55589616/210704567-39476ac0-46fa-48ab-bc4e-ad5f101fdb2f.png)

- Θ(f(n)) : Tight bound
![image](https://user-images.githubusercontent.com/55589616/210704661-0b196bcf-7373-436e-b82f-ed63baf47ec0.png)

