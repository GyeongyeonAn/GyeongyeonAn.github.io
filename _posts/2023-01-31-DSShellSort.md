---
layout: single
title:  "[자료구조] 쉘/병합/기수 정렬"
---


## 1) 쉘(Shell) 정렬
- 일정한 간격(Interval)으로 떨어져있는 자료들끼리 부분집합을 구성하고, 각 부분집합에 있는 원소들에 대해서 정렬을 수행하는 작업을 반복하면서 전체 원소들을 정렬하는 방법
- 전체 원소에 대해서 정렬을 수행하는 것보다 부분집합으로 나누어 정렬하게 되면 비교연산과 교환연산 감소


### 수행 과정
1. 쉘 정렬의 부분집합
    - Step 1 : 부분집합의 기준이 되는 간격을 매개변수 h에 저장
    - Step 2 : 한 단계가 수행될 때마다 h의 값을 감소시키고 쉘 정렬을 순환 호출
    - Step 3 : h가 1이 될 때까지 반복
2. 쉘 정렬의 성능은 매개변수 h의 값에 따라 달라짐
    - 정렬할 자료의 특성에 따라 매개변수 생성 함수를 사용
    - 일반적으로 사용하는 h의 값은 원소 개수의 1/2을 사용하고 한 단계 수행될 때마다 h의 값을 반으로 감소시키면서 반복 수행

![image](https://user-images.githubusercontent.com/55589616/216929438-27d71c0c-39e7-443a-ae2f-af6ceffb924b.png)

![image](https://user-images.githubusercontent.com/55589616/216929589-a2ffe3ef-de5b-473a-a63d-fb948738459e.png)

![image](https://user-images.githubusercontent.com/55589616/216929843-3935992e-4d73-4578-a3e7-a3c3d7e578b3.png)

![image](https://user-images.githubusercontent.com/55589616/216930035-d2fe5a37-b69a-44dd-96d9-c2cedd2bf4fe.png)


``` c++
shellSort(a[], n)
{
    interval = n; 
    while (interval >= 1) 
    {
        interval = interval / 2;
        for (i=0; i<interval; i=i+1)
            intervalSort(a[], i, n, interval);
    }
}
```


### 메모리 사용 공간
- n개의 원소에 대하여 n개의 메모리와 매개변수 h에 대한 저장공간 사용


### 연산 시간
- 비교 횟수 : 처음 원소의 상태에 상관없이 매개변수 h에 의해 결정
- 일반적인 시간 복잡도 : O(n^1.25)
- 쉘 정렬은 정렬의 시간 복잡도 O(n^2) 보다 개선된 정렬 방법


## 2) 병합(Merge) 정렬
- 일정한 여러 개의 정렬된 자료의 집합을 병합하여 한 개의 정렬된 집합으로 만는 방법
- 부분집합으로 분할(Divide)하고, 각 부분집합에 대해서 정렬 작업을 완(Conquer)한 후에 정렬된 부분집합들을 다시 결합(Combine)하는 분할 정복(Divide and Conquer) 기법 사용
- 2-way 병합
    - 2개의 정렬된 자료의 집합을 결합하여 하나의 집합으로 만드는 병합 방법
- n-way 병합
    - n개의 정렬된 자료의 집합을 결합하여 하나의 집합으로 만드는 병합 방법


### 수행 과정
1. 분할(Divide) : 
- 입력 자료를 같은 크기의 부분집합 2개로 분할함
2. 정복(Conquer) : 
- 부분집합의 원소들을 정렬함
- 부분집합의 크기가 충분히 작지 않으면 순환 호출을 이용하여 다시 분할 정복 기법을 적용함
3. 결합(Combine) :
- 정렬된 부분집합들을 하나의 집합으로 결합함

![image](https://user-images.githubusercontent.com/55589616/216932995-903a4010-798e-47d7-85ec-0987774803c9.png)

![image](https://user-images.githubusercontent.com/55589616/216933140-5e1e9819-b372-498c-8ce4-f9aeeaf5d61e.png)


``` c++
mergeSort(a[], m, n)
{
    if (a[m:n]의 원소수 > 1)
    {
        전체집합을 두 개의 부분집합으로 분할;
        mergeSort(a[], m, middle);
        mergeSort(a[], middle+1, n);
        Merge(a[m:middle], a[middle+1:n]);
    }
}
```


### 메모리 사용 공간
- 각 단계에서 새로 병합하여 만든 부분집합을 저장할 공간이 추가로 필요
- 원소 n개에 대해서 (2xn)개의 메모리 공간 사용


### 연산 시간
- 분할 단계 : n개의 원소를 분할하기 위해서 lgn번의 단계 수행
- 병합 단계 : 부분집합의 원소를 비교하면서 병합하는 단계에서 최대 n번의 비교연산 수행
- 전체 병합 정렬의 시간 복잡도 : O(nlgn)


## 3) 기수(Radix) 정렬
- 일정한 원소의 키 값을 나타내는 기수를 이용한 정렬 방법
- 정렬할 원소의 키 값에 해당하는 버킷(Bucket)에 원소를 분배하였다가 버킷의 순서대로 원소를 꺼내는 방법을 반복하면서 정렬
- 원소의 키를 표현하는 기수만큼의 버킷 사용


### 수행 과정
- 키 값의 일의 자리 -> 십의 자리 -> 백의 자리에 대해서 기수 정렬을 수행함
- 한 단계가 끝날 때마다 버킷에 분배된 원소들을 버킷의 순서대로 꺼내서 다음 단계의 기수 정렬을 수행해야 하므로 큐를 사용하여 버킷을 만듦

![image](https://user-images.githubusercontent.com/55589616/216934681-55d56960-4629-43ed-b5ad-617564a62670.png)

![image](https://user-images.githubusercontent.com/55589616/216934826-df8e13d3-3b84-4c7f-a935-2f869e408015.png)

![image](https://user-images.githubusercontent.com/55589616/216935120-5763417e-4e9a-404b-9a09-07169bba1244.png)

![image](https://user-images.githubusercontent.com/55589616/216935339-ae2da5bf-c923-4a59-8e47-0580611cd455.png)

![image](https://user-images.githubusercontent.com/55589616/216935513-a3f967bf-c5b1-4231-9a22-812f17ba59d4.png)


``` c++
radixSort(a[], n)
{
    for (k=1; k<=n; k++)
    {
        for (i=0; i<n; i++)
        {
            K번째 자릿수 값에 따라서 해당 버킷에 저장한다.
            enQueue(Q[k].a[i]);
        }
        p = -1;
        for (i=0; i<=9; i<=i+1)
        {
            while (Q[i] != NULL)
            {
                p++;
                a[p] = deQueue(Q[i]);
            }
        }
    }
}
```


### 메모리 사용 공간
- 원소 n개에 대해서 n개의 메모리 공간 사용
- 기수 r에 따라 버킷 공간이 추가로 필요


### 연산 시간
- 연산 시간은 정렬할 원소의 수 n과 키 값의 자릿수 d와 버킷의 수를 결정하는 기수 r에 따라서 달라짐
    - 정렬할 원소 n개를 r개의 버킷에 분배하는 작업 : (n+r)
    - 이 작업을 자릿수 d만큼 반복
- 수행할 전체 작업: d(n+r)
- 시간 복잡도 : O(d(n+r))