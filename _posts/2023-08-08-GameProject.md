---
layout: single
title:  "[게임 프로젝트] #1 Player 움직임을 구현하자"
---

유니티 버전 : 3.6f1   
   

새로운 프로젝트를 생성해준다 드디어 개인 프로젝트의 시작이다. 아자아자 화이팅!!


일단 만들고자 하는 게임은 원신을 모델로 한 오픈월드 게임이다.


본래 게임 만들기를 시작한다면, 철저한 사전 기획을 해야한다. 어떤 게임을 만들지 윤곽부터 잡자. 여러 말들이 많지만 복잡해서 모르겠고 일단 주인공부터 움직여보자. 한 스탭 한 스탭 부족한 부분이 보이고 기획이 필요하다 생각이 들때, 매꾸는 형식으로 기획해도 늦지 않는다고 생각한다. (맨땅에 헤딩부터!)


게임의 첫 시작은 역시 주인공 캐릭터의 모델을 구해보자. 에셋 스토어에 떠도는 주인공 캐릭 하나를 선정했다.

![image](https://github.com/GyeongyeonAn/Unity-RPG-2D-Game/assets/55589616/8f2909a3-0acc-40ea-9994-9e0d64ffdf72)


우리의 주인공이 사용할 적절한 무기 에셋도 임포트 하자.


![image](https://github.com/GyeongyeonAn/Unity-RPG-2D-Game/assets/55589616/3edc0594-39d2-41e9-b61c-214fd6aff1dc)


이전에 언젠가 쓰겠지 라는 생각으로 사둔 대검 캐릭터 애니메이션 에셋도 임포트 해준다. (드디어 이걸 쓸날이 오는구나!!)


![image](https://github.com/GyeongyeonAn/Unity-RPG-2D-Game/assets/55589616/6dbdd00d-fcaa-4c3f-99d8-206b31f5b20b)


# 목표 
Player Movement 구현하기
상하좌우 이동 : w a s d
공격 : 마우스 왼쪽 클릭
점프 : space
대쉬 : shift
각 동작에 애니메이션 적용


내가 알고 있는 Player 이동 방식에는
1. transform로 구현한 이동
2. Rigidbody로 구현한 이동
3. Charactor Controller로 구현한 이동
4. Nevigation Mesh로 구현한 이동
4가지가 있다.

오픈월드 특성상 경사진 언덕도 있고, 각종 장애물과 상호작용 해야한다.
따라서 기본적으로 Rigidbody를 포함하고 있으며, 굴곡진 경사를 오를 수 있는 기능등을 포함하고 있는 Charactor Controllelr를 사용하도록 하자.


Player에 Charactor Controller를 추가하고 콜라이더를 캐릭터 높이에 맞게 Y값을 1로 조정했다.


![image](https://github.com/GyeongyeonAn/Unity-RPG-2D-Game/assets/55589616/efc40657-e690-4950-b2e5-1cbe9446cb87)


이제 wasd로 이동하는 스크립트를 작성한다.

``` c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    [SerializeField]
    private float moveSpeed;
    private Vector3 moveDir;
    private CharacterController characterController;

    private void Start()
    {
        moveSpeed = 5.0f;
        characterController = GetComponent<CharacterController>();
    }

    private void Update()
    {
        float x = Input.GetAxis("Horizonal");
        float z = Input.GetAxis("Vertical");
        moveDir = new Vector3(x, 0, z);
        characterController.Move(moveDir * moveSpeed * Time.deltaTime);
    }
}
```

moveSpeed는 캐릭터 이동속도이다. private으로 선언하면서 값을 조정하고 싶어서 SerializeField로 선언했다.
moveDir은 캐릭터의 이동 값과 방향을 저장해주는 Vector3 변수다.

GetAxis는 Project에서 Input 셋팅을 했던 해당 키 값을 누를 때 -1f~1f까지의 값을 반환해준다. 

Horizonal은 키보드 좌우 방향키와 A, D 로 설정되어 있다. 
Vecrtical은 키보드 상하 방향키와 W, S 로 설정되어 있다.
만약 키를 변경해주고 싶을 때는 Input 셋팅에서 변경해주면 간단히 처리할 수 있다.

GetAxisRaw를 사용하는 방식도 있는데, 이는 입력 값이 -1f, 0, 1f로 정규화하여 반환해준다.

GetAxis는 가속도의 느낌이 생기며, 부드러운 움직임을 보인다.
GetAxisRaw는 정규화한 값으로 일정한 속도로 딱 딱 끊어지는 듯한 움직임을 보인다.

나는 방대한 필드를 탐험하는 오픈월드 특성상 부드러운 움직임을 선택했다.
(사실상 취향차이 일것이다.)


이것으로 WASD 이동은 구현되었다. 다음은 점프를 구현해보자


``` c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    [SerializeField]
    private float moveSpeed;
    [SerializeField]
    private float jumpForce;
    private Vector3 moveDir;
    private CharacterController characterController;

    private void Start()
    {
        moveSpeed = 5.0f;
        jumpForce = 4.0f;
        characterController = GetComponent<CharacterController>();
    }

    private void Update()
    {
        if (characterController.isGrounded)
        {
            float x = Input.GetAxis("Horizontal");
            float z = Input.GetAxis("Vertical");
            moveDir = new Vector3(x, 0, z);

            if (Input.GetKeyDown(KeyCode.Space))
                moveDir.y = jumpForce;
        }
        moveDir.y += Physics.gravity.y * Time.deltaTime;
        characterController.Move(moveDir * moveSpeed * Time.deltaTime);
    }
}
```

characterController.isGrounded로 현재 오브젝트가 지면에 닿아 있는지 검사할 수 있다. 이를 이용하여 플레이어가 땅에 있다면 이동과 점프키를 누르도록 허용해준다.

Input.GetKeyDown으로 space키를 눌렀을 때의 이벤트를 검사하고, moveDir의 y값을 넣어준다.

Project에 설정한 중력값을 가져올때는 Physics.gravity를 선언하면 값을 반환해준다.
중력값이 -로 설정되어있기에 이를 moveDir값에 더해주면 중력값 만큼 떨어지게 된다. 

움직임 값

![녹음_2023-08-09_151412_AdobeExpress (1)](https://github.com/GyeongyeonAn/Unity-RPG-2D-Game/assets/55589616/d281de8e-336a-471a-acec-aa12c8ba1b7a)
