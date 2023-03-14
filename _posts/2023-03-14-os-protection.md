---
title: Protection
author: Develiberta
date: 2023-03-14 19:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 현대 컴퓨터 시스템에서 보호의 목적과 원칙에 대해 논의한다.
2. 접근 행렬과 결합한 보호 도메인이 프로세스가 접근할 수 있는 자원을 지정하는 데 어떻게 사용되는지 설명한다.
3. 자격- 및 언어- 기반의 보호 시스템을 설명한다.
4. 보호 기법이 시스템 공격을 완화할 방법을 설명한다.

## 실천 목표
---
1. 현대 컴퓨터 시스템에서 보호의 목적과 원칙에 대해 논의한다.
2. 접근 행렬과 결합한 보호 도메인이 프로세스가 접근할 수 있는 자원을 지정하는 데 어떻게 사용되는지 설명한다.
3. 자격- 및 언어- 기반의 보호 시스템을 설명한다.
4. 보호 기법이 시스템 공격을 완화할 방법을 설명한다.

## 용어
---
- 보호 _Protection
	- 컴퓨터 시스템에 의해 정의된 자원에 대한 프로세스와 사용자의 접근을 제어
	- 중요한 원칙은 기법(mechanism)과 정책(policy)를 분리하는 것
		- 기법은 어떤 일을 어떻게 할지 결정
		- 정책은 무엇을 할지 (시스템 관리자가 기법을 바탕으로) 결정

## 보호의 원칙
---
- 최소 권한의 원칙 _The Principle of Least Privilege
	- 프로그램, 사용자, 시스템이 태스크를 수행하는 데 필요한 만큼의 권한만 부여하는 원칙
	- 예) root, sudo, chmod

## 보호의 방법
---
1. 접근 행렬 _Access Matrix
	- 행은 영역을 나타내고 열은 객체를 나타냄
	- 행렬의 각 항은 접근 권한의 집합으로 구성됨
2. 샌드박싱 _Sandboxing
	- 스마트폰에서의 보호
	- 프로세스는 credentials을 받아서 credentials이 있는 행위만 수행 가능
	- 예) Java나 .NET은 JVM이나 Runtime이 사용하지 않는 Native 영역을 직접 접근할 수 없음
3. 코드 서명 _Code signing
	- 스마트폰에서의 보호
	- 앱에 대한 디지털 서명을 한 후 앱스토어에 올리면 앱스토어는 사용자 스마트폰이 이를 다운로드 시 무결성 확인하도록 함

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html