---
layout: single
title:  "[Unity] 모바일 빌드"
---

## iOS 빌드
- Xcode 프로젝트
    - 유니티 빌드시 Xcode 프로젝트가 생성됨
    - Mac에서 Xcode를 이용하여 Build -> Archive를 해야함
- Replace / Append 모드
    - 기존의 Xcode 프로젝트 변동 사항 유지를 위한 Append 모드 제공
- ipa 빌드
    - 유니티에서 바로 ipa빌드 불가


## iOS 빌드 주의사항
- IL2CPP 필수
    - 64비트 단말기 대응
- Target SDK 설정
    - Device SDK (단말기용 빌드)
    - Simulatro SDK (Xcode Simulator용 빌드)
- Enable Bitcode
    - 향후 업데이트와 빠른 다운로드를 제공하기 위한 기술
    - 앱 용량이 커지는 현상, 써드파트 라이브러리 충돌
    - 가급적 No로 설정! (Xcode 프로젝트에서 설정 가능)


## Android 빌드
- ADT / Gradle 프로젝트
    - ADT는 eclipse 또는 ant로 빌드
    - Gradle은 Android Studio 또는 gradle로 빌드
- apk 빌드
    - 유니티에서 바로 apk 빌드 가능
    - Android SDK, Android NDK, JDK 설정 필요
- Append 모드가 없다.


## Android 빌드 주의사항
- 가급적 Gradle 사용
    - ADT는 구글에서 더 이상 지원하지 않음
    - 64k 참조제한, MultiDex 같은 문제는 ADT로 해결 불가능
- apk 빌드 사용 권장
    - ADT / Gradle 프로젝트 생성이 없어 빌드가 빠름
    - KeyStore 생성 / Signing이 유니티에서 가능하여 편함


## PlayerSetting
- 플랫폼 별 빌드 세부설정
- ProjectSettings.asset 파일에 저장
- 여러 종류의 빌드를 위해 C# 스크립트 빌드 셋팅 권장


## PostProcessBuild 활용
- iOS 빌드시 Append 모드의 문제점
    - 모듈 수정/삭제시
        - Xcode 프로젝트에 이전 내용이 그대로 남아있음
    - 유니티 업데이트 시
        - Xcode 프로젝트 생성방식이 변경된 경우 변경 사항 제대로 적용 안됨
    - 그래서 **Append 모드 이제 사용 안함**
    - 대신 **Replace 모드 사용**
    - Xcode 프로젝트 설정은 Post ProcessBuild 활용


![image](https://github.com/GyeongyeonAn/CodingTest/assets/55589616/f4a3c25a-11a5-4bfd-a770-a9a86b133c99)


## 유니티 빌드 자동화
- Jenkins
    - 무료
    - 다양한 버전 관리 시스템과 빌드를 지원
    - 사용자가 많아 참고자료가 풍부하다
    - 플러그인이 굉장히 많다
    - 꽤 많은 빌드 지식 필요
    - 확장성 유연성이 뛰어남
- Unity Cloud Build
    - 유니티 전용 빌드 자동화 서비스
    - 유료 서비스
    - 빌드 전용 맥 또는 PC 필요 없음
    - 매우 간단하고 쉽게 빌드 자동화 셋팅 가능
    - Jenkins 만큼, 세세하고 다양한 설정은 불가능
    - 확장성에 제약이 많음


## 빌드 자동화를 위한 필수 Plugin 설치
- Unity3D Plugin : 유니티 빌드를 위한 플러그인
- Xcode Plugin : iOS 빌드를 위한 플러그인
- Ant Plugin / Gradle Plugin : Android 빌드를 위한 플러그인


## 빌드 프로세스 구성
1. 빌드 전 단계
    - 빌드를 하기 위한 준비 단계
    - 프로젝트 설정, 소스 코드 관리, 빌드 유발, 빌드 환경
2. 빌드 단계
    - 실제로 빌드가 이루어지는 단계
    - Unity3D, Xcode, Ant, Gradle, Shell, Maven, MSBuild
3. 빌드 후 조치
    - 빌드가 끝난 후 테스트, 배포, 알림 등의 작업을 하는 단계
    - ftp, 아마존 S3, Test Flight 등의 배포 작업
    - Email, Slack 등의 알림 작업
