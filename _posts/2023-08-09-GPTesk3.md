---
layout: single
title:  "[게임 프로젝트] #3 3인칭 쿼터뷰 시점의 카메라 기능 구현"
---

# 목표
1. 마우스 상하 좌우 이동에 따라 카메라 이동
2. Player를 보는 위치에 카메라 고정
3. 카메라가 보는 시점에 Player도 같은 방향을 바라봄

먼저 1번, 2번 목표를 달성하기 위해 CameraMovement 스크립트를 작성하였다.


``` c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraMovement : MonoBehaviour
{
    public Transform targetObject;  // Player Object의 위치를 저장
    public float followSpeed;       // Camera Move의 속도 
    public float sensitivity;       // Camera 민감도
    public float clampAngle;        // Camera 각도 제한 수치

    private float rotX;
    private float rotY;

    public Transform realCamera;    // mainCamera의 위치
    public Vector3 dirNormalized;   // mainCamera의 방향 값
    public Vector3 finalDir;        // 목적지 벡터 값
    public float minDistance;       // Camera와 Target의 최소 거리 
    public float maxDistance;       // Camera와 Target의 최대 거리 
    public float finalDistance;     // 목적지까지의 거리
    public float smoothness;        // 부드러움 움직임을 위한 수치 

    private void Start()
    {
        followSpeed = 10f;
        sensitivity = 100f;
        clampAngle = 70f;
        smoothness = 10f;

        rotX = transform.localRotation.eulerAngles.x;
        rotY = transform.localRotation.eulerAngles.y;

        dirNormalized = realCamera.localPosition.normalized;
        finalDistance = realCamera.localPosition.magnitude;

        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    private void Update()
    {
        rotX += -(Input.GetAxis("Mouse Y")) * sensitivity * Time.deltaTime;
        rotY += Input.GetAxis("Mouse X") * sensitivity * Time.deltaTime;

        rotX = Mathf.Clamp(rotX, -clampAngle, clampAngle);
        Quaternion rot = Quaternion.Euler(rotX, rotY, 0);
        transform.rotation = rot;
    }

    private void LateUpdate()
    {
        transform.position = Vector3.MoveTowards(transform.position, targetObject.position, followSpeed * Time.deltaTime);
        finalDir = transform.TransformPoint(dirNormalized * maxDistance);

        RaycastHit hit;
        if (Physics.Linecast(transform.position, finalDir, out hit))
        {
            finalDistance = Mathf.Clamp(hit.distance, minDistance, maxDistance);
        }
        else
        {
            finalDistance = maxDistance;
        }
        realCamera.localPosition = Vector3.Lerp(realCamera.localPosition, dirNormalized * finalDistance, Time.deltaTime * smoothness);
    }
}
```

Input.GetAxis("Mouse Y")와 Input.GetAxis("Mouse X")로 마우스의 움직임에 따라 움직이는 좌표 X, Y 값을 Update 시켜준다.
회전되는 각도의 한계점을 나타내는 변수는 clampAngle로 마우스가 위 아래로 움직일 때, -70도~70도 까지만 움직이도록 각도를 제어한다.

카메라는 플레이어를 따라가는 움직임을 구현할 때, 다른 Update 함수보다 느린 LateUpdate에서 작성하였다. 

Vector3.MoveTowards 함수를 통해 카메라를 플레이어의 이동 변화량에 따라 같이 이동하도록 구현하였다.


여기서 개선해야할 문제는 카메라가 플레이어 뒤편에서 시야각으로 플레이어를 비추고 있기 때문에, 카메라가 플레이어 뒤편에 있던 어떤 오브젝트 위치와 겹쳐지게 될 시 시야각이 가로막히는 현상이 있을 수 있다.

이를 방지하기 위해 카메라가 장애물과 겹칠때, 그 장애물 앞에 카메라를 이동시키도록 구현하여 개선할 것이다. 

Linecast를 사용하여 카메라로부터 플레이어까지의 이어지는 라인에서 다른 장애물이 있다면 그 장애물의 위치를 검출한다.

장애물이 검출되었다면, 카메라가 플레이어의 좀더 가까이로 이동하기 때문에, 카메라와 플레이어 사이의 거리를 max와 min값을 정해줘야한다. 

이는 카메라가 플레이어 뒤에 최소한 얼마정도 떨어져 있을 것인지, 최대 얼마까지 떨여져 있을 것인지를 의미한다.

평상시에는 max 거리값으로 떨어져 있고, 장애물에 가로막힌 경우, 최소 min 이상되는 거리까지 카메라를 이동시킬 것이다. 

finalDir은 카메라로부터 플레이어까지의 방향에 maxDistance만큼 떨어진 위치 값을 저장한다.

현재 카메라 위치부터 finalDir 까지의 라인을 생성하고 그 사이에 장애물을 검출한다. 장애물이 있다면 장애물 위치로부터 플레이어의 거리를 Clamp로 minDistance ~ maxDistance 사이의 값으로 수정하고 finalDistance 변수에 저장한다.

이제 카메라의 위치만 final Vector값으로 이동시켜주면 된다.
Lerp를 사용하여 final Vector와 카메라 위치 사이에 중간값을 만들고, 그만큼씩 움직이도록하여 좀더 부드럽게 움직임을 구현한다.

여기까지 1번, 2번 목표는 달성했다.

3번을 구현해보자.

나는 Alt키를 누르지 않는 상태에서 마우스를 이동했을때, 플레이어가 카메라가 비추는 방향을 바라보도록 구현할 것이다.
반대로 Alt키를 누른다면 플레이어의 각도는 동일하고, 플레이어만 이동하도록 할 것이다.

PlayerMovement 스크립트를 다음과 같이 수정했다. 

``` c#
public class PlayerMovement : MonoBehaviour
{
    private float moveSpeed;
    [SerializeField]
    private float jumpForce;
    [SerializeField]
    private float walkSpeed;
    [SerializeField]
    private float sprintSpeed;

    private CharacterController characterController;
    private Vector3 moveDir;
    private KeyCode sprintKey;
    private KeyCode jumpKey;
    // 추가된 부분
    private KeyCode cameraToggleKey;
    private Animator animator;

    public enum MovementState
    {
        Walking,
        Sprint,
        Jumping
    }
    public MovementState state;

    // 추가된 부분
    public bool toggleCameraRotation;
    public float smmothness = 10f;

    private void Start()
    {
        jumpForce = 4.0f;
        walkSpeed = 5.0f;
        sprintSpeed = 10.0f;
        moveSpeed = walkSpeed;
        characterController = GetComponent<CharacterController>();
        sprintKey = KeyCode.LeftShift;
        jumpKey = KeyCode.Space;
        // 추가된 부분
        cameraToggleKey = KeyCode.LeftAlt;
        state = MovementState.Walking;
        animator = GetComponent<Animator>();
    }

    private void Update()
    {
        InputSystem();
        moveDir.y += Physics.gravity.y * Time.deltaTime;
        Vector3 forward = transform.TransformDirection(Vector3.forward);
        Vector3 right = transform.TransformDirection(Vector3.right);
        Vector3 up = transform.TransformDirection(Vector3.up);
        moveDir = forward * moveDir.z + right * moveDir.x + up * moveDir.y;
        characterController.Move(moveDir * moveSpeed * Time.deltaTime);
    }

    // 추가된 부분
    private void LateUpdate()
    {
        if (toggleCameraRotation != true)
        {
            Vector3 playerRotate = Vector3.Scale(Camera.main.transform.forward, new Vector3(1, 0, 1));
            transform.rotation = Quaternion.Slerp(transform.rotation, Quaternion.LookRotation(playerRotate), Time.deltaTime * smmothness);
        }
    }

    private void InputSystem()
    {
        // 추가된 부분
        if (Input.GetKey(cameraToggleKey))
        {
            toggleCameraRotation = true;
        }
        else
        {
            toggleCameraRotation = false;
        }

        if (characterController.isGrounded)
        {
            animator.SetBool("IsWalking", true);
            animator.SetBool("IsJumping", false);
            float x = Input.GetAxis("Horizontal");
            float z = Input.GetAxis("Vertical");
            moveDir = new Vector3(x, 0, z);
            if (Input.GetKey(sprintKey) && moveDir.z > 0)
            {
                state = MovementState.Sprint;
                moveDir.x = 0f;
                animator.SetFloat("X", moveDir.x);
                animator.SetFloat("Z", moveDir.z * 2);
                moveSpeed = sprintSpeed;
            }
            else
            {
                state = MovementState.Walking;
                animator.SetFloat("X", moveDir.x);
                animator.SetFloat("Z", moveDir.z);
                moveSpeed = walkSpeed;
            }

            if (Input.GetKey(jumpKey))
            {
                moveDir.y = jumpForce;
                state = MovementState.Jumping;
                animator.SetBool("IsJumping", true);
                animator.SetBool("IsWalking", false);
                animator.SetTrigger("DoJump");
            }
        }
    }
}
```

bool toggleCameraRotation 변수는 Alt키를 눌렀을 때와 아닐 때의 상태를 나타낸다.

LateUpdate 함수에서 player의 Rotation을 카메라가 비추는 방향으로 적용해줄 것인데, y의 값은 예외로 처리할 것이다.
카메라가 비추는 x값과 z값만이 의미를 가지기 때문이다. 따라서 Vector3.Scale을 이용하여 y값은 0을 곱해줌으로써, 계산에 제외시켜준다.
현재 플레이어의 회전값을 수정해줄 것인데, 이 또한 부드러운 움직임을 위해 Slerp를 사용한다.
Slerp와 Lerp의 차이는 직선과 원형의 차이로, 중간값을 만들어주는 기능은 동일하다.
회전은 원형으로 구현되기 때문에 Slerp를 사용한 것 뿐이다.

카메라 회전에 대한 결과는 다음과 같다.

![1](https://github.com/GyeongyeonAn/PythonBasic/assets/55589616/77e16dfa-e6c8-4644-b4ad-9f4c2e506965)














