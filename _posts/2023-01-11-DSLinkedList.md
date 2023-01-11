---
layout: single
title:  "[자료구조] 연결 리스트"
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
    free p;
}
```

![image](https://user-images.githubusercontent.com/55589616/211820936-e89985f2-c3a3-44cc-9bb0-c8d075ba2639.png)


## 2) 이중 연결 리스트
- 단순 연결 리스트의 삽입
- tail 포인터가 따로 있으면 유리
- 삭제일 경우에는 tail 포인터가 뒤로 이동
- 이중 연결이어야 tail 포인터를 뒤로 이동할 수 있음

![image](https://user-images.githubusercontent.com/55589616/211822047-24eb5dc4-e37b-4419-a333-9669b9fd2969.png)


## 3) 원형 연결 리스트
- 타임 셰어링 시스템의 타임 슬라이스
- 사용자 교대

![image](https://user-images.githubusercontent.com/55589616/211822324-698f14fd-246e-4203-af63-90fc83eb93f2.png)


### 원형 이중 연결 리스트
- 장점 : 단순 연결, 이중 연결, 원형 리스트의 장점을 모두 가지고 있다.
- 단점 : 비용이 많이 든다.
![image](https://user-images.githubusercontent.com/55589616/211822787-43763329-6eb1-4a2a-9843-7f18721e62c8.png)


## 4) 배열과 연결리스트 비교
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
