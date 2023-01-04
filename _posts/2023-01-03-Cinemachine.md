---
layout: single
title:  "[Unity] Cinemachine 사용하기"
---

## 시네머신이란?
- 시네머신은 유니티 게임 안의 카메라, 시네마틱, 컷씬을 자동화할 수 있는 강력한 도구 모임이다.
- 모든 형태의 카메라 움직임, 다른 카메라로의 전환, 기타 복잡한 형태의 동작을 자동화할 수 있다.


## 시네머신 설치하기
window -> Package Manager -> 상단에 Packages: Unity Registry로 되어있는지 확인한다 -> Cinemachine 설치

![image](https://user-images.githubusercontent.com/55589616/210342318-37b8e369-425c-47c9-adee-f415aafafc9e.png)


## 시네머신 카메라로 Player 추적하기
Hierarchy -> Cinemachine -> 2D 카메라 선택

CM vcam1 게임 오브젝트의 인스펙터 창에서 시네머신 가상 카메라 컴포넌트의 Follow 속성에 PlayerObject를 적용해준다.

![image](https://user-images.githubusercontent.com/55589616/210343868-b931acc8-30e6-403f-996f-049709a1515b.png)


## 가상 카메라란?
- 카메라맨과 유사하다.
- 메인 카메라의 방향을 바꾸거나 이동 방식을 지정하는 간단한 조종기
    - 따라다닐 대상을 설정
    - 지정한 경로를 따라 움직임
    - 지정한 경로로 가다가 경로를 바꿈


## 시네머신 제한자
- 플레이어가 화면의 가장자리에 가까이 갔을 때 카메라가 움직이지 않도록 한다.

가상머신 카메라 -> Extensions의 Add Extention 옆의 드롭다운에서 CinemathineConfiner를 선택한다.

![image](https://user-images.githubusercontent.com/55589616/210353993-80db8544-9c8f-4a6d-83e0-b0f0610dc5e9.png)

맵 Object에 Polygon Collider 2D를 추가하고 사각형 형태로 조정한다.
is Trigger 체크를 해준다. (Player와의 충돌을 막기 위해)

![image](https://user-images.githubusercontent.com/55589616/210357292-6002ba69-1046-4607-aefb-56ae93bb8915.png)

가상 카메라 오브젝에 있는 CinemathineConfiner 컴포넌트 안 Bounding Shape 2D 속성에 맵 Object를 적용해준다.

![image](https://user-images.githubusercontent.com/55589616/210357544-cd3561b7-8574-44e4-841f-117c1b6df642.png)

이제 제한자는 맵 오브젝트의 콜라이더를 제한자의 경계로 사용한다.


## 지터링 현상 제거하기
- 플레이어로 맵을 돌아다니다 보면 약간의 화면 떨림(지터링 현상)이 느껴질 수 있다. 
- 이 현상의 원인은 플레이어는 픽셀 단위로 움직이지만, 플레이어를 따라가는 카메라는 픽셀보다 작은 단위로 움직이기 때문이다. 
- 지터링을 없애려면 시네머신 가상 카메라의 최종 위치가 픽셀 단위로 맞아 떨어져야 한다.
- 아래 스크립트를 가상머신 카메라에 컴포넌트로 추가한다.

``` c#
using UnityEngine;
using Cinemachine;
 
public class RoundCameraPos : CinemachineExtension
{
    public float PixelsPerUnit = 32;

    // Called by Cinemachine after the Confiner is done with its processing pipeline

    protected override void PostPipelineStageCallback(
        CinemachineVirtualCameraBase vcam,
        CinemachineCore.Stage stage, ref CameraState state, float deltaTime)
    {
        // Check to see what stage of post-processing we're in
        if (stage == CinemachineCore.Stage.Body)
        {
            // Get the VC's final position
            Vector3 finalPos = state.FinalPosition;

            // Call the method we wrote to round the position
            Vector3 newPos = new Vector3(Round(finalPos.x), Round(finalPos.y), finalPos.z);
            // Set the VC's new position to the difference between the old 
            // position and the new rounded position that we just calculated
            state.PositionCorrection += newPos - finalPos;
        }
    }
 
    float Round(float x)
    {
        return Mathf.Round(x * PixelsPerUnit) / PixelsPerUnit;
    }
}
```