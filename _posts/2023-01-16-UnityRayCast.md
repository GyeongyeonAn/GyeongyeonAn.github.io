---
layout: single
title:  "[Unity] RayCast 사용법"
---


## 1) Ray 활용기초
- Physics.Raycast는 직선을 씬에 투영하여 대상에 적중되면 true를 리턴하는 물리 함수다.
- Ray 변수
- RaycastHit 변수
- Raycast 함수 
- RaycastAll 함수
- RaycastNonAlloc 함수


## 2) Ray 구조체 사용법
- Ray는 직선의 시작점(origin)과 방향(direction)을 가지고 있는 단순한 구조체다.
- 시작점(origin)은 Vector3 타입의 월드 포지션이며 방향(direction)은 직선의 방향을 나타낼 Vector3 타입의 법선 벡터다.
- Ray는 사용할 때 마다 업데이트 되어야 한다.
    - Ray의 시작점과 방향이 매 프레임마다 달라지는 경우 Ray도 매 프레임 마다 갱신되어야 한다.


``` c#
private void update()
{
    // Creates a Ray from this object, moving forward
    Ray objectForwardRay = new Ray(transform.position, transform.forward);

    // Creates a Ray from the center of the viewport
    // 아래에서 0.5f 값은 뷰포트의 중간값을 나타낸다.
    Ray cameraCenterRay = Camera.main.ViewportPointToRay(new Vector3 (0.5f, 0.5f, 0));

    // Creates a Ray from the mouse position
    Ray mousePointRay = Camera.main.ScreenPointToRay(Input.mousePosition);
}
```


## 3) RaycastHit 구조체 사용법
- RaycastHit은 객체와 Ray의 충돌에 대한 결과 정보를 저장하는 구조체다. 
- Raycast 함수의 out 파라미터로 사용된다.
- 월드에서 Raycasting의 Hit가 발생한 위치, Ray가 충돌한 물체, Ray의 원점에서 얼마나 떨어져있는지 등의 정보를 저장하여 돌려준다.

``` c#
private void update()
{
    // Container for hit data
    RaycastHit hitData;

    // Gets hit position
    Vector3 hitPosition = hitData.point;

    // Gets 원점에서 충돌 지점까지의 거리
    float hitDistance = hitData.distance;

    // Reads the Collider tag
    string tag = hitData.collider.tag;

    // Gets a Game Object reference from its Transform
    GameObject hitObject = hitData.transform.gameObject;
}
```


## 4) Raycast 함수 사용법
- Ray가 씬의 다른 객체와 충돌하는지 여부를 알 수 있으며 충돌할 경우 충돌 정보를 RaycastHit 변수에 저장할 수 있다.
- Physics.Raycast 함수의 리턴 타입은 bool이다. 
    - Ray에 어떠한 오브젝트라도 걸리면 true를 리턴한다.
- 추가 디폴트 인자로 ray의 충돌 탐지 거리 제한, 특정 레이어 또는 트리거 콜라이더 무시하기 등의 제약사항을 추가할 수 있다. 
    - 이러한 세팅들은 어떤 오버로드 된 레이케스트 함수를 사용하느냐에 따라 달라진다.

``` c#
void Update()
{
    Ray ray = new Ray(transform.position, transform.forward);
    RaycastHit hitData;

    if (Physics.Raycast(ray, out hitData))
    {
        // The Ray hit something!
    }
}
```

- 추가 디폴트 인자 사용

``` c#
// Raycast할 Layer 선택
public LayerMask layerMask;

void Update()
{
    Ray ray = new Ray(transform.position, transform.forward);
    RaycastHit hitData;
    
    if (Physics.Raycast(ray, out hitData, 10, layerMask, QueryTriggerInteraction.Ignore))
    {
        // The Ray hit something less than 10 Units away,
        // It was on the a certain Layer
        // But it wasn't a Trigger Collider
    }
}
```


### LayerMask를 사용할 때 레이어 번호를 직접 입력하는 방법
- 우리는 때로 Layer Mask를 동적으로 지정해야할 때도 있다.
- 유니티에서는 총 32개의 Layer를 지원하며 각 Layer를 구분하기 위해 32bit Bit Mask를 사용한다. 

![image](https://user-images.githubusercontent.com/55589616/212619043-83292601-553d-43b7-b7f0-152a5336274d.png)

- Layer는 0부터 시작하며 31이 마지막 Layer번호다.

![image](https://user-images.githubusercontent.com/55589616/212613547-74152c05-5428-4f66-b7f1-2e748056d504.png)

- 9번 레이어는 오른쪽에서 부터 0을 포함해 10번째 이므로 아래와 같은 비트 마스크를 만들어야 한다.

![image](https://user-images.githubusercontent.com/55589616/212619194-61d48efe-c843-455b-bedb-696402c397fb.png)

- 만일 9번 레이어를 선택하기 위해 9를 넘기게 되면 결과적으로 아래 그림과 같이 마스킹 되어 0번, 3번 레이어가 선택 되게 된다. 

![image](https://user-images.githubusercontent.com/55589616/212619482-74cc4802-bde3-49c1-bb3c-872efb5de100.png)

- 9번 레이어를 선택하기 위해서는 9가 아닌 512를 넘겨 주어야 한다. 

![image](https://user-images.githubusercontent.com/55589616/212619549-7ece71e3-50ac-4db8-b049-d2fbb0ddf621.png)

- 만일 9번과 4번 레이어를 동시에 선택하고 싶다면 아래와 같이 528을 넘겨야 한다.

![image](https://user-images.githubusercontent.com/55589616/212619623-0576ddfe-8ef0-459a-bc90-496e4e5e21f3.png)

``` c#
if (Physics.Raycast(ray, 10, 528))
{
    // Layer 9 or 4 was hit!
}
```


### 정수를 LayerMask 값으로 변환하는 방법
- 레이어를 번호로 선택할때는 쉬프트 연산을 사용하는 것이 효과적이다.
- 9번 레이어만 선택하여 Raycast 체크하기

``` c#
void Update()
{
    Ray ray = new Ray(transform.position, transform.forward);
    
    if (Physics.Raycast(ray, 10, 1<<9))
    {
        // Layer 9 was hit!
    }
}
```

- 9번 레이어만 제외하고 모든 레이어 Raycast 체크하기

``` c#
void Update()
{
    Ray ray = new Ray(transform.position, transform.forward);

    if (Physics.Raycast(ray, 10, ~(1<<9)))
    {
        Debug.Log("something else was hit");
    }
}
```


### 레이어 이름과 레이어 번호의 상호작용
- LayerMask.NameToLayer(string layerName) : 레이어의 문자열 이름을 입력으로 레이어 번호를 반환한다.
- LayerMask.LayerToName(int layerNumber) : 레이어의 번호를 입력으로 레이어 문자열 이름을 반환한다.

``` c#
int layerNum = LayerMask.NameToLayer("UI");
Debug.Log(layerNum); // 5
```

- 쉬프트 연산자를 사용해야 한다는 것을 주의하자

``` c#
Ray ray = new Ray(transform.position, transform.forward);
int layerNum = LayerMask.NameToLayer("UI");

if (Physics.Raycast(ray, 10, 1<<layerNum))
{
    Debug.Log("something else was hit");
}
```

### trigger collider를 무시하는 법
- QueryTriggerInteraction 파라미터는 아래 세 가지 중 하나의 값을 가질 수 있다.
    - Ignore - 트리거 콜라이더의 충돌을 무시한다.
    - Collider - 트리거 콜라이더의 충돌을 허용한다.
    - UseGlobal - Physics 옵션에 정의된 기본 값을 따른다


## 5) RaycastAll 함수 사용법
- Raycast 함수에서 단 하나의 객체에 대한 충돌 정보만 반환하는 대신 RaycastHit 구조체 배열을 이용해 여러 개체에 대한 충돌 정보들을 반환한다. 

``` c#
public RaycastHit[] hits;

void Update()
{
    Ray ray = new Ray(transform.position, transform.forward);
    hits = Physics.RaycastAll(ray);
}
```

- Ray의 경로에 있던 모든 객체들을 파괴하는데 사용 될 수도 있다.

``` c#
public class CameraRay : MonoBehaviour
{
    public RaycastHit[] hits;

    void Update()
    {
       if(Input.GetMouseButtonDown(0))
        {
            FireLaser();
        }
    }

    void FireLaser()
    {
        Ray ray = new Ray(transform.position, transform.forward);
        hits = Physics.RaycastAll(ray);

        foreach(RaycastHit obj in hits)
        {
            Destroy(obj.transform.gameObject);
        }
    }
}
```


### RaycastAll의 문제
- RaycastAll은 여러 충돌체를 감지 할 수는 잇지만 정의되지 않은 순서로 검색한다. 
- 물체를 통과한 레이저의 시작점과 가까이 있는 순서대로 배열에 들어갈것 같지만 실제 결과값은 예측 할 수 없는 순서로 저장된다.
- 순서가 중요하다면 아래와 같이 거리에 따라 배열을 정렬하는 방법도 있다.

``` c#
void FireLaser()
{
    RaycastHit[] hits;
    Ray ray = new Ray(transform.position, transform.forward);
    hits = Physics.RaycastAll(ray);

    // Sorts the Raycast results by distance
    Array.Sort(hits, (RaycastHit x, RaycastHit y) => x.distance.CompareTo(y.distance));
}
```


## 6) RaycastNonAlloc 함수 사용법
- 외부에서 이미 생성된 배열을 out 파라메터로 재사용 할 수 있어 가비지(garbage)의 발생을 줄인다.
- 충돌한 객체의 개수를 리턴하지만 그 수는 인자로 넘겨진 배열의 길이 보다는 크지 않다. 
- 실제 반환된 충돌된 객체의 개수를 알면 리턴된 배열이 가득 차지 않았을 때 빈 요소들을 참조하는 것을 방지할 수 있다.
- RaycastNonAlloc도 정의되지 않은 순서로 충돌 정보 배열을 리턴한다.

``` c#
void FireLaser()
{
    RaycastHit[] results = new RaycastHit[10];

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            FireLaser();
        }
    }

    void FireLaser()
    {
        Ray ray = Camera.main.ViewportPointToRay(new Vector3(0.5f, 0.5f, 0));
        int hits = Physics.RaycastNonAlloc(ray, results);

        for(int i=0; i < hits; i++)
        {
            Destroy(results[i].transform.gameObject);
        }
    }
}