---
layout: single
title:  "[Unity] 이론 정리"
---


## 가비지 콜렉터 (Garbage Collector)
- 가비지 컬렉터란 사용하지 않는 메모리를 자동적으로 회수하는 .Net서비스다.
    - 게임오브젝트에서 컴포넌트를 가져오고자 한다면 GetComponent() 메서드를 사용하고 
    - 추가하고자 한다면 AddComponent() 메서드를 사용한다. 
    - 이러한 메서드는 가비지콜랙터 (Garbage Collector)에 의해 수거되는 일정양의 가비지를 남긴다.
- 가비지 수집은 시스템 백그라운드에서 상당한 CPU자원을 소모하므로 실행 시 일시 멈춤 현상이 발생할 수 있다. 
- 힙(Heap)메모리 해제를 수동으로 하는 상황이 아니므로 가급적 가비지를 발생시키지 말아야 한다. 
- 여러 번 호출하기 보다는 실행 시 한번 호출하는 것이 효율적이다.
- 즉, GetComponent의 사용을 최대한 줄이고, AddComponent는 괜찮다.


## 게임 오브젝트 (GameObject)
- Unity에서 제공하거나 직접 만든 모든 오브젝트의 자료형을 GameObject라고 한다.
- GameObject : 클래스(자료형)의 개념
- gameObject : GameObject에 속해있는 컴포넌트에 접근하기 위한 인스턴스
    - 인스턴스 : 하나의 클래스에서 생성된 객체


## Log 메서드의 종류
- DeBug.Log : Console뷰에 메세지 출력
- DeBug.LogWarning : Console뷰에 경고 메세지 출력
- DeBug.LogError : Console뷰에 에러 메세지를 출력


## 게임 리소스와 프리펩
- 게임에서 필요로 하는 그래픽 이미지와 같은 데이터
- Asset : 유니티 게임에서 사용되는 자원 
- 게임 리소스를 사용하기 위해서 프리팹(Prefab)이라는 게임 오브젝트를 사용해야 한다. (반드시 프리팹 이어야만 리소스로 사용가능 한 것은 아니다.)
- 리소스 파일은 저장된 장소에 따라서 읽어 오는 방식이 다르다. (이미 약속된 장소라면 간단히 읽어 올 수 있다.) 
- Unity는 Resources 폴더라는 이름의 특정 폴더에서 리소스를 읽어 올 수 있도록 하였다. 
    - Resources 폴더는 Assets 폴더 하위에서만 존재 해야 한다. 
    - 자동으로 생성해주는 폴더가 아니므로 직접 생성한다.
- Resources 폴더가 아닌 곳에 데이터를 보관하고 읽어 오고 싶다면 AssetBundle 이라는 데이터를 제작하여 읽어와야 하는 번거로움이 있다.
- FBX파일 : 프리팹(Prefab)의 원본 파일로 그래픽 저작도구(3DS MAX 등)로 제작한 캐릭터, 배경 등의 리소스 파일이다. 


### 리소스의 자료형
- GameObject : 프리팹, FBX를 로드하기 위한 자료형
- 2D의 png
    - Texture2D (3차원 모델에서 텍스처로 사용할 목적)
    - Sprite (UI, Sprite에 사용할 목적)
- 텍스트 형식
    - TextAsset
- 사운드
    - AudioClip


## 이벤트 함수
- 활성화 / 비활성화
    - gameObject.SetActive(false)와 this.enabled = false는 다르다.
        - gameObject.SetActive(false) : 게임오브젝트 자체를 비활성화
        - this.enabled = false : 스크립트 컴포넌트를 비활성화
- Awake()
    - GameObject가 유니티 내부에서 Instantiate될 때, GameObject가 활성화 상태일 경우 호출된다. 
    - 단, **스크립트 컴포넌트가 활성/비활성에 상관 없이 호출된다.**
    - 주로 게임의 상태 값 또는 변수의 초기화에 사용된다.
    - 코루틴 사용 불가
- OnEnable()
    - 게임 시작 이후, 첫 프레임 시작 전, GameObject가 활성화 될 때 호출되는 함수
    - 게임오브젝트와 스크립트 컴포넌트 모두 활성화되어있어야 호출된다.
    - 이벤트 연결 시 사용한다.
- Start()
    - Update 직전에 단 한번 호출되는 함수
    - 게임오브젝트와 스크립트 컴포넌트 모두 활성화되어있어야 호출된다.
    - 다른 스크립트의 모든 Awake가 모두 다 실행된 이후에 실행된다.
- FixedUpdate()
    - 물리 갱신용
    - GameObject와 해당 컴포넌트가 활성화 되어 있는 동안, 설정된 고정 시간 주기로 실행되는 함수
    - 고정 시간 주기 설정법
        - Edit -> Project Setting -> Time -> Fixed Timestep의 값을 조정한다. (작아질 수록 빨라짐)
- Update()
    - 랜더링 갱신용
    - GameObject와 해당 컴포넌트가 활성화 되어 있는 동안, 매 프레임마다 반복 호출되는 함수
    - 속도가 느린 기기에 실행해서 게임의 프레임이 속도가 떨어지는 상황이 오면 호출 빈도가 줄어들 수 있다.
    - 랜더링과 물리 중 속도가 빠른 것은 랜더링인 Update함수다. (시간 제어는 속도가 빠른 곳에서 하는 것이 좋다)
- LateUpdate()
    - 모든 Update 함수가 호출되고 나서 한 번씩 호출된다.
    - 순차적으로 실행해야 하는 로직에 사용한다.
    - 주로 카메라 이동 로직에 사용된다. 
        - 카메라는 캐릭터, 배경, 몬스터 등등 게임세계가 모두 갱신된 후 갱신해야한다.
- OnGUI()
    - 레거시 GUI 관련 함수를 사용할 떄 사용한다.
- OnDisable() : 
    - 게임오브젝트 또는 스크립트 컴포넌트가 비활성화됐을 때 호출된다.
    - 호출 시에는 Update()가 대기 상태가 되며 정지된다.
    - 이벤트 연결을 종료할 떄 사용한다.
    - 코루틴 사용 불가
- OnApplicationQuit()
    - Application 종료 시 호출 되는 함수


### 실행 순서
![image](https://user-images.githubusercontent.com/55589616/209093883-f8650386-6c7d-4701-9dfe-cb95a2a6c320.png)


## 시간 제어
- Time.deltaTime
    - 마지막 프레임을 완료하는 데 걸린 시간 (초)
    - 다음 업데이트 호출까지의 간격
    - 컴퓨터의 성능에 따라 값이 다르다.
    - 이 기능을 사용하면 게임 프레임 속도를 독립적으로 설정할 수 있다.
- Time.fixedDeltaTime
    -  다음 FixedUpdate함수 호출까지의 간격
- Time.time 
    - 게임시작 후 진행된 시간 (초)
    - Start함수에서  Debug.Log(Time.time) 사용하면  0이 출력되고 Update함수에서는 값이 계속 증가할 것이다.
- Time.timeScale 
    - 시간이 지나가는 스케일
    - 슬로우 모션 효과에 사용할 수 있다.
        - Time.timeScale 이 1이라면 원래의 진행 속도를 의미하며 0.5라면 절반의 진행 속도를 의미한다.
        - 보스 몬스터를 처치 하였을 경우 보상을 받는 상황일 때, Time.timeScale값을 조절하여 원하는 연출을 할 수 있다.


## 벡터
- 벡터
    - 크기와 방향을 갖는다
    - 방향은 부호를 의미
    - 변위, 속도, 가속도
- 스칼라
    - 크기만 갖는다
    - 절대값으로 계산
    - 거리, 속력, 가속력
    - 벡터의 내적(Dot Product)
- 단위 벡터
    - 크기가 1인 벡터
    - 단위 벡터 중 방향 관계를 결정하기 위한 벡터를 "방향벡터"라고 한다.
        - 방향벡터 = (목적지 - 시작점)을 정규화
    - 정규화란(Normalize), 어떠한 벡터를 크기가 1인 벡터로 만드는 것
        - 각 벡터(x, y, z)에 스칼라 값을 나눠준다.
- 벡터의 곱하기는 내적, 외적 2종류가 있다.


### 벡터의 내적 (Dot Product)
- 기호 • (dot 라고 읽는다)
- 두 벡터의 방향관계를 계산할때 사용 (cos값을 얻을 수 있어서 각도를 구할 수 있다 )
- 계산결과는 스칼라
- 공식

![image](https://user-images.githubusercontent.com/55589616/210039750-06a48291-3f22-4865-b640-ca0e8fc96d60.png)

- 각도 구하기
    - 벡터의 내적을 이용
        - 벡터의 내적의 반환값은 각도가 아니며 각도를 구하기 위해서는 아크코사인 연산을 해야 한다
        - 게임오브젝트의 앞, 뒤 판정 할 때 사용
    - 유니티 Vector3.Angle()함수 사용
        - Angle함수를 사용하지만 내부적으로는 내적을 연산한다.
        - 각도를 리턴(반환)


![image](https://user-images.githubusercontent.com/55589616/210039097-afafb845-82dd-469f-9582-874517489664.png)


### 게임 오브젝트 앞, 뒤 판정하기
``` c#
public Transform other;
void Update() 
{ 
    if (other) 
    { 
        Vector3 forward = transform.TransformDirection(Vector3.forward); 
        Vector3 toOther = other.position - transform.position;
        if (Vector3.Dot(forward, toOther) < 0) 
        { 
            print("The other transform is behind me!"); 
        } 
    } 
}
```


### 벡터의 외적 (cross Product)
- 기호 X (cross라고 읽는다 )
- 두 벡터에 수직인 벡터를 구할때 사용


## 월드 변환 행렬
- 이동행렬(T), 회전행렬(R), 크기행렬(S)
- 월드에 게임오브젝트가 올려질때 월드변환행렬이 적용된다.
- 변환행렬은 S * R * T 또는 T * R * S 순으로 곱해야 한다.
    - OpenGL과 DirectX 방식에서 표현방법이 다르다.
- 이동변환 행렬 (T)
    - 이동변환 행렬의 대각 성분 dx, dy, dz는 이동량을 의미한다.

    ![image](https://user-images.githubusercontent.com/55589616/210024501-9de3bfbe-eddf-4725-bf51-a266535e12da.png)

- 회전변환 행렬 (R)

    ![image](https://user-images.githubusercontent.com/55589616/210024952-b6cc72d5-f747-456a-b273-1213e9c482e1.png)

- 크기변환 행렬 (S)
    - 크기변환행렬에서 a, b, c의 값이 각 성분의 scale값을 의미한다.

    ![image](https://user-images.githubusercontent.com/55589616/210024607-5c9dd962-1e75-40ec-bfee-778b18f3663c.png)


## 사원수 (Quaternion)
- 3차원 공간 회전을 표현하는 여러가지 방법중의 하나
- 임의의 카메라 조작이나 키프레임 애니메이션의 보간(interpolation)등에 사용
- 보간 (interpolation)
    - 중간값을 만들어 내는 것
    - 선형 보간
        - Mathf.Lerp()      // 숫자 간의 선형 보간
            - 공식 : (1 - t)a + tb (t: 시간)
        - Vector3.Lerp()    // Vector3간의 선형 보간
        - 끝점의 값이 주어졌을 때 그 사이의 위치값을 추정하기 위하여 직선 거리에 따라 선형적으로 계산하는 방법
    - 구면 선형 보간
        - Mathf.SLerp()      // 숫자 간의 구면 선형 보간
        - Vector3.SLerp()    // Vector3간의 구면 선형 보간
        - 구의 면을 따라서 보간을 계산
- 짐벌락(Gimbal-Lock)을 해결
    - X축을 90도 회전한다고 가정한다면 Y축 또는 Z축이 될 수 있다.
    - 회전축이 겹쳐짐으로 인하여 원하지 않는 계산결과가 나오는 현상
- 계산의 효율이 좋고, 메모리를 적게 사용.


### 사원수의 사용
- 사원수 변환은 4개의 요소로 이루어진 사원수 만을 이용하여 임의의 회전축에 대한 변환을 효율적으로 표현할 수 있다.
- 게임에서 사원수 변환을 적용하려면 최종적으로 행렬의 형태로 변환해서 사용해야 한다. (DirectX)
- 오일러 방식의 회전값 : transform.eulerAngles, transform.localEulerAngles
- 쿼터니언 방식의 회전값 : transform.rotation

![image](https://user-images.githubusercontent.com/55589616/210026336-49f16b4a-30ed-436a-97a8-c8e642187a57.png)


## 가시성 판단
- 화면에 보여지는 것과 보여지지 않는것을 판단
- 화면에 보이지 않는 데이터
    - 메시(Mesh)의 뒷면
    - 시야 절두체(MainCamera의 시야)에 포함되지 않는 메시나 면
    - 다른 메시에 가려진 메시나 면
- 가시성 판단은 게임의 속도를 높이기 위해서 사용하는 기술
- 필요없는 데이터를 그리지 않는다.
- 컬링(Culling) : 그려지는(랜더링) 부분을 구분
    - 절두체컬링 : 랜더링 되는 부분 
    - 오큘루전컬링 : 가려진 부분을 랜더링하지 않는 것


### 노멀(normal) 벡터
- 면은 앞뒤가 존재한다.
- 앞뒤를 구분하는 기준은 normal벡터이다.
    - 노멀 벡터가 향하는 방향이 앞면이다.
- 면에 수직인 벡터
- 법선벡터
- 노멀 벡터 3개가 선을 이루어 삼각형을 만들고, 삼각형을 메시가 랜더링한다.
    - 메시는 삼각형으로 이루어지기 때문에, 모서리에 위치한 노멀은 단순히 해당 삼각형의 표면에 수직이라고 볼 수도 있다. 
    - 하지만 노멀 벡터는 실제로 해당 삼각형 전체에 걸쳐 보간된 모서리 사이의 중간 위치 표면에 방향을 지정해 준다. 
    - 만약 세 개의 노멀 모두가 같은 방향을 가리킨다면 이 삼각형 전체에 조명이 균등하게 주어진다.
- 유니티의 모든 게임오브젝트는 정점 3개가 이어져 그려진 여러개의 삼각형으로 이루어져있다.
- 노멀벡터의 개수가 많을 수록, 곡선을 만들 수 있고, 정교한 색과 명암을 구현할 수 있다.

![image](https://user-images.githubusercontent.com/55589616/210029766-faf49b55-4246-4f7d-970c-29725c2a575d.png)


### Mesh(메쉬)
- 메쉬(Mesh) : 점과 면으로 이루어진 게임오브젝트를 구성하는 덩어리
- 메쉬(Mesh)의 구성요소
    - 정점(Vertex)
    - 폴리곤을 구성하는 정점 인덱스
    - 정점 노멀벡터(Vertex Nomal Vector)
    - UV 좌표
        - Texture를 연산할때 사용하는 좌표
        - 이미지의 크기가 정규화된 좌표
- Mesh Filter 컴포넌트에 Mesh가 존재
    - Mesh Renderer를 사용하는 경우
- 인간형 캐릭터와 같은 모델구조를 갖는 게임 오브젝트
    - Skinned Mesh Renderer 내부에 포함

``` c#
public class Plane : MonoBehaviour
{
    private void Start()
    {
        // 빈 게임오브젝트 Plane이라는 이름으로 생성
        GameObject obj = new GameObject("Plane");
        MeshRenderer meshRenderer = obj.AddComponent<MeshRenderer>();
        MeshFilter meshFilter = obj.AddComponent<MeshFilter>();

        Mesh mesh = new Mesh();

        // 정점(Vertex)
        Vector3[] vertices = new Vector3[4];
        vertices[0] = new Vector3(-0.5f, 0, 0);
        vertices[1] = new Vector3(0.5f, 0, 0);
        vertices[2] = new Vector3(-0.5f, 1f, 0);
        vertices[3] = new Vector3(0.5f, 1f, 0);
        mesh.vertices = vertices;

        // 폴리곤을 구성하는 정점 인덱스
        // 정점의 Index값을 가진다.
        int[] tri = new int[6];
        tri[0] = 0;
        tri[1] = 2;
        tri[2] = 3;
        tri[3] = 0;
        tri[4] = 3;
        tri[5] = 1;
        mesh.triangles = tri;

        // 정점의 노말 벡터
        Vector3[] normals = new Vector3[4];
        normals[0] = -Vector3.forward;
        normals[1] = -Vector3.forward;
        normals[2] = -Vector3.forward;
        normals[3] = -Vector3.forward;
        mesh.normals = normals;

        // uv 좌표
        Vector2[] uv = new Vector2[4];
        uv[0] = new Vector2(0, 0);
        uv[1] = new Vector2(1, 0);
        uv[2] = new Vector2(0, 1);
        uv[3] = new Vector2(1, 1);
        mesh.uv = uv;

        meshFilter.mesh = mesh;
    }
}
```


## 물리 연산
- 유니티는 랜더링 시스템과 물리 시스템이 구분되어져 있다.
    - 랜더링 시스템 : 눈에 보여지는 것을 다루는 시스템
    - 물리 시스템 : 물리량을 다루는 시스템
    - 생각해야 할 점 : 빠른 물체의 이동속도를 갖는 발사체와 같은 충돌을 효과적으로 구현하기 위한 방법
- 특정 목표지점 또는 방향으로 날아가는 물체 (힘, 속도, 위치계산)
- 구현 방법 : Physics 클래스를 이용
    - 물체의 움직임을 현실적으로 표현하기 위한 물리 시스템.
    - 구성요소
        - RigidBody(강체) : 질량(mass), 저항(Drag), 중력(Gravity)
        - Collider : 물리재질, 마찰(Friction), 반동(Bounciness)


### RigidBody 컴포넌트
![image](https://user-images.githubusercontent.com/55589616/210294983-31c2bf9c-7a3b-4aad-b60a-9e85f9d35d75.png)
![image](https://user-images.githubusercontent.com/55589616/210295024-5042db1c-4416-4004-a388-f0c28613ab08.png)


### Collider 컴포넌트
- Shpere Colider가 충돌 연산량이 제일 적다. (두 원의 반지름 사이의 거리 < 두 원의 반지름의 합)
- Collider의 충돌을 감지하기 위해서는 rigidbody가 필요하다.

![image](https://user-images.githubusercontent.com/55589616/210295081-415f5875-a66a-4668-ba95-5d30e91c905c.png)