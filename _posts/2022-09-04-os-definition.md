---
title: OS Definition
author: Develiberta
date: 2022-09-04 12:30:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 운영체제의 정의에 나와있는 용어들을 이해해서, 운영체제의 정의를 이해한다.

## 실천 목표
---
1. "컴퓨터" 용어를 이해한다.
2. "정보" 용어를 이해한다.
3. "처리" 용어를 이해한다.
4. "프로그램" 용어를 이해한다.
5. "운영체제" 용어를 이해한다.

## 용어의 정의 꼬리잡기
---
1. Operating System 운영체제
	- An operating system is
	- a software(program) that operates a computer system

2. Computer 컴퓨터
	- A computer is
	- a machine that processes the information

3. Information 정보
	- An information can be defined as
	- a quantitative representation that measures the uncertainty
	- 즉, 불확실성을 측정해서 양적으로 표현한 것
	- How? 클로드 섀넌
		- 클로드 섀넌(Claude Shannon)은 정보에 대해 수학적으로 정의
			![2022-09-04-os-definition](/assets/img/illustrations/2022-09-04-os-definition.jpg)
			_Copyrightⓒ2022 Develiberta All rights reserved._

4. Process 처리
	- 처리 : 상태 변환
	- 정보의 처리 : 정보의 상태 변환 (0에서 1로, 1에서 0으로)

5. How Computer processes information 컴퓨터는 어떻게 정보를 처리할까
	1. 논리적 관점 용어
		- 부울 대수 (Boolean Algebra): NOT, AND, OR
			- NOT, AND, OR 게이트만으로 모든 계산을 할 수 있다.
			- NAND 게이트만으로 모든 계산을 할 수 있다.
		- 논리 게이트 (Logic Gate) : 부울 대수로 만든, 한 개의 논리 출력을 얻는 회로 - NOT, AND, OR, XOR, NAND, NOR
		- 논리 회로 : 여러 개의 논리 게이트를 모아놓은 것 - IC, LSI, VLSI, ULSI, SoC 등
	2. 물리적 관점 용어
		- 스위치 (Switch) : 0/1, H/T 등의 이진 정보를 표현하는 수단
		- 트랜지스터 (Transistor) : 스위치를 만들 수 있는 물리적인 소자
	3. 컴퓨터의 정보 처리 방법
		- 덧셈 : 반가산기, 전가산기
		- 뺄셈 : 2의 보수 표현법
		- 곱셈과 나눗셈 : 덧셈과 뺄셈의 반복 (단일회로로 하드웨어적으로도 물론 구현 가능)
		- 실수 연산 : 부동 소수점 표현법
		- 함수 : GOTO
		- 트랜지스터로 게이트를 만들면 부울 대수에 의해 모든 연산(삼각함수, 미분, 적분, 사진 촬영, 동영상 재생 등) 가능

6. (명제의 역) 정보를 처리하는 것은 모두 컴퓨터일까
	- NO. "Computer"라고 명명될 조건
		1. 범용성 (Universality)
			- 범용 컴퓨터 (General-purpose Computer)
			- S/W가 하고자 하는 모든 것을 H/W(Computer)가 지원 가능해야 함
				- 예를 들어, 계산기는 계산 외에는 S/W를 통한 다른 작업을 할 수 없으므로 컴퓨터가 아님
		2. 계산가능성 (Computability)
			- Turing-computable : 튜링 머신으로 계산 가능한 것
				- [참고] 정지 문제 (Halting Probolem) : 튜링 머신으로 풀 수 없는 문제
				
7. (쉬어가기) 컴퓨터의 아버지, 할아버지
	1. 컴퓨터의 할아버지
		- 앨런 튜링 Alan Turing : 튜링 머신 Turing Machine
		- 현재 컴퓨터의 구조를 설계
	2. 컴퓨터의 아버지
		- 폰 노이만 John von Neumann : ISA (Instruction Set Architecture) 설계
		- 내장형 프로그램(메모리에 프로그램 저장, fetch-execute 처리) 방식 도입

8. Program 프로그램
	- A program is a set of instructions
	- that tells a computer's hardware to perform a task
	
9. Operating System As a Program 프로그램으로서의 운영체제
	- Operating system
	- is a program running at all times on the computer
	- to provide system services to application programs
	- to manage processes★, resources, user inerfaces, and so on.

10. (다시 음미하기) Operating System 운영체제
	- An operating system is
	- a software(program) that operates a computer system

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard