---
title: Virtual Memroy
author: Develiberta
date: 2023-02-19 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 가상 메모리를 정의하고 그 이점을 설명한다
2. 요구 페이징을 사용하여 페이지가 메모리에 적재되는 방법을 설명한다.
3. FIFO, 최적 및 LRU 페이지 교체 알고리즘을 적용한다.
4. 프로세스의 작업 집합을 설명하고 프로그램 지역성과 어떤 관련이 있는지 설명한다.
5. Linux, Windows 10 및 Solaris가 가상 메모리를 관리하는 방법을 설명한다.
6. C 프로그래밍 언어로 가상 메모리 관리자 시뮬레이션을 설계한다.

## 실천 목표
---
1. 가상 메모리가 무엇인지 이해하고 필요성을 설명한다.
2. 요구 페이징을 이용해서 페이지가 메모리에 적재되는 과정을 이해한다.
3. 페이지 교체 알고리즘을 설명하고 문제에 대해 각 페이지 교체 알고리즘을 이용해서 해결한다.

## 가상 메모리 개요
---
1. 프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법
2. 물리 메모리로부터 프로그래머 관점의 논리 메모리를 분리해서 메인 메모리를 균일한 크기의 저장 공간으로 구성된 엄청나게 큰 배열로 추상화
	- 물리 메모리보다 큰 가상 메모리를 보여주는 다이어그램  
	![2023-02-19-os-virtual-memory-01](/assets/img/illustrations/2023-02-19-os-virtual-memory-01.jpg)
	- 프로세스 가상 주소 공간 in 메모리  
	![2023-02-19-os-virtual-memory-02](/assets/img/illustrations/2023-02-19-os-virtual-memory-02.jpg)

## 가상 메모리 이점
---
1. 프로그래머는 메모리의 크기 제약으로부터 자유로워짐
	- 각 프로그램이 더 작은 메모리를 차지하므로 더 많은 프로그램을 동시에 수행 가능
	- 프로그램을 메모리에 올리고 스왑(swap)하는 데 필요한 I/O 회수가 줄어들기 때문에 프로그램들이 보다 빠르게 실행됨
2. 파일과 라이브러리의 공유를 수월하게 해주고 공유 메모리 구현을 가능하게 함
	- 가상 메모리를 사용한 공유 라이브러리  
	![2023-02-19-os-virtual-memory-03](/assets/img/illustrations/2023-02-19-os-virtual-memory-03.jpg)
	- (Copy on Write) Parent 프로세스가 페이지 C를 수정하기 전  
	![2023-02-19-os-virtual-memory-07](/assets/img/illustrations/2023-02-19-os-virtual-memory-07.jpg)
	- (Copy on Write) Parent 프로세스가 페이지 C를 수정한 후  
	![2023-02-19-os-virtual-memory-08](/assets/img/illustrations/2023-02-19-os-virtual-memory-08.jpg)
3. 프로세스 생성을 효율적으로 처리할 수 있는 기법도 제공

## 요구 페이징
---
1. 프로세스 실행 중 필요할 때만 페이지가 적재되어서 접근되지 않은 페이지는 물리 메모리로 적재되지 않음
2. 프로세스가 실행되는 동안 일부 페이지는 메모리에 있고 일부 페이지는 보조저장장치에 있음
	- swap in과 swap out  
	![2023-02-19-os-virtual-memory-04](/assets/img/illustrations/2023-02-19-os-virtual-memory-04.jpg)
3. 위의 둘을 구분하기 위해서는 하드웨어 지원이 필요
	- (예) valid-invalid bit 기법
		- 일부 페이지가 메인 메모리에 없을 때의 페이지 테이블  
		![2023-02-19-os-virtual-memory-05](/assets/img/illustrations/2023-02-19-os-virtual-memory-05.jpg)
4. 페이지 폴트(page fault)를 처리하는 과정
![2023-02-19-os-virtual-memory-06](/assets/img/illustrations/2023-02-19-os-virtual-memory-06.jpg)
	- 프로세스에 대한 내부 테이블(internal table)을 검사해서 그 메모리 참조(reference)가 유효한지 여부를 검사
		- 유효 (valid) : 페이지가 접근이 허용되고 메모리 상에 존재
		- 무효 (invalid) : 페이지가 접근이 허용되지 않거나 메모리 상에 존재하지 않음 (보조기억장치에 존재)
	- 무효한 경우 중 접근이 허용되지만 해당 페이지가 메모리에 올라오지 않았다면 보조기억장치로부터 가져오는 이하의 과정 수행
		- 무효한 경우 중 접근이 허용되지 않는 페이지 대한 참조라면 프로세스는 중단
	- 빈 공간, 즉 가용 프레임(free frame)를 찾음
		- 예를 들어, 페이지 프레임 리스트(Linked List로, 운영체제가 유지)에서 하나를 가져옴
	- 해당 페이지를 읽어들이도록 요청 (보조저장장치 $$\rightarrow$$ 새롭게 할당된 프레임)
	- 보조저장장치 읽기가 끝나면, 이 페이지가 이제는 메모리에 있다는 것을 알리기 위해 페이지 테이블 갱신 $$\approx$$ (프로세스가 유지하고 있는) 내부 테이블 수정
	- 트랩에 의해 중단되었던 명령어를 **처음부터** 다시 수행 (restart)
5. 참조의 지역성 _Locality of Reference
	- 모든 프로그램은 참조의 지역성(Locality of Reference) 성질이 있어서 프로그램의 어느 한 특정 작은 부분만 한동안 집중적으로 참조하기 때문에
	- 요구 페이징은 만족할만한 성능을 보임
	- Locality of Code, Locality of Data $$\in$$ Locality of Reference
6. 하드웨어의 지원
	- 요구 페이징을 지원하기 위해 필요한 하드웨어는 페이징과 스와핑을 위한 하드웨어와 동일
		- 페이지 테이블 $$\ni$$ 보호 비트(protection bit)
		- 보조저장장치(secondary memory) $$\approx$$ 스왑 공간(swap space)
7. 요구 페이징의 성능
	- $$EAT = (1-p) * ma + p * (page fault time) $$
	- 각 변수의 의미
		- EAT = Effective Access Time
		- ma = memory access time
		- p = probability of a page fault (ma의 수백 배, 수만 배에 달함)
		- page fault time = 페이지 폴트를 처리하는 데에 걸리는 시간 (page를 읽는 데에 가장 많은 시간이 소요됨)

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html