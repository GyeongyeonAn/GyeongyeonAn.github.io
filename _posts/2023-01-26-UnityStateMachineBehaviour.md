---
layout: single
title:  "[Unity] StateMachineBehaviour"
---


## 1) 상태 머신 동작(State Machine Behaviour)
- 일반 Unity 스크립트(MonoBehaviours)를 개별 게임 오브젝트에 연결하는 것과 유사한 방법으로 StateMachineBehaviour 스크립트를 상태 머신에 개별 상태로 연결할 수 있다. 
- 상태 머신이 특정 상태로 전환되거나, 상태를 종료하거나, 해당 상태로 유지될 때 실행할 코드를 작성할 수 있다. 
- 이를 사용하면, 상태를 테스트하거나 상태 변경을 감지하기 위해 로직을 직접 작성할 필요가 없다.
- 활용 예
    - 상태를 시작 또는 종료할 때 사운드 재생
    - 적절한 상태에 있을 때만 특정 테스트(예: 지면 탐지) 수행
    - 특정 상태와 연관된 특수 효과 활성화 및 제어

![image](https://user-images.githubusercontent.com/55589616/214753179-0b8e0f78-75e2-4835-93d7-0150cdd1f577.png)


## 2) Public 함수
- OnStateMachineEnter
    - StateMachine으로 전환할 때 첫 번째 업데이트 프레임에서 호출된다.
    - 이것은 StateMachine 하위 상태로 전환할 때 호출되지 않는다.
- OnStateMachineExit
    - StateMachine에서 전환할 때 마지막 업데이트 프레임에서 호출된다. 
    - 이것은 StateMachine 하위 상태로 전환할 때 호출되지 않는다.


## 3) 오버라이드 함수
- OnStateEnter() : 상태머신이 이 상태를 평가할 때 첫 번째 업데이트 프레임에서 호출된다.
- OnStateUpdate() : 첫 번째 프레임과 마지막 프레임을 제외한 각 업데이트 프레임에서 호출된다.
- OnStateExit() : 상태머신이 이 상태를 평가할 때 마지막 업데이트 프레임에서 호출된다.
- OnStateMove() : MonoBehaviour.OnAnimatorMove 직후에 호출된다.
- OnStateIK() : MonoBehaviour.OnAnimatorIK 직후에 호출된다.


``` c#
using UnityEngine;

public class AttackBehaviour : StateMachineBehaviour
{
	public GameObject particle;
	public float radius;
	public float power;
	
	protected GameObject clone;
	
	override public void OnStateEnter(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
	{
		clone = Instantiate(particle, animator.rootPosition, Quaternion.identity) as GameObject;
		Rigidbody rb = clone.GetComponent<Rigidbody>();
		rb.AddExplosionForce(power, animator.rootPosition, radius, 3.0f);
	}
	override public void OnStateExit(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
	{
		Destroy(clone);
	}
	override public void OnStateUpdate(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
	{
		Debug.Log("On Attack Update ");
	}
	override public void OnStateMove(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
	{
		Debug.Log("On Attack Move ");
	}
	override public void OnStateIK(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
	{
		Debug.Log("On Attack IK ");
	}
}
```