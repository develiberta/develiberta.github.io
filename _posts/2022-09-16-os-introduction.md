---
title: OS Introduction
author: Develiberta
date: 2022-09-16 20:30:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 운영체제의 역할을 전반적으로 이해한다.
2. 컴퓨터 시스템의 구성 및 구조를 이해한다.

## 실천 목표
---
1. 컴퓨터 시스템의 일반적인 구성과 인터럽트의 역할을 이해한다.
2. 현대 다중 처리기 컴퓨터 시스템의 구성요소에 관해 이해한다.
3. 사용자 모드에서 커널 모드로의 전환에 대해 이해한다.
4. 다양한 컴퓨팅 환경에서 운영체제가 어떻게 사용되는지 이해한다.

## 운영체제의 역할
---
1. 사용자 관점
	- 사용의 용이성 : 사용자가 수행하는 작업을 최대화

2. 시스템 관점
	- 자원 할당자 : 효율적이고 공정하게 자원 할당 결정
	- 제어 프로그램 : 부적절한 사용 방지 위해 사용자 프로그램의 수행 제어

## 컴퓨터 시스템의 구성 속의 운영체제
---
1. 전형적인 컴퓨터 시스템의 구성
	1. 구성도
		- ![2022-09-16-os-introduction-01](/assets/img/illustrations/2022-09-16-os-introduction-01.png)
	2. 구성요소
		- CPU (Central Processing Unit, 중앙처리장치) 1개+
		- 각 장치에 대한 장치 컨트롤러
		- 시스템 버스

```		
2. 다시 보는 운영체제의 역할, 특히 시스템 관점
	1. 자원 할당자 : 효율적이고 공정하게 자원 할당 결정
	2. 제어 프로그램 : 부적절한 사용 방지 위해 사용자 프로그램의 수행 제어
```
	
3. 위의 역할, 운영체제가 어떻게?
	1. 일단 운영체제가 메모리에 적재되어야 : 부트스트랩(bootstrap) 프로그램
		- EEP ROM(비휘발성)에 저장되어 있는 프로그램
		- 컴퓨터에 전원이 공급되면 HDD에 존재하는 OS를 메모리(RAM, Random Access Memory, 휘발성)에 적재 (이후에 이를 CPU가 fetch-execute)
			- 참고 : 저장 장치 구조
				- 용량, 접근 속도에 따른 위계 구조
				- EEP ROM과 RAM은 main memory에 포함되는 개념
				- 하지만 보통 memory, main memory 라는 용어는 RAM만 지칭
				- ![2022-09-16-os-introduction-02](/assets/img/illustrations/2022-09-16-os-introduction-02.jpg)
	2. 운영체제가 하드웨어의 신호를 받을 수도 있어야 : 인터럽트(interrupt)
		- 운영체제와 하드웨어(특히 입출력 장치)의 상호 작용 방식의 핵심
		- 장치의 작업이 완료되면 장치 컨트롤러는 장치 드라이버(운영체제의 일부)에 작업 완료 신호(=인터럽트) 전송
		- ![2022-09-16-os-introduction-03](/assets/img/illustrations/2022-09-16-os-introduction-03.jpg)
		- ![2022-09-16-os-introduction-04](/assets/img/illustrations/2022-09-16-os-introduction-04.png)
		
4. 옛날에 전형적이었던 컴퓨터 시스템의 구성 vs 요즘 전형적인 컴퓨터 시스템의 구성
	```
	1. 위에서 언급한 (옛날에 전형적이었던) 구성도
		- Single Processor System
		- ![2022-09-16-os-introduction-01](/assets/img/illustrations/2022-09-16-os-introduction-01.png)
		- 1 CPU 1 Memory
		- 더이상 사용하지 않음
	2. 요즘 구성도를 보기 전에 용어 설명
		- CPU : 명령어를 execute하는 하드웨어
		- Processor : 1개 이상의 CPU를 포함하는 물리적인 칩이지만 점차 CPU와 같은 용어로 불리어짐
		- Core : CPU의 구성요소로, CPU의 기본 계산 단위 (명령을 실행하고, 레지스터를 이용해 로컬에 데이터를 저장)
		- Multiprocessor : 한 컴퓨터에 Processor(CPU)가 여러 개
		- Multicore : 한 CPU에 Core가 여러 개
			- ![2022-09-16-os-introduction-05](/assets/img/illustrations/2022-09-16-os-introduction-05.jpg)
	```
	3. 요즘 구성도
		- Multi Processor System
		- ![2022-09-16-os-introduction-06](/assets/img/illustrations/2022-09-16-os-introduction-06.jpg)
		- 특히 보편적인 것은 (위의 그림과 같은) Symmetric Multi Processing(SMP)
			- 보통 이를 Multiprocessing이라고 지칭
			- 각 CPU는 (특정한 작업만 담당하는 게 아니라) 모든 작업을 수행 가능
			- CPU가 하나의 작업만 도맡아 하는 게 아니므로, 작업들은 모든 CPU에 의해 처리되며 빈번하게 문맥 교환 발생
			- 문맥 교환 시 어떤 작업을 먼저 처리할지(즉, 어떤 작업에 자원을 먼저 할당할지) 결정하는 게 CPU 스케줄링 : 운영체제의 역할 중 하나
			- 메모리에 여러 프로세스를 적재하는 Multiprogramming(CPU가 항상 한 개의 작업은 실행할 수 있도록 함)가 선행되어야 함
		- 이게 가능하면 CPU 1개로 메모리에 여러 OS를 적재할 수도 있는 것 아닌가 하는 의문
			- 가상화(Virtualization)란 한 컴퓨터의 하드웨어를 추상화해서 여러 환경 속에서 수행될 수 있도록 하는 기술
			- 이 때 사용되는 소프트웨어가 VMM(Virtaul Machine Manager, 가상 머신 관리자)
				- (예) VMWare, XEN, WSL
			- (a) 하나의 OS 사용 (b) 세 개의 가상머신 사용
				- ![2022-09-16-os-introduction-08](/assets/img/illustrations/2022-09-16-os-introduction-08.png)

## 운영체제의 동작
---
1. 사용자 모드 : 사용자가 응용 프로그램을 실행하는 모드

2. 커널 모드 : 시스템 프로그램이 실행되는 모드

3. 시스템 콜 : 사용자 모드와 커널 모드를 상호 전환하도록 하는 인터페이스
	- 시스템 콜은 OS의 API로 일종의 인터페이스인데, 이 인터페이스는 (현재 가용한) OS 서비스로 연결됨
	- 시스템 콜에 의해 사용자 모드에서 커널 모드로 전환됨
	- 시스템 콜이 종료되면 커널 모드에서 사용자 모드로 전환됨
	- ![2022-09-16-os-introduction-07](/assets/img/illustrations/2022-09-16-os-introduction-07.png)
	- 대부분의 언어에서는 표준 라이브러리에 시스템 콜 수행을 위한 함수를 포함하며, 이를 이용해서 간단하게 시스템 콜하는 코드 작성 가능
		- (예) printf(), fork(), wait()
		
## 그래서, 운영체제의 역할
---
```
1. 사용자 관점
	- 사용의 용이성 : 사용자가 수행하는 작업을 최대화

2. 시스템 관점
	- 자원 할당자 : 효율적이고 공정하게 자원 할당 결정
	- 제어 프로그램 : 부적절한 사용 방지 위해 사용자 프로그램의 수행 제어
```
![2022-09-16-os-introduction-09](/assets/img/illustrations/2022-09-16-os-introduction-09.jpg)
1. 사용자 인터페이스의 제공
2. 시스템 콜을 통한 사용자 모드와 커널 모드의 전환 가능
3. 자원을 관리하고 할당 : 프로그램을 실행하도록 함
4. 장치가 인터럽트 신호를 보내면 장치에 자원 할당
5. 부적절한 사용 방지 위해 에러 검출 및 보안 기능 제공

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
	
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일