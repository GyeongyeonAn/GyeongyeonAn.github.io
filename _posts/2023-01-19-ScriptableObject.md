---
layout: single
title:  "[Unity] ScriptableObject"
---


## 스크립터블 오브젝트(ScriptableObject)
- ScriptableObject를 상속받은 클래스로 Unity Serialize 시스템을 가지고 있다.
- Serialize는 클래스나 오브젝트를 string이나 일차원 배열 형태로 변환하는 것을 의미한다.
- MonoBehaviour보다 경량화된 데이터 컨테이너로써의 역할을 한다.


### MonoBehaviour와의 차이
- 이벤트 함수 콜백 중 OnEnable, OnDisable, OnDestroy만 받는다.
- 게임오브젝트에 Add 컴포넌트 할 수 없다.
- 에셋을 인스턴스로 생성할 수 없고 고유한 하나의 파일로 저장된다.
- 에셋의 내용을 변경하면 모든 참조하는 곳에 동일하게 변경된 내용이 반영된다.


### ScriptableObject 활용
- 기존 시스템은 하나의 컴포넌트에 변수를 정의하면 프리팹을 인스턴스 화해서 만드는 만큼 메모리 할당이 발생한다.
    - ScriptableObject는 데이터 컨테이너로 데이터 공유에 사용할 수 있고, 이를 통해 메모리 사용량 줄일 수 있다.
- 데이터를 참조 형태로 오브젝트에서 가져올 수 있게 함으로써 메모리 할당을 최소화하고 고유한 값을 가질 수 있게 한다.
- 프리펩에서 변화가 없는 동일한 데이터를 사용하는 경우, ScriptableObject로 해당 데이터를 관리하면 편리성과 최적화 효과를 얻을 수 있다.
    - 인벤토리에 저장하는 Item 오브젝트를 관리할때 주로 사용한다.

![image](https://user-images.githubusercontent.com/55589616/213398358-99e980f2-d25c-4f31-95fc-49e240d446fc.png)

![image](https://user-images.githubusercontent.com/55589616/213398471-4d229677-705b-462f-ba70-bafbffcfc8b4.png)


## ScriptableObject의 사용
``` c#
using UnityEngine;

// 에디터에서 쉽게 사용할 수 있도록 메뉴를 만듬
[CreateAssetMenu(menuName = "Item")]

public class Item : ScriptableObject
{
    public string objectName;
    public Sprite sprite;
    public int quantity;
    public bool stackable;
    public enum ItemType
    {
        COIN,
        HEALTH
    }
    public ItemType itemType;
}
```

![image](https://user-images.githubusercontent.com/55589616/213399900-d7989eff-245b-43fc-963c-9050b035cfea.png)

![image](https://user-images.githubusercontent.com/55589616/213400768-36f36ff7-7f5d-4aa2-95cc-cd4c983633ec.png)


## ScriptableObject의 의견
- Json, xml, csv 등으로 관리하던 데이터를 Unity에서 쉽게 사용할 수 있도록 편의적인 기능으로 제공하는 것으로 이해할 수 있다.
- 소규모의 데이터를 편하게 관리할 수 있는 장점이 있지만, 대용량의 데이터를 ScriptableObject로 관리하기엔 어려움이 있을 것으로 생각된다. 
    - 대규모의 데이터는 기본적으로 엑셀로 관리하는 것이 편하기 때문이다. 
- 만약 ScriptableObject로 데이터를 관리한다고 할 경우 프로젝트에 맞는 툴 개발이 필요할 것이다.
- 외부 데이터를 파싱해서 사용하는 번거로움을 피하고, 데이터의 관리 부하가 크지 않다면 ScriptableObject를 사용하는 것이 편하고 최적화에도 도움이 될 것으로 생각한다. 
- 에디터에서 편하게 관리가 필요한 데이터만 ScriptableObject를 사용하는 것도 좋은 개발 방향이 될 수 있을 것으로 생각된다.