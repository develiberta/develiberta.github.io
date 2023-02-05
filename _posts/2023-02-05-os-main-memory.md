---
title: Main Memroy
author: Develiberta
date: 2023-02-05 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 논리 주소와 물리 주소의 차이점과 주소를 변환할 때 MMU(메모리 관리 장치)의 역할을 설명한다.
2. 메모리를 연속적으로 할당하기 위해 최초, 최적 및 최악 접합 전략을 적용한다.
3. 내부 및 외부 단편화의 차이점을 설명한다.
4. TLB (translation look-aside buffer)가 포함된 페이징 시스템에서 논리 주소를 물리 주소로 변환한다.
5. 계층적 페이징, 해시 페이징 및 역 페이지 테이블을 설명한다.
6. IA-32, x86-64 및 ARMv8 아키텍처의 주소 변환에 관해 설명한다.

## 실천 목표
---
1. 기본 하드웨어에서 메인 메모리의 역할을 이해한다.
2. 프로세스에 메모리 주소가 할당되는 과정을 이해한다.
3. 기호(또는 가상) 메모리 주소와 실제 메모리 주소와의 연결을 이해한다.

## 메인 메모리 개요
---
1. 메인 메모리와 각 처리 코어에 내장된 레지스터들은 CPU가 직접 접근할 수 있는 유일한 범용 저장장치
2. 기계 명령어들은 메모리 주소만을 인수로 취함 (디스크의 주소를 인수로 취하지 않음)
1. 메모리는 각각 주소가 할당된 일련의 바이트들로 구성됨
2. 명령어 실행의 과정
	1. CPU가 PC(Program Counter)가 가리키는 명령어의 주소를 이용해서 메모리로부터 한 명령어를 가져옴
	2. 명령어를 해독하고, 메모리에서 피연산자(operand)를 가져와 피연산자에 대해 명령어를 실행한 후에 계산 결과를 메모리에 다시 저장

## 메모리 주소의 할당
---
1. 원시 프로그램에서 주소는 숫자가 아닌 (변수와 같이) 심볼 형태로 표현됨
2. 컴파일러는 이 심볼 주소를 재배치 가능 주소(예: 이 모듈의 첫 번째 바이트로부터 열네 번째 바이트 주소")로 바인딩
3. 링커(linker)나 로더(loader)가 재배치 가능 주소를 절대 주소(예: 74014번지)로 바인딩
![2023-02-05-os-main-memory-01](/assets/img/illustrations/2023-02-05-os-main-memory-01.jpg)
	- source program에서는 symbolic address 사용
	- object module에서는 relocatable address 사용
	- executable file에서는 logical address 사용
	- program in memory(process)에서는 physical address 사용

## 명령어와 데이터의 바인딩 시점
---
1. 컴파일 시간 (compile time)
	- 프로세스가 메모리 내에 들어갈 위치를 컴파일 시간에 미리 알 수 있으면 컴파일러는 절대 코드를 생성 가능
	- 그러나 만일 이 위치가 변경되어야 한다면 이 코드는 다시 컴파일되어야 함
2. 적재 시간 (load time)
	- 프로세스가 메모리 내 어디로 올라오게 될지 컴파일 시점에 알지 못하면 컴파일러는 일단 이진 코드를 재배치 가능 코드로 생성
	- 이 경우 심볼과 진짜 번지수와의 바인딩은 프로그램이 메인 메모리로 실제로 적재되는 시간에 이루어짐
3. 실행 시간 (execution time)
	- 프로세스가 실행하는 중간에 메모리 내의 한 세그먼트로부터 다른 세그먼트로 옮겨질 수 있다면 바인딩이 실행시간까지 허용된 것으로 봄
	- 이것이 가능하려면 특별한 하드웨어 필요

## 동적 연결 및 공유 라이브러리 (Dynamic Linking & Shared Libraries)
---
1. 동적 연결 라이브러리 (DLL)는 사용자 프로그램이 실행될 때, 사용자 프로그램에 연결되는 시스템 라이브러리
2. 연결(linking)이 실행 시기까지 미루어지는 것
3. 프로그램이 동적 라이브러리에 있는 루틴을 참조하면 로더는 DLL을 찾아 필요한 경우 메모리에 적재하고, 동적 라이브러리의 함수를 참조하는 주소를 DLL이 저장된 메모리의 위치로 조정
4. 장점
	- 실행 가능 이미지의 크기를 감소시키고 메인 메모리를 절약할 수 있음 
	- 라이브러리를 여러 프로세스 간에 공유할 수 있어서 메인 메모리에 DLL 인스턴스가 하나만 있을 수 있음

## 논리 주소와 물리 주소의 바인딩
---
1. 논리 주소 (logical address)
	- CPU가 생성하는 주소
2. 물리 주소 (physical address)
	- 메모리가 취급하게 되는 주소로, 메모리 주소 레지스터(MAR)에 주어지는 주소
3. 논리 주소와 물리 주소의 비교
	1. 컴파일(compile) 또는 적재(load) 시에 주소를 바인딩하면 논리 주소와 물리 주소가 같음
	2. 실행(execution) 시에 주소를 바인딩하면 논리 주소와 물리 주소가 다름
		- 이때 논리 주소를 가상 주소(virtual address)라고 부름
		- 프로그램의 실행 중에 가상 주소를 물리 주소로 바꾸는 변환(mapping) 작업은 하드웨어 장치인 메모리 관리 장치(MMU, Memory Management Unit)가 수행
		- 여러 가지 변환(mapping) 기법이 존재하지만 여기에서는 기준 레지스터(base register) 기법을 일반화시킨 단순한 MMU 기법에 따른 변환을 설명할 것 - (*)

## 기준 레지스터(base register) 기법을 일반화시킨 단순한 MMU 기법에 따른 변환 - (*)
---
1. 기준 레지스터(base register) 기법
	- 프로세스가 독립된 메모리 공간을 가지도록 보장하기 위한 단순한 기법
	- 개별적인 메모리 공간을 분리하기 위해서 특정 프로세스만 접근할 수 있는 합법적인(legal) 메모리 주소 영역 설정
	- 기준(base)와 상한(limit)이라 불리는 두 개의 레지스터들을 사용
	![2023-02-05-os-main-memory-02](/assets/img/illustrations/2023-02-05-os-main-memory-02.jpg)
		- 기준 레지스터(base register)는 가장 작은 하법적인 물리 메모리 주소의 값을 저장
		- 상한 레지스터(limit register)는 주어진 영역의 크기를 저장
	- 잘못된 접근인 경우 segmentation falut 발생하며 프로세스 중단
	![2023-02-05-os-main-memory-03](/assets/img/illustrations/2023-02-05-os-main-memory-03.jpg)
2. 재배치(relocation) 레지스터
	- 위의 기준 레지스터(base register) 기법을 일반화시켜서 단순한 MMU 기법을 설명할 때, 재배치(relocation) 레지스터라고 부름
	![2023-02-05-os-main-memory-04](/assets/img/illustrations/2023-02-05-os-main-memory-04.jpg)
	- 사용자 프로그램은 결코 실제적인 물리 주소에 접근하지 않음
	- 그러나 그것이 일단 주소로 갈 때는 재배치 레지스터(relocation register)에 의해 다시 바인딩됨
	- 연속적인 메모리 할당이 가정됨

## 연속적인 메모리 할당 Contiguous Memory Allocation
---
연속적인 메모리 할당에서 각 프로세스는 다음 프로세스가 적재된 영역과 인접한 하나의 메모리 영역에 적재됨
1. 메모리 보호
	- 재배치 레지스터는 가장 작은 물리 주소의 값을 저장하고, 상한 레지스터는 논리 주소의 범위 값을 저장
	- MMU는 동적으로 논리 주소에 재배치 레지스터의 값을 더해서 주소를 변환하는 역할
	- 변환된 주소는 메모리로 보내짐
	- CPU 스케줄러가 다음에 수행할 프로세스를 선택할 때, 디스패처(dispatcher)는 문맥 교환의 일환으로 재배치 레지스터와 상한 레지스터에 정확한 값을 적재
	![2023-02-05-os-main-memory-05](/assets/img/illustrations/2023-02-05-os-main-memory-05.jpg)
2. 메모리 할당
	- 메모리를 할당하는 가장 간단한 방법 중 하나는 프로세스를 메모리의 가변 크기 파티션에 할당하는 것
	- 가변 파티션 기법에서 운영체제는 사용 가능한 메모리 부분과 사용 중인 부분을 나타내는 테이블을 유지
	- (용어) hole : 하나의 큰 사용 가능한 메모리 블록
	![2023-02-05-os-main-memory-06](/assets/img/illustrations/2023-02-05-os-main-memory-06.jpg)
	- 동적 메모리 할당 문제 (dynamic storage allocation problem)
		- 일련의 메모리 가용 공간-리스트로부터 크기 n-바이트 블록을 요구하는 프로세스를 어떻게 실행할 것인가를 결정하는 문제
			- 최초 적합 : 첫 번째 사용 가능한 가용 공간을 할당
			- 최적 적합 : 사용 가능한 공간 중에서 가장 작은 것을 택함
			- 최악 적합 : 가장 큰 가용 공간을 택함
3. 단편화 fragmentation
	1. 외부 단편화(external fragmentation)
	![2023-02-05-os-main-memory-06](/assets/img/illustrations/2023-02-05-os-main-memory-06.jpg)
		- 프로세스들이 메모리에 적재되고 제거되는 일이 반복될 때 작은 hole이 많이 생기는 현상
		- continuous memory allocation으로 인해 발생
		- 이를 해결하는 방법으로 압축(compaction), 페이징(paging)를 들 수 있음
	2. 내부 단편화(internal fragmentation)
		- 일반적으로 메모리를 먼저 아주 작은 공간들로 분할하고 프로세스가 요청하면 할당을 항상 이 분할된 크기의 정수배로만 함
		- 할당된 공간은 요구된 공간보다 약간 더 클 수 있는데 이들 두 크기 사이의 남는 부분이 생기는 현상
		- paging으로 인해 발생

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html