---
layout: single
title:  "[자료구조] 재귀호출 (Recursion)"
---


## 1) 재귀호출
- 자기 자신을 호출하여 순환 수행되는 것
- 작업의 특성에 따라 재귀호출 방식을 사용하여 함수를 만들면 프로그램의 크기를 줄이고 간단하게 작성할 수 있음
- Base Case : 재귀 호출의 루프 탈출 조건
    - 재귀 호출은 반드시 Base Case에 도달해야 함
- 대표적인 분할 정복 알고리즘
    1. 문제의 크기 N
    2. 큰 문제를 작은 문제로 환원
    3. 작은 문제 역시 큰 문제와 유사함
    4. Self Call, Boomerang


## 2) 이진 탐색

``` c++
BinarySearch(SearchRange)
{ 
    if (One Page)
        Scan Until Found; 
    else 
    { 
        Unfold the Middle Page;
        Determine Which Half;
        if First Half
            BinarySearch(First Half);
        else 
            BinarySearch(Second Half);
    } 
}
```


## 3) 팩토리얼(Factorial) 연산

``` c++
int Factorial(int n) 
{ 
    if (n == 1)
        return 1; 
    else 
        return(n * Factorial(n-1)); 
}
```

![image](https://user-images.githubusercontent.com/55589616/212863337-b85a61be-89ff-4e5c-92d5-4dd1569bf14f.png)


## 4) 문자열 뒤집기

``` c++
void Reverse(char S[], int First, int Last)
{ 
    if (First > Last) 
        return;
    else
    { 
        printf("%c", S[First]);
        Reverse(S, First+1, Last); 
    } 
}
```

![image](https://user-images.githubusercontent.com/55589616/212864119-dd4f3295-17e2-4938-9c8a-415ced138fa1.png)


## 5) 피보나치 수열
- F(n) = F(n-1) + F(n-2)
- 단, F(0) = 0, F(1) = 1

``` c++
int Fibonacci(int n) 
{ 
    if (n < 2)
        return 1;
    else 
        return (Fibonacci(n-1) + Fibonacci(n-2));
}
```

![image](https://user-images.githubusercontent.com/55589616/212864766-2aa01024-b5ec-4cc1-a60f-69606e919387.png)


## 6) 분할 기법
- 문제 : 10, 7, 2, 8, 3, 1, 9, 6 이라는 숫자 중에서 세 번째 작은 수는?
- 문제 풀이
    1. 10, 7, 2, 8과 3, 1, 9, 6으로 분할
    2. 10, 7, 2, 8 중 세 번째 작은 수는 8
    3. 3, 1, 9, 6 중 세 번째 작은 수는 6
    4. 작은 문제의 해결책이 큰 문제의 해결책으로 이어지지 않는다.


### 분할(Partition)

![image](https://user-images.githubusercontent.com/55589616/212868546-fade9b1d-a7c8-4d52-9556-49ca59636a6b.png)

![image](https://user-images.githubusercontent.com/55589616/212868671-ae20b567-05ca-442d-9d98-6277b6004d94.png)

- 분할 방식
    - 피벗보다 작은 것은 왼쪽으로, 피벗보다 큰 것은 오른쪽으로
    - 전체 데이터가 정렬된 상태는 아님
    - 모든 데이터가 정렬되어도 피벗 위치는 불변
    - K가 4라면 네 번째 작은 수를 이미 찾은 것임

![image](https://user-images.githubusercontent.com/55589616/212871640-bcba1966-5e23-4ea2-8f15-244cb1db437d.png)

- 세 번째 작은 수 찾기
    - 분할된 왼쪽에 대해서 다시 파티션을 가함(결과 p=2)

![image](https://user-images.githubusercontent.com/55589616/212871825-6de2001b-3c7a-4acb-95a3-316eef877cf3.png)

    - 분할된 오른쪽에 대해서 다시 파티션을 가함: Self-swap(결과 p=3)


## 7) 재귀 함수의 작성
- **Step 1**
    - 더 작은 문제로 표시할 수 있는지 시도
        - 문제 크기를 하나씩 줄이는 방법
        - 반으로 줄이는 방법
        - 다른 여러 개의 작은 문제의 조합으로 표시하는 방법
    - 문제 크기 파라미터 n을 확인

- **Step 2**
    - 문제를 직접 풀 수 있는 것이 어떤 경우인지 베이스 케이스 확인

- **Step 3**
    - n이 줄어서 반드시 베이스 케이스를 만나는지 확인
    - n이 양수인지 음수인지, 짝수인지 홀수인지, 또는 부동소수인지 정수인지 모든 경우에 대해 모두 검증

- **Step 4**
    - 베이스 케이스와 베이스 케이스가 아닌 경우를 나누어서 코드를 작성


## 8) 재귀 호출의 효율성
- 순환(Recursion) : 순환 호출 이용
    - 순환적인 문제에서는 자연스러운 방법
    - 함수 호출의 오버 헤드
- 반복(Iteration) : for이나 while을 이용한 반복
    - 수행 속도가 빠름
    - 순환적인 문제에 대해서는 프로그램 작성이 아주 어려울 수도 있음