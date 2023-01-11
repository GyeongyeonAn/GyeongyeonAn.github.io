---
layout: single
title:  "[자료구조] 선형 리스트"
---

## 1) 리스트
- 리스트의 정의
    - 자료를 나열한 목록
    - 목록이나 도표처럼 여러 데이터를 관리할 수 있는 자료형을 추상화
    - 데이터 삽입, 삭제, 검색 등 필요 작업을 가함
    - 스택과 큐는 리스트의 특수한 경우에 해당


## 2) 선형 리스트
- 순서 리스트(Ordered List)
- 자료들 간에 순서를 갖는 리스트
    - 선형 리스트에서 원소를 나열한 순서는 원소들의 순서가 됨
- 표현형식
    - 리스트 이름 = (원소1, 원소2, …, 원소n)
- 저장
    - 원소들의 논리적 순서와 같은 순서로 메모리에 저장
    - 순차 자료구조
        - 원소들의 논리적 순서 = 원소들이 저장된 물리적 순서
        - 동창 선형 리스트가 메모리에 저장된 물리적 구조
- 위치 계산
    - 선형 리스트가 저장된 시작 위치 : a
    - 원소의 길이 : l
    - i번째 원소의 위치 = a + (i-1) * l


## 3) 새로운 원소 삽입
- 선형 리스트 중간에 원소가 삽입되면, 그 이후의 원소들은 한자리씩 자리를 뒤로 이동하여 물리적 순서를 논리적 순서와 일치시킨다.
    1. 원소를 삽입할 빈 자리 만들기
    2. 준비한 빈 자리에 원소 삽입하기
- 삽일 할 자리를 만들기 위한 자리 이동 횟수
    - (n+1)개의 원소로 이루어진 선형 리스트에서 k번 자리에 원소를 삽입하는 경우
    - 이동 횟수 : n–k+1 (마지막 원소의 인덱스 - 삽입할 자리의 인덱스 + 1)


## 4) 원소 삭제
- 선형 리스트 중간에서 원소가 삭제되면, 그 이후의 원소들은 한자리씩 자리를 앞으로 이동하여 물리적 순서를 논리적 순서와 일치시킨다.
1. 원소 삭제하기
2. 삭제한 빈 자리 채우기
- 삭제 후, 빈 자리를 채우기 위한 자리 이동 횟수
    - (n+1)개의 원소로 이루어진 선형 리스트에서 k번 자리의 원소를 삭제한 경우
    - 이동 횟수 : n - k (마지막 원소의 인덱스 - 삭제한 자리의 인덱스)


## 5) 다항식의 순차 자료구조 구현
- 다항식의 특징
    - 지수에 따라 내림차순으로 항을 나열
    - 다항식의 차수 : 가장 큰 지수
    - 다항식 항의 최대 개수 = (차수 + 1)개
- 추상 자료형 : ADT Polynomial
![image](https://user-images.githubusercontent.com/55589616/211756928-ee6dd5d9-8ad8-49b6-9189-de62d39f3d4b.png)

![image](https://user-images.githubusercontent.com/55589616/211757004-e1b4917a-2f4a-4f37-8c25-11865c741a15.png)


### 1차원 배열로 표현
- 배열 인덱스 i : 지수(n-i)을 의미
- 배열 인덱스 i의 원소: 지수(n-i)항의 계수

![image](https://user-images.githubusercontent.com/55589616/211757591-54a676fd-6d1e-4181-a8a6-9b84d81e5ec7.png)
    
- 희소 다항식에 대한 문제 발생

![image](https://user-images.githubusercontent.com/55589616/211757961-5663ead8-bd9a-40d4-88c2-72dc6cd8e7ee.png)


### 2차원 배열로 표현
- 다항식의 각 항에 대한 <지수, 계수>의 쌍을 2차원 배열에 저장
- 2차원 배열의 행의 개수: 다항식의 항의 개수
- 2차원 배열의 열의 개수: 2

![image](https://user-images.githubusercontent.com/55589616/211758300-7dc5a3dc-f343-470f-8266-d6fd72f78612.png)


``` c
#include<stdio.h>
#define MAX(a,b) ((a>b)?a:b)
#define MAX_DEGREE 50

typedef struct {
	int degree;             // 차수
	float coef[MAX_DEGREE]; // 계수
} polynomial;

polynomial addPoly(polynomial A, polynomial B) 
{
	polynomial C; // 다항식 덧셈의 결과 다항식을 저장할 polynomial 구조체 변수 선언
	int A_index = 0;
	int B_index = 0;
	int C_index = 0;
	int A_degree = A.degree;
	int B_degree = B.degree;
	C.degree = MAX(A.degree, B.degree);

	while (A_index <= A.degree && B_index <= B.degree)
	{
		if (A_degree > B_degree)
		{
			C.coef[C_index++] = A.coef[A_index++];
			A_degree--;
		}
		else if (A_degree == B_degree)
		{
			C.coef[C_index++] = A.coef[A_index++] + B.coef[B_index++];
			A_degree--;
			B_degree--;
		}
		else
		{
			C.coef[C_index++] = B.coef[B_index++];
			B_degree--;
		}
	}
	return C;
}

void printPoly(polynomial P)
{
	int i, degree;
	degree = P.degree;

	for (i = 0; i <= P.degree; i++)
		printf("%3.0fx^%d", P.coef[i], degree--);
	printf("\n");
}

void main()
{
	polynomial A = { 3, {4,3,5,0} };		// 다항식 A의 초기화
	polynomial B = { 4, {3, 1, 0, 2, 1} };	// 다항식 B의 초기화

	polynomial C;
	C = addPoly(A, B);	// 다항식 A, B에 대한 덧셈을 수행하기 위해 addPoly 함수 호출

	printf("\n A(x)=");	printPoly(A);
	printf("\n B(x)=");	printPoly(B);
	printf("\n C(x)=");	printPoly(C);
}
```

- 실행 결과
![image](https://user-images.githubusercontent.com/55589616/211759241-6bf6eaf2-920e-4413-9395-ecef0893638d.png)


## 6) 행렬의 순차 자료구조 구현
- 행렬(Matrix)
    - m : 행의 개수
    - n : 열의 개수
    - 원소의 개수 : (m * n)개
- 전치 행렬
    - 행렬의 행과 열을 서로 교환하여 구성한 행렬
    - 행렬 A의 모든 원소의 위치(i, j)를 (j, i)로 교환
    - m * n행렬을 n * m행렬로 변환한 행렬 A’는 행렬 A의 전치행렬

![image](https://user-images.githubusercontent.com/55589616/211759833-cceceae2-adac-4522-9acb-c561fa6c20ea.png)

### 2차원 배열로 표현
- m * n 행렬을 m행 n열의 2차원 배열로 표현
- 희소 행렬에 대한 문제 발생
    - 희소 행렬 B는 배열의 원소 56개 중에서 실제 사용하는 것은 0이 아닌 원소를 저장하는 10개 뿐이므로 46개의 메모리 공간 낭비
    - 희소 행렬인 경우에는 0이 아닌 원소만 추출하여 <행 번호, 열 번호, 원소> 쌍으로 배열에 저장
    ![image](https://user-images.githubusercontent.com/55589616/211760359-5b387fd4-12c2-4abf-87b4-8bf18d5c2cae.png)

    - 추출한 순서쌍을 2차원 배열의 행으로 저장
    - 원래의 행렬에 대한 정보를 순서쌍으로 작성하여 0번 행에 저장
    ![image](https://user-images.githubusercontent.com/55589616/211760591-325a9775-d210-4263-b7a3-590449a44c4a.png)


``` c
#include<stdio.h>

typedef struct {
	int row;
	int col;
	int value;
} term;

void smTranspose(term a[], term b[])
{
	int m, n, v, i, j, p;
	m = a[0].row;	// 희소 행렬 a의 행 수
	n = a[0].col;	// 희소 행렬 a의 열 수
	v = a[0].value;	// 희소 행렬 a에서 0이 아닌 원소 수
	b[0].row = n;	// 전치 행렬 b의 행 수
	b[0].col = m;	// 전치 행렬 b의 열 수
	b[0].value = v;	// 전치 행렬 b의 원소 수 
	if (v > 0)		// 0이 아닌 원소가 있는 경우에만 전치 연산 수행
	{
		p = 1;
		for(i = 0; i < n; i++)			// 희소 행렬 a의 열별로 전치 반복 수행
			for (j = 1; j <= v; j++)	// 0이 아닌 원소 수에 대해서만 반복 수행
			{
				if (a[j].col == i)		// 현재의 열에 속하는 원소가 있으면 b[]에 삽입
				{
					b[p].row = a[j].col;
					b[p].col = a[j].row;
					b[p].value = a[j].value;
					p++;
				}
			}
	}
}
```