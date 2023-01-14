---
layout: single
title:  "[자료구조] 연결 리스트 (Linked List)"
---

## 1) 연결 리스트(Linked List)
- 자료의 논리적인 순서와 물리적인 순서가 일치하지 않는 자료구조
- 각 원소에 저장되어 있는 다음 원소의 주소에 의해 순서가 연결되는 방식
    - 물리적인 순서를 맞추기 위한 오버 헤드가 발생하지 않음
- 여러 개의 작은 공간을 연결하여 하나의 전체 자료구조를 표현
    - 크기 변경이 유연하고, 더 효율적으로 메모리를 사용

![image](https://user-images.githubusercontent.com/55589616/211826615-e6ba1727-032f-40b2-929f-40b4a53bc776.png)

``` c
typedef struct 
{
	int data;   // 노드 배부의 실제 데이터 또는 레코드
	Node* next; // Next가 가리키는 것은 노드 타입
}Node;

void main()
{
    Node* p;
    p = (Node*)malloc(sizeof(Node));
    p->data = 33;
    p->next = (Node*)malloc(sizeof(Node));
    p->next->data = 22;
    p->next->next = NULL;
}
```

![image](https://user-images.githubusercontent.com/55589616/211818418-3624a795-7910-4035-8ea6-dc029f3a65ce.png)


### 자유공간 리스트(Free Space List)
-  사용하기 전의 메모리나 사용이 끝난 메모리를 관리의 용이성을 위해 노드로 구성하여 연결한 리스트
- 큐(선입 선출)와 유사함

![image](https://user-images.githubusercontent.com/55589616/211829293-7b872f72-5945-4c06-b12b-40463773d0b3.png)


### 데이터 출력
``` c
temp = head;
while(temp != null)
{
    printf("%d ", temp->data);
    temp = temp->next;
}
```

![image](https://user-images.githubusercontent.com/55589616/211819780-b5f55201-096e-42db-8c6d-a6fc79a83a5f.png)


### 데이터 삽입
1. 삽입할 노드를 자유 공간 리스트에서 할당 받음
2. 새 노드의 데이터 필드에 data를 저장
3. 새 노드 p가 리스트의 첫 번째 노드(temp->next)를 가리키게 한다.
4. 첫 번째 노드(temp->next)가 p로 갱신해준다.

``` c
p = (Node*)malloc(sizeof(Node)); // 1.
p->data = 8;    // 2.
p->next = temp->next;   // 3.
temp->next = p; // 4.
```

![image](https://user-images.githubusercontent.com/55589616/211820519-d5a0e9f3-1eb7-4a6d-9c83-2c1dba5d1ba3.png)


### 데이터 삭제
1. 노드 temp의 다음 노드의 주소(temp->next)를 포인터 p에 저장하여, 포인터 p가 다음 노드를 가리키게 함
2. 만약 노드 temp가 리스트의 마지막 노드였다면 temp->next의 값은 null이므로 포인터 p의 값은 null이 됨. 결국 노드 temp 다음에 삭제할 노드가 없다는 의미이므로 삭제연산을 수행하지 못하고 반환(return)
3. 삭제할 노드 p의 다음 노드(p->next)를 노드 temp의 다음 노드(temp->next)로 연결
4. 삭제한 노드 p를 자유 공간리스트에 반환

``` c
if (temp == NULL) return;
else
{
    p = temp->next; // 1.
    if(p == NULL) return;   // 2.
    temp->next = p->next;   // 3.
    free p; // 4.
}
```

![image](https://user-images.githubusercontent.com/55589616/211820936-e89985f2-c3a3-44cc-9bb0-c8d075ba2639.png)


## 2) 원형 연결 리스트(Circular Linked List)
- 단순 연결 리스트에서 마지막 노드가 리스트의 첫 번째 노드를 가리키게 하여 리스트의 구조를 원형으로 만든 연결 리스트
    - 단순 연결 리스트의 마지막 노드의 링크 필드에 첫 번째 노드의 주소를 저장하여 구성
    - 링크를 따라 계속 순회하면 이전 노드에 접근 가능

![image](https://user-images.githubusercontent.com/55589616/211822324-698f14fd-246e-4203-af63-90fc83eb93f2.png)


### 데이터 삽입
- 마지막 노드의 링크를 첫 번째 노드로 연결하는 부분만 제외하고는 단순 연결 리스트에서의 삽입 연산과 같다.
1. 첫 번째 노드로 삽입하기

``` c
InsertFistNode(head, x)
{
    Node* new = getNode();
    new.data = x;
    if(head == NULL)  // 공백 리스트일 경우
    {
        head = new;   // 포인터 CL이 new를 가리키게 한다.
        new.link = new; // 첫 노드가 자신을 가리키게 한다.
    }
    else
    {
        Node* temp = head;  // temp가 첫 노드를 가리키기
        while(temp.link != head)    // 마지막 노드까지 이동하기
            temp = temp.link;
        new.link = temp.link;   // 새로운 노드가 현재의 첫 노드를 가리킨다.
        temp.link = new;    // 마지막 노드의 다음 노드는 첫번째 노드
        head = new; // 헤드가 새로운 노드를 가리킨다.
    }
}
```

2. 중간 노드로 삽입하기

``` c
InsertMiddleNode(head, pre, x)
{
    Node* new = getNode();
    new.data = x;
    if(head == NULL)  // 공백 리스트일 경우
    {
        head = new;   // 포인터 CL이 new를 가리키게 한다.
        new.link = new; // 첫 노드가 자신을 가리키게 한다.
    }
    else
    {
        new.link = pre.link;
        pre.link = new;
    }
}
```


### 데이터 삭제
- 원형 연결 리스트 head에서 포인터 pre가 가리키는 노드의 다음 노드를 삭제하고 삭제한 노드는 자유공간 리스트에 반환하는 연산

``` c
DeleteNode(head, pre)
{
    if(head == NULL) return;
    else
    {
        Node* old = pre.link;   // pre.link를 삭제할 노드 old로 지정
        pre.link = old.link;
        if(old == head) // 삭제할 노드가 head인 경우
            head = old.link;    // 두 번째 노드가 첫 번째 노드가 되도록 조정
        free(old);  // 메모리 해제
    }
}
```


## 3) 이중 연결 리스트(Doubly Linked List)
- 양쪽 방향으로 순회할 수 있도록 노드를 연결한 리스트
- 두 개의 Link 필드와 Data 필드로 구성
    - Left Link
    - Right Link

![image](https://user-images.githubusercontent.com/55589616/212339118-cdeb31eb-89b1-4fc6-b776-4b160262845f.png)

``` c
typedef struct
{
    Dnode* lLink;
    char data[5];
    Dnode* rLink;
}Dnode;
```


### 데이터 삽입
``` c
InsertNode(head, pre, x)
{
    Node* new = getNode();
    new.data = x;
    new.rLink = pre.rLink;
    pre.rLink = new;
    new.lLink = pre;
    new.rLink.lLink = new;
}
```


### 데이터 삭제
``` c
DeleteNode(head, old)
{
    old.lLink.rLink = old.rLink;
    old.rLink.lLink = old.lLink;
    free(old);
}
```


## 4) 원형 이중 연결 리스트(Circular Linked List)
- 이중 연결 리스트를 원형으로 구성
- 장점 : 단순 연결, 이중 연결, 원형 리스트의 장점을 모두 가지고 있다.
- 단점 : 비용이 많이 든다.

![image](https://user-images.githubusercontent.com/55589616/212341092-190247e7-95d5-4606-826d-14152dd756df.png)


## 5) 배열과 연결리스트 비교
- 공간
    - 배열은 정적이므로 최대크기를 미리 예상해야 함
    - 만들어 놓은 배열 공간이 실제로 쓰이지 않으면 낭비
    - 연결 리스트는 실행 시 필요에 따라 새로운 노드 생성
    - 연결 리스트는 포인터 변수공간을 요함
- 검색 시간
    - 배열은 단번에 찾아감(묵시적 어드레싱: 인덱스 연산)
    - 연결 리스트는 헤드부터 포인터를 따라감(현시적 어드레싱: 포인터 읽음)
- 삽입, 삭제 시간
    - 배열의 삽입 : 오른쪽 밀림(Shift Right)
    - 배열의 삭제 : 왼쪽 밀림(Shift Left)
    - 연결 리스트 : 인접 노드의 포인터만 변경
- 어떤 자료구조를 선택할 것인가?
    - 검색 위주이면 배열이 유리
    - 삽입 삭제 위주라면 연결 리스트가 유리
    - 최대 데이터 수가 예측 불가라면 연결 리스트


## 6) 다항식의 연결 자료구조 표현
- 노드 구조
    - 각 항에 대해서 계수와 지수를 저장
    - 계수를 저장하는 coef와 지수를 저장하는 expo의 두 개의 필드로 구성
    - 링크 필드 : 다음 항을 연결하는 포인터로 구성

``` c
typedef struct 
{ 
    float coef; 
    int expo; 
    Node* link; 
}Node; 
```

![image](https://user-images.githubusercontent.com/55589616/212069581-3fb2b318-33c1-4285-8a53-e66e5ce3c657.png)


### 다항식의 덧셈 알고리즘
- *p : 다항식의 A(x)에서 비교할 항을 지시
- *q : 다항식의 B(x)에서 비교할 항을 지시
- *r : 덧셈 연산 결과 만들어지는 다항식 C(x)의 항을 지시

``` c
addPoly(A, B)
{
    Node* p = A;
    Node* q = B;
    Node* C = NULL;
    Node* r = NULL;
    float sum = 0;

    while (p != NULL & q != NULL)   // p와 q가 null이 아닌 동안 반복
    {
        if(p.expo == q.expo)    // p 차수가 q 차수와 같은 경우
        {
            sum = p.coef + q.coef   // 계수의 합을 구함
            if (sum != 0)   // C 다항식에 지수와 합을 계수로 추가
                appendTerm(C, sum, p.expo, r);
            p = p.link; // p 다항식의 다음 차수를 p가 가리킴
            q = q.link; // q 다항식의 다음 차수를 q가 가리킴
        }
        else if(p.expo < q.expo)    // p 차수가 q 차수보다 작은 경우
        {
            appendTerm(C, q.coef, q.expo, r);
            q = q.link;
        }
        else    // p 차수가 q 차수보다 큰 경우
        {
            appendTerm(C, p.coef, p.expo, r);
            p = p.link;
        }
    }
    while (p != null)   // p의 나머지 항 복사
    {
        appendTerm(C, p.coef, p.expo, r);
        p = p.link;
    }
    while (q != null)   // q의 나머지 항 복사
    {
        appendTerm(C, q.coef, q.expo, r);
        q = q.link;
    }
    return C;
}
```

