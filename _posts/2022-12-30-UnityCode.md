---
layout: single
title:  "[Unity] 활용 기초"
---


## 게임 오브젝트의 위치, 회전, 크기
- 인스펙터 창에 있는 게임오브젝트의 Transform의 각 값은 지역 좌표이다.

``` c#
public class Vector3Example : MonoBehaviour {
    void Start()
    {
        Vector3 tmpPos = new Vector3(10f, 10f, 10f);
        Debug.Log("tmpPos의 값 = " + tmpPos.x + tmpPos.y + tmpPos.z);
        Debug.Log("이름 = " + gameObject.name);
        Debug.Log("전역좌표 = " + transform.position);
        Debug.Log("지역좌표 = " + transform.localPosition);
        Debug.Log("전역회전값(도단위) = " + transform.eulerAngles);
        Debug.Log("지역회전값(도단위) = " + transform.localEulerAngles);
        Debug.Log("크기 = " + transform.localScale);
    }
}
```


## 게임 오브젝트 검색
- 검색은 항상 많은 비용이 예상 되므로 사용 할 때는 주의를 기울여야 한다. 
- Hierarchy에 올려진 게임오브젝트 개수가 많을 때에는 매 프레임 마다 검색하는 것이 효율적이지 않으므로 검색하고자 하는 오브젝트들을 처음 한번 검색하여 저장 후 시작하는 것을 추천한다.
- 다양한 검색 방법
    - Find : GameObject를 이름으로 검색할 할 때, 사용한다. 
    - FindWithTag : GameObject를 Tag로 검색할 때, 사용한다. 
    - FindGameObjectsWithTag : 복수개의 GameObject를 Tag로 검색할 때, 사용한다.
    - FindObjectOfType : Script의 Type으로 검색할 할 때, 사용한다. 
        - **Type으로 검색할 경우 스크립트의 함수 호출시 GetComponent를 호출하지 않는 장점이 있다.**
        - **Garbage 수거를 줄일 수 있다.** 

``` c#
public class ObjectFindScript : MonoBehaviour {
{
    // 이름으로 찾기
    GameObject obj = GameObject.Find(string strname);
    // Tag로 찾기
    GameObject obj = GameObject.FindWithTag(string strTag); 
    // Tag로 여러개를 찾기
    GameObject [] obj = GameObject.FindGameObjectsWithTag(string strTag); 
    // type으로 찾기
    // GetComponent를 호출하지 않음으로 가비지 남김을 줄일 수 있다.
    MyScript obj = (MyScript)FindObjectOfType(typeof(MyScript));
}
```


## 게임 오브젝트 이동
### 방향벡터 구하기

``` c#
public class DerectionVector3 : MonoBehaviour {
    Vector3 vStart;
    Vector3 vEnd = Vector3.zero;
    Vector3 vDir = Vector3.zero;

    void Start()
    {
        // 주어진 시작점에서 주어진 끝점까지의 방향벡터 구하기
        vStart = new Vector3(-3f, 2f, 4f);
        vEnd = new Vector3(10f, 10f, 10f);
        vDir = vEnd - vStart;
        Debug.Log(vDir.normalized);

        // 현재 위치에서 주어진 끝점까지의 방향벡터 구하기
        vDir = vEnd - transform.position;
        Debug.Log(vDir.normalized);
    }
}
```


### 자신의 위치에서 (10, 10, 10)의 방향으로 이동하기

``` c#
public class GameObjectMove : MonoBehaviour
{
    // 시작 위치   
    private Vector3 startPos;
    // 끝점 위치
    private Vector3 endPos;
    // 목적지 position 벡터
    private Vector3 targetPos;
    // 방향 벡터
    private Vector3 dir;
    // 이동 스피드
    public float moveSpeed;

    private void Start()
    {
        startPos = transform.position;
        endPos.Set(10f, 10f, 10f);
        targetPos = endPos;
        dir = endPos - transform.position;
    }

    private void Update()
    {
        // 목표 지점까지의 거리
        float distance = Vector3.Distance(transform.position, targetPos);
        
        // 목표 지점에 거의 도달했을 때, Start -> End or End -> Start 이동 반복하기
        if (distance < 0.5f)
        {
            // 방향을 바꿔준다.
            dir = -dir;
            // Start -> End
            if (targetPos == startPos)
                targetPos = endPos;
            // End -> Start
            else if (targetPos == endPos)
                targetPos = startPos;
        }
        // 이동 함수
        MovePosition();
    }

    public void MovePosition()
    {
        Vector3 tmp = transform.position;
        tmp.x += dir.normalized.x * Time.deltaTime * moveSpeed;
        tmp.y += dir.normalized.y * Time.deltaTime * moveSpeed;
        tmp.z += dir.normalized.z * Time.deltaTime * moveSpeed;
        transform.position = tmp;
    }
}
```


### 다양한 이동 방법

``` c#
// (x, y, z) 각각 + 연산으로 이동
public void MovePosition()
{
    Vector3 tmp = transform.position;
    tmp.x += dir.normalized.x * Time.deltaTime * moveSpeed;
    tmp.y += dir.normalized.y * Time.deltaTime * moveSpeed;
    tmp.z += dir.normalized.z * Time.deltaTime * moveSpeed;
    transform.position = tmp;
}

// Translate 함수로 이동
public void TranslateMove()
{
    Vector3 delta = dir.normalized * Time.deltaTime * moveSpeed;
    transform.Translate(delta);
}

// MoveTowards 함수로 이동
// 1. 목표지점까지 정확하게 이동할 수 있는 함수 (소수점까지 맞춰준다)
// 2. 목표지점까지 도달했는지 조건을 체크하지 않아도 된다.
// 3. 방향벡터가 필요 없다.
public void MoveTowardMove()
{
    float delta = Time.deltaTime * moveSpeed;
    transform.position = Vector3.MoveTowards(transform.position, endPos, delta);
}
```


## Quaternion으로 회전하기
- Quaternion 사용 이유 : 짐벌락 현상을 방지해준다.
- 벡터를 쿼터니언으로 변환 후 계산하고, 대입하여 사용한다.

``` c#
public class QuaternionExample : MonoBehaviour {

    public GameObject target;

    void Start()
    {
        Vector3 direction = target.transform.position - transform.position;
        
        // Quaternion 회전 방식으로, 짐벌락 현상을 피하며 회전하기
        transform.rotation = Quaternion.Euler(new Vector3(30f, 30f, 30f));
        
        // 방향을 제시하면 그 방향을 쳐다보도록 회전한다.
        transform.rotation = Quaternion.LookRotation(direction);

        Quaternion aRotation = Quaternion.Euler(new Vector3(30f, 0, 0));
        Quaternion bRotation = Quaternion.Euler(new Vector3(60f, 0, 0));
        // 두 개의 값 사이의 중간 값(사잇값)을 구할 수 있다.
        // 결과는 30과 60의 사이인 45도를 회전한다.
        transform.rotation = Quaternion.Lerp(aRotation, bRotation, 0.5f);
        
        Quaternion originalRotation = transform.rotation;
        // originalRotation.eulerAngles는 Quaternion을 Vector3로 변환해준다.
        Vector3 originalRotationVector3 = originalRotation.eulerAngles;

        // 두 Quaternion을 곱하면 값이 더해진다.
        Quaternion targetRotation = aRotation * bRotation;
        // 결과는 x축으로 90도 회전한다.
        transform.rotation = targetRotation;
    }
}
```


## 리소스 불러오기
- Resources.Load 메서드는 Resources 폴더안에 있는 리소스를 메모리 상에 로드 하지만 화면에 보여지는 것은 아니다. 
- 화면에 보여주기 위해서는 Intstantiate 메서드로 인스턴스(Instance)를 생성한다.

``` c#
public class ResourceLoad : MonoBehaviour {

    private GameObject Ammo_obj;
    private GameObject Cargo_obj;
    void Start()
    {
        // “Resources/Prefab폴더의 AMMO GameObject 로드
        GameObject tmp = Resources.Load<GameObject>("Prefab/AMMO");

        // Resources 폴더의 Cargo GameObject 로드
        GameObject tmp_1 = Resources.Load<GameObject>("Cargo");
        
        // 같은 결과 다른 표현들
        // GameObject tmp = (GameObject)Resources.Load("Prefab/AMMO");
        // GameObject tmp = Resources.Load("Prefab/AMMO") as GameObject;

        // 화면에 obj 인스턴스 생성
        Ammo_obj = (GameObject)Instantiate(tmp);
        Cargo_obj = (GameObject)Instantiate(tmp_1);
    }
}
```


### 리소스 폴더의 전체 프리팹을 로드하는 방법

``` c#
public class ResourcesFolderLoad : MonoBehaviour {

    private GameObject [] HPBOX_obj;
    void Start()
    {
        // “Resources/Prefab폴더의 모든 게임오브젝트 로드
        GameObject [] tmp = Resources.LoadAll<GameObject>("Prefab");

        // 프리팹 개수 만큼 배열 할당.
        HPBOX_obj = new GameObject[tmp.Length];

        // 화면에 Prefab폴더안의 모든 프리팹 출력
        for (int i = 0; i < tmp.Length; i++)
        {
            HPBOX_obj[i] = (GameObject)Instantiate(tmp[i]);
            // 인스턴스 게임오브젝트 이름 변경
            HPBOX_obj[i].name = "HPBOX_" + i.ToString();
        }
    }
}
```


### 몬스터를 무기와 함께 생성하기

``` c#
public class ResourceLoadExample : MonoBehaviour
{
    private void Start()
    {
        // 리소스에 있는 게임오브젝트 불러오기
        GameObject rcGameObject = Resources.Load<GameObject>("TrollGiant");
        GameObject weaponObject = Resources.Load<GameObject>("Weapons/Axe_DS");

        // 몬스터 생성하기
        GameObject monster = Instantiate<GameObject>(rcGameObject);
        monster.name = "TrollGiant";

        // 무기 생성하기
        GameObject weapon = Instantiate<GameObject>(weaponObject);
        weapon.name = "Axe_DS";

        // 몬스터의 오른손 게임오브젝트 가져오기
        GameObject monsterHR = GameObject.Find("TrollGiant/Bip001/Bip001 Pelvis/Bip001 Spine/Bip001 Spine1/Bip001 Neck/Bip001 R Clavicle/Bip001 R UpperArm/Bip001 R Forearm/Bip001 R Hand/HR");

        // 무기를 몬스터의 오른손 위치로 이동하기
        // 좌표 설정 후에 부모로 설정하는 순서를 지켜야 위치가 정확하게 맞춰진다.
        weapon.transform.position = monsterHR.transform.position;
        weapon.transform.eulerAngles = monsterHR.transform.eulerAngles;
        weapon.transform.SetParent(monsterHR.transform);
    }
}
```

![image](https://user-images.githubusercontent.com/55589616/213094783-6bae5635-4ffa-493e-8b7b-b1f650178c90.png)


## 1초 간격 구현하기
- 코루틴을 사용하여 시간을 제어하는 방법도 있지만, Update에서 시간을 제어하는 방법을 택하는 것이 좋다.
- 랜더링은 1초가 늦어져도 큰 문제는 없겠지만, 데이터는 무결성을 만족해야한다.
- Unity에서는 Update라는 한 프레임 단위에서 함께 관리하기 때문에, 시간제어가 중요하다.

``` c#
public class UpdateExample : MonoBehaviour
{
    float elapsed;
    float oldTime;

    void Start()
    {
        elapsed = 0;
        oldTime = Time.time;
    }

    void Update()
    {
        // 1초 간격 안정적으로 구현하기
        elapsed = Time.time - oldTime;
        if(elapsed >= 1f)
        {
            // 1초마다 실행할 기능을 넣어준다.
            // .............................
            // 소주점까지 고려한다면 elapsed - 1f 값을 더해준다.
            oldTime = Time.time + elapsed - 1f;
        }
    }
}
```


## Random 값을 반환하기
- Random.Range(float a, float b) : a ~ b 범위의 랜덤 값 반환
- Random.Range(int a, int b) : a ~ b-1 범위의 랜덤 값 반환

``` c#
public class PrimitiveInstance : MonoBehaviour
{
    private void Update()
    {
        // 기본 도형 생성하기
        if (Input.GetMouseButton(0))
        {
            GameObject obj = GameObject.CreatePrimitive(PrimitiveType.Cube);
            
            obj.name = "Test";
            // 생성한 기본 도형의 위치를 (-10~10, -3~3, -3~5)의 범위에서 생성하시오.
            // 실수를 넣은 경우 : Random.Range(-10f, 10f) = -10 ~ 10
            // 정수를 넣은 경우 : Random.Range(-10, 10) = -10 ~ 9
            obj.transform.position = new Vector3(Random.Range(-10f, 10f), Random.Range(-3f, 3f), Random.Range(-3f, 5f));

            // 3초 후 오브젝트 파괴
            GameObject.Destroy(obj, 3f);
        }
    }
}
```


## 버튼 입력 체크하기
- 키를 눌렀을 때를 체크하는 함수
    - Input.GetKeyDown() : 키를 누른 시점에 참을 반환
    - Input.GetKeyUp() : 키를 누른 상태를 벗어난 시점에 참을 반환
    - Input.GetKey() : 키를 누르는 중인 상태에 참을 반환

``` c#
public class InputExample : MonoBehaviour
{
    private void Update()
    {
        if (Input.GetKeyDown(KeyCode.LeftArrow))
        {
            Debug.Log("LeftArrow Down");
        }
        if (Input.GetKeyUp(KeyCode.LeftArrow))
        {
            Debug.Log("LeftArrow Up");
        }
        if (Input.GetKey(KeyCode.A))
        {
            Debug.Log("A키를 누르고 있는 동안");
        }
        if (Input.GetMouseButtonDown(0))
        {
            Debug.Log("마우스 왼쪽 버튼 눌렀을 경우");
        }
        if (Input.GetMouseButtonUp(0))
        {
            Debug.Log("마우스 왼쪽 버튼 떼었을 경우");
        }
        if (Input.GetMouseButton(0))
        {
            Debug.Log("마우스 왼쪽 버튼 누르는 동안");
        }
        if (Input.GetMouseButtonDown(1))
        {
            Debug.Log("마우스 오른쪽 버튼 눌렀을 경우");
        }
        if (Input.GetMouseButtonDown(2))
        {
            Debug.Log("마우스 휠 버튼 눌렀을 경우");
        }
    }
}
```


### 플레이어 입력(W,A,S,D)으로 이동하기
- Input.GetAxis(string name)
    - 설정된 버튼의 입력을 받으면 해당하는 float 값 반환
    - "Project Settings -> Input Manager"에서 입력 설정을 확인할 수 있다.
    - 1f ~ 1f 의 값을 반환한다.
    - 가속하는 느낌의 부드러운 이동이 필요한경우 사용한다
- Input.GetAxisRaw(string name)
    - 설정된 버튼의 입력을 받으면 해당하는 float 값 반환
    - "Project Settings -> Input Manager"에서 입력 설정을 확인할 수 있다.
    - 1f , 0f, 1f 의 값을 반환한다. 
    - 즉각적인 반응이 필요할때 사용한다.

``` c#
public class InputExample : MonoBehaviour
{
    // 변수 선언 시점에 바로 초기화 하지 말자 : 선언부의 호출 수가 늘어나는 것은 좋지 않은 방법이다.
    // public float moveSpeed = 5f;
    public float moveSpeed;
    private Vector3 movement

    private void Start()
    {
        moveSpeed = 5f;
        movement = new Vector3();
    }

    private void Update()
    {
        // A, D, left, right 버튼 입력에 따라 1f ~ 1f의 값 반환
        movement.x = Input.GetAxis("Horizontal");
        // W, S, up, down 버튼 입력에 따라 1f ~ 1f의 값 반환
        movement.y = Input.GetAxis("Vertical");

        // A, D, left, right 버튼 입력에 따라 1f, 0f, 1f의 값 반환
        movement.x = Input.GetAxisRaw("Horizontal");
        // W, S, up, down 버튼 입력에 따라 1f, 0f, 1f의 값 반환
        movement.y = Input.GetAxisRaw("Vertical");

        // 정규화를 하는 이유
        // - 대각선 버튼(W, A를 동시에 클릭)을 클릭하는 경우 더해지는 값이 커져서 더 빠른 속도로 이동한다.
        // - 정규화를 하게 되면 이러한 속도를 일반적인 속도로 맞춰줄 수 있게 된다.
        transform.Translate(movement.normalized * Time.deltaTime * moveSpeed);
    }
}
```


### 마우스로 입력 좌표로 플레이어 이동하기
- 마우스가 클릭된 좌표의 y 값과 현재 플레이어의 y값을 동일하게 맞춰줘야 한다.
    - 회전과 이동에 문제 발생을 예방하기 위해

``` c#
public class MousePickExample : MonoBehaviour
{
    public float moveSpeed;
    public float rotateSpeed;
    private Vector3 endPos;

    private void Update()
    {
        MouseTargetMove();
    }

    private void MouseTargetMove()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
            RaycastHit hitInfo;
            if (Physics.Raycast(ray, out hitInfo, Mathf.Infinity))
            {
                if (hitInfo.collider.CompareTag("Ground"))
                {
                    endPos = hitInfo.point;
                }
            }
        }

        // 시작점부터 목표점으로 향하는 선 Print
        Debug.DrawLine(transform.position, endPos, Color.blue);
        // 자신의 위치에서 전방으로 향하는 선 Print
        Debug.DrawRay(transform.position, transform.forward, Color.green);

        // 마우스 Ray Position과 Player의 position의 y축 값을 맞춰줘야 한다.
        // 높이의 오차가 있으면, 회전과 이동에 영향을 주게 된다. 
        Vector3 tmpEnd = endPos;
        tmpEnd.y = transform.position.y;

        Vector3 dir = tmpEnd - transform.position;
        Vector3 newDir = Vector3.RotateTowards(transform.forward, dir.normalized, Time.deltaTime * rotateSpeed, 0f);
        transform.forward = newDir.normalized;

        // 방향전환 회전을 마친 후에 플레이어 이동하기
        if (Vector3.Distance(transform.forward, dir.normalized) <= 0.01f)
            transform.position = Vector3.MoveTowards(transform.position, endPos, Time.deltaTime * moveSpeed);
    }
}
```


### 마우스 스크롤로 카메라 시야각 조정
- Input.GetAxis("Mouse ScrollWheel") 함수로 스크롤에 따른 float값 반환
- 선형보간을 이용해 움직임 조절 

``` c#
public class MouseScroll : MonoBehaviour
{
    public float scrollVelocity;
    float t;

    private void Start()
    {
        scrollVelocity = 30f;
    }

    private void Update()
    {
        float scroll = Input.GetAxis("Mouse ScrollWheel");
        float a = Camera.main.fieldOfView;
        float b = Camera.main.fieldOfView + scroll * scrollVelocity;
        if (scroll != 0)
            t += Time.deltaTime * 200f;
        else
            t = 0;
        Camera.main.fieldOfView = Mathf.Lerp(a, b, t);
    }
}
```