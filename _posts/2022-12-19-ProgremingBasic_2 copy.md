---
layout: single
title:  "프로그래밍 언어 활용"
---

## 1) 함수 포인터의 이해


### 소개


#### 필요성
- 프로그램 코드 간결화
- 배열로 처리함으로써 중복 코드 제거 가능
- 상황에 따른 함수 호출
- 함수를 데이터 형태로 처리 -> 함수의 보관과 전달이 용이


#### 형식
- 리턴타입 (*함수 포인터명)(매개변수리스트);


#### 함수 포인터 배열
- 리턴타입 (*함수 포인터명[배열 사이즈])(매개변수 리스트);
- int (*fp[4])(int, int) = {add, sub, mul, div};


#### 힘수의 매개변수
- void function(int (*fp)(int, int)){}


#### qsort()
- 헤더 : stdlib.h
- 기능 : 테이블의 자료를 퀵 정렬로 내림이나 오름차순으로 정렬
- 매개변수 :
    - void *base : 테이블의 포인터 주소
    - size_t num : 테이블에 들어 있는 실제 데이터 개수
    - size_t size : 한 개 요소의 크기
    - int (* compar)(const void *, const void *) : 두 요소를 비교하기 위한 함수 포인터
    - 반환값 : void
    - 구현 : 비교함수는 직접 구현해야 함 (배열의 자료형과 비교방식이 다르기 때문)


## 2) 파일 입출력 기초


### 개요
- 파일 입출력 : 입력과 출력의 대상이 파일인 경우
- 입출력 대상 : 텍스트 파일, 이진 파일


### 라이브러리 함수
- 텍스트 파일
    - 입력 : fgetc, fgets, fscanf
    - 출력 : fputs, fputs, fprintf
    - 닫기 : fclose
- 이진 파일
    - 입력 : fread
    - 출력 : fwrite


### 입출력 과정
- 스트림 생성 : fopen
- 입출력 : fgets, fputs, fread, fwrite
- 스트림 닫기 : fclose

    