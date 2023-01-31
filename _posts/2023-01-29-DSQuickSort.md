---
layout: single
title:  "[자료구조] 퀵/삽입/히프/트리 정렬"
---


## 1) 퀵 정렬
- 정렬할 전체 원소에 대해서 정렬을 수행하지 않고, 기준 값을 중심으로 왼쪽 부분 집합과 오른쪽 부분집합으로 분할하여 정렬하는 방법
- 왼쪽 부분집합에는 기준 값보다 작은 원소들을 이동시키고, 오른쪽 부분집합에는 기준 값보다 큰 원소들을 이동시킴
- 기준 값: 피봇(Pivot)


### 수행 과정
1. 분할(Divide)
- 정렬할 자료들을 기준 값을 중심으로 2개의 부분집합으로 분할하기
- 부분집합으로 분할하기 위해서 L과 R을 사용
    - Step 1 : 왼쪽 끝에서 오른쪽으로 움직이면서 크기를 비교하여 피봇 보다 크거나 같은 원소를 찾아 L로 표시함
    - Step 2 : 오른쪽 끝에서 왼쪽으로 움직이면서 피봇 보다 작은 원소를 찾아 R로 표시함
    - Step 3 : L이 가리키는 원소와 R이 가리키는 원소를 서로 교환함
2. 정복(Conquer)
- 부분집합의 원소들 중에서 기준 값보다 작은 원소들은 왼쪽 부분집합으로, 기준 값보다 큰 원소들은 오른쪽 부분집합으로 정렬하기
- 부분집합의 크기가 1 이하로 충분히 작지 않으면 순환 호출을 이용하여 다시 분할
    - Step 1 : L과 R이 만나게 되면 피봇과 R의 원소를 서로 교환하고, 교환한 위치를 피봇의 위치로 확정함
    - Step 2 : 피봇의 확정된 위치를 기준으로 만들어진 새로운 왼쪽 부분집합과 오른쪽 부분집합에 대해서 퀵 정렬을 순환적으로 반복 수행함
    - Step 3 : 모든 부분집합의 크기가 1 이하가 되면 퀵 정렬을 종료함

![image](https://user-images.githubusercontent.com/55589616/215674738-bb60381e-3d50-4563-bc12-cdddc8aeba84.png)

![image](https://user-images.githubusercontent.com/55589616/215674832-109024e0-c8bb-4316-a7a1-b17f9de4db86.png)

![image](https://user-images.githubusercontent.com/55589616/215674988-5c263e8d-2e44-4fc3-8ec0-e21230c094d0.png)

![image](https://user-images.githubusercontent.com/55589616/215675173-78983da7-4694-4a8c-9487-7e1efb5eba31.png)

![image](https://user-images.githubusercontent.com/55589616/215675228-e6433d19-495a-4c6e-b8c2-070cffef10af.png)

![image](https://user-images.githubusercontent.com/55589616/215675319-b630a8aa-6f07-48e6-8ec0-7753619a00f3.png)

![image](https://user-images.githubusercontent.com/55589616/215675392-4fe5c23f-7691-4160-a140-3629d589a414.png)

![image](https://user-images.githubusercontent.com/55589616/215675452-70adc1b4-3959-4d9e-b562-62efd8f9ecd4.png)


``` c++
partition(a[], begin, end)
{
    pivot = (begin + end) / 2;
    L = begin;
    R = end;
    while(L < R)
    {
        while (a[L] < a[pivot] && L < R)   L++;
        while (a[R] < a[pivot] && L < R)   R--;
        if(L < R)   // L의 원소와 R의 원소 교환
        {
            temp = a[L];
            a[L] = a[R];
            a[R] = temp;
        }
    }
    temp = a[pivot];    // R의 원소와 피봇 원소 교환
    a[pivot] = a[R];
    a[R] = temp;
    return R;
}
```


### 메모리 사용 공간
- n개의 원소에 대하여 n개의 메모리 사용


### 연산 시간
- 최선의 경우 :
    - 피봇에 의해서 원소들이 왼쪽 부분집합과 오른쪽 부분집합으로 정확히 n/2개씩 이등분이 되는 경우가 반복되어 수행 단계 수가 최소가 되는 경우
- 최악의 경우 :
    - 피봇에 의해 원소들을 분할하였을 때 1개와 n-1개로 한쪽으로 치우쳐 분할되는 경우가
    - 반복되어 수행 단계 수가 최대가 되는 경우
- 평균 시간 복잡도 : O(nlgn)
- 같은 시간 복잡도를 가지는 다른 정렬 방법에 비해서 자리 교환 횟수를 줄임으로써 더 빨리 실행되어 실행 시간 성능이 좋은 정렬 방법임


## 2) 삽입 정렬
- 정렬되어있는 부분집합에 정렬할 새로운 원소의 위치를 찾아 삽입하는 방법
- 정렬할 자료를 두 개의 부분집합 S와 U로 가정
    - 부분집합 S : 정렬된 앞부분의 원소들
    - 부분집합 U : 아직 정렬되지 않은 나머지 원소들
- 정렬되지 않은 부분집합 U의 원소를 하나씩 꺼내서 이미 정렬되어 있는 부분집합 S의 마지막 원소부터 비교하면서 위치를 찾아 삽입
- 삽입 정렬을 반복하면서 부분집합 S의 원소는 하나씩 늘리고 부분집합 U의 원소는 하나씩 감소하게 함. 
- 부분집합 U가 공집합이 되면 삽입 정렬이 완성됨


### 수행 과정
![image](https://user-images.githubusercontent.com/55589616/215676540-56eb6850-ef39-461d-995c-dc1c22c67f91.png)

![image](https://user-images.githubusercontent.com/55589616/215676687-02f29861-fe71-4037-b9df-c50d0febe117.png)

![image](https://user-images.githubusercontent.com/55589616/215676752-860f7e88-6dfa-4d38-b900-1a463db58abb.png)

.....

![image](https://user-images.githubusercontent.com/55589616/215677057-ea64a9f2-61ee-4aab-999c-2b36dd5e5fd1.png)


``` c++
insertionSort(a[], n)
{
    for (int i=1; i<n; i++) 
    {
        temp = a[i];
        j = i;
        if (a[j=1] > temp)  move = true;
        else move = false;
        while (move)
        {
            a[j] = a[j–1];
            j--;
            if (j>0 && a[j-1] > temp)   move = true;
            else move = false;
        }
    }
    a[j] = temp; 
}
```


### 메모리 사용 공간
- n개의 원소에 대하여 n개의 메모리 사용


### 연산 시간
- 최선의 경우 :
    - 원소들이 이미 정렬되어 있어서 비교 횟수가 최소인 경우
    - 이미 정렬되어있는 경우에는 바로 앞자리 원소와 한번만 비교함
    - 전체 비교 횟수 = n-1 
    - 시간 복잡도: O(n)
- 최악의 경우 :
    - 모든 원소가 역순으로 되어있어서 비교 횟수가 최대인 경우
    - 전체 비교 횟수 = 1 + 2 + 3 + ⋯ + (n-1) = n(n-1)/2
    - 시간 복잡도 : O(n^2)
    - 삽입 정렬의 평균 비교 횟수 = n(n-1)/4
    - 평균 시간 복잡도 : O(n^2)


## 3) 히프 정렬
- 히프 자료구조를 이용한 정렬 방법
- 히프에서는 항상 가장 큰 원소가 루트 노드가 되고 삭제 연산을 수행하면 항상 루트 노드의 원소를 삭제하여 반환
    - 최대 히프에 대해서 원소의 개수만큼 삭제 연산을 수행하여 내림차순으로 정렬 수행
    - 최소 히프에 대해서 원소의 개수만큼 삭제 연산을 수행하여 오름차순으로 정렬 수행


### 수행 과정
![image](https://user-images.githubusercontent.com/55589616/215678345-0b6628c4-5ffe-416b-8bdc-26604cc5a71c.png)

![image](https://user-images.githubusercontent.com/55589616/215679076-767995a9-c5b5-4a82-86f3-cfb5521090c6.png)

![image](https://user-images.githubusercontent.com/55589616/215679129-8f234db2-3396-461b-a3a3-2bd2091dfa38.png)

...

![image](https://user-images.githubusercontent.com/55589616/215679208-08656601-3ef6-4625-94fa-01f92c5a6163.png)


### 메모리 사용 공간
- 원소 n개에 대하여 n개의 메모리 공간 사용
- 크기 n의 히프 저장 공간 사용


### 연산 시간
- 히프 재구성 연산 시간 :
    - 완전 이진 트리를 히프로 구성하는 평균 시간은 O(lgn)
    - n개의 노드에 대해서 n번의 히프 재구성 작업 수행
- 평균 시간 복잡도 : O(nlgn)


### 4) 트리 정렬
- 이진 탐색 트리를 이용하여 정렬하는 방법
- 중위 우선 순회


### 수행 과정
1. 정렬할 원소들을 이진 탐색 트리로 구성함
2. 이진 탐색 트리를 중위 우선 순회함 중위 순회 경로가 오름차순 정렬이 됨

![image](https://user-images.githubusercontent.com/55589616/215679924-3b3f83dc-d5fc-4c19-80d7-18c500d03cab.png)


### 메모리 사용 공간
- 원소 n개에 대해서 n개의 메모리 공간 사용
- 크기 n의 이진 탐색 트리 저장 공간


### 연산 시간
- 노드 한 개에 대한 이진 탐색 트리 구성 시간 : O(lgn)
- n개의 노드에 대한 시간 복잡도 : O(n)
