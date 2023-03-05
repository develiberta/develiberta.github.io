---
title: Virtual Memroy
author: Develiberta
date: 2023-02-19 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 물리적 스토리지의 구조를 이해한다.
2. 논리적 스토리지에서 파일을 저장하는 방법을 설명한다.

## 실천 목표
---
1. 물리적 스토리지의 구조를 이해한다.
2. 논리적 스토리지에서 파일을 저장하는 방법을 설명한다.

## 물리적 스토리지
---
- 비휘발성으로 컴퓨터의 보조적인 저장장치
- HDD, SDD, Flash Memory, NAND Memory
- magnetic tapes, optical disks, cloud storage
- HDD moving-head disk mechanism
![2023-03-05-os-storage-management-01](/assets/img/illustrations/2023-03-05-os-storage-management-01.jpg)

## HDD Scheduling
---
- 스케줄링은 seek time을 최소화하고 bandwidth를 최대화하는 방향으로 진행
	- seek time
		-
	- disk bandwidth
		-

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
	- $$EAT = (1-p) * ma + p * (page fault time)$$
	- 각 변수의 의미
		- EAT = Effective Access Time
		- ma = memory access time
		- p = probability of a page fault (ma의 수백 배, 수만 배에 달함)
		- page fault time = 페이지 폴트를 처리하는 데에 걸리는 시간 (page를 읽는 데에 가장 많은 시간이 소요됨)
8. 요구 페이징의 두 가지 주요 문제
	- 페이지 교체 알고리즘
		- 교체할 프레임을 어떻게 선택할 것인가
	- 프레임 할당 알고리즘
		- 각 프로세스에 얼마나 많은 프레임을 할당한 것인가

## 페이지 교체 알고리즘
---
1. 개요
	- 가용 프레임(free frame)이 존재하지 않는 경우, 현재 사용되고 있지 않은 프레임 공간을 찾아서 비워주고 (swap space$$=$$secondary storage로 옮겨주고) 프레임이 가용하도록 만들어야 함
	![2023-02-19-os-virtual-memory-09](/assets/img/illustrations/2023-02-19-os-virtual-memory-09.jpg)
2. 평가
	- 동일한 reference string(메모리 레퍼런스를 페이지 번호 단위로 나열한 문자열)에 대해 page faults의 발생 수가 최소인 것을 성능이 높은 것으로 평가
	![2023-02-19-os-virtual-memory-10](/assets/img/illustrations/2023-02-19-os-virtual-memory-10.jpg)
3. 종류
	1. FIFO 페이지 교체 알고리즘
		- First-In-First-Out
		- 가장 오래된 페이지를 교체
		![2023-02-19-os-virtual-memory-11](/assets/img/illustrations/2023-02-19-os-virtual-memory-11.jpg)
		- FIFO의 심각한 문제는 Belady's Anomaly로, 할당된 프레임과 평가 성능이 비례하지 않는 지점들이 존재한다는 것
		![2023-02-19-os-virtual-memory-12](/assets/img/illustrations/2023-02-19-os-virtual-memory-12.jpg)
	2. 최적(Optimal) 페이지 교체 알고리즘
		- 이상적인 페이지 교체 알고리즘으로, page faults 발생 수를 최소화함
		- 미래에 가장 늦게 사용될 프레임을 교체
		- 미래 정보가 필요하므로 다른 페이지 알고리즘에 대한 비교 연구로만 사용됨
		![2023-02-19-os-virtual-memory-13](/assets/img/illustrations/2023-02-19-os-virtual-memory-13.jpg)
	3. LRU 페이지 교체 알고리즘
		- Least Recently Used
		- 미래는 과거를 보면 알 수 있음 $$\longrightarrow$$ recent past로 near future 예측 가능
		- 페이지의 사용 기록을 저장해서 최근에 가장 사용되지 않은 페이지를 교체
		![2023-02-19-os-virtual-memory-14](/assets/img/illustrations/2023-02-19-os-virtual-memory-14.jpg)
		- 최근에 가장 사용되지 않은 페이지를 판단하기 위해서는 자료구조가 복잡해지거나 메모리 용량을 많이 차지하거나 속도가 저하될 수 있으므로 하드웨어의 지원을 받는 방법이 있음
			- counter
				- 페이지가 참조될 때마다 counter나 clock을 복사해서 가장 작은 값을 가진 페이지를 교체
			- stack
				- 스택에 페이지 번호를 저장하고 페이지가 참조될 때마다 페이지 번호가 (중간에 있는 경우) 스택의 중간에서 제거되어 스택 꼭대기(top)에 놓이게 됨
				- 스택의 꼭대기(top)는 항상 가장 최근에 사용된 페이지고, 밑바닥(bottom)은 가장 오랫동안 이용되지 않은 페이지
				- 이때 Douly Linked List로 구현되는데, 리스트의 tail 포인터가 스택의 밑바닥(bottom)을 가리키고 있게 됨
				![2023-02-19-os-virtual-memory-15](/assets/img/illustrations/2023-02-19-os-virtual-memory-15.jpg)
		- 그러나 하드웨어 도움 없이도 많은 시스템이 reference bit를 지원
			- reference bit이란
				- 각 페이지에 포함된 속성으로 0 또는 1의 값을 가짐
				- 페이지가 참조될 때 0에서 1로 바뀜
			- reference bit가 0인 페이지를 교체
	4. Second-Chance 페이지 교체 알고리즘
		- FIFO 페이지 교체 알고리즘 + refernce bit(를 이용한 Secondary Chance)
		- 기본적으로 FIFO 페이지 교체 알고리즘을 사용
		- refrence bit에 따라서
			- reference bit가 0인 경우 페이지를 교체
			- reference bit가 1인 경우 이를 0으로 바꾸고 페이지는 교체하지 않은 채 페이지 교체할 대상의 후보를 그 다음 페이지로 넘어감
		![2023-02-19-os-virtual-memory-16](/assets/img/illustrations/2023-02-19-os-virtual-memory-16.jpg)

## 프레임 할당 알고리즘
---
1. 할당 알고리즘
	- 균등 할당
		- 각 프로세스의 모두 같은 개수의 프레임을 할당
	- 비례 할당
		- 각 프로세스의 크기 비율에 맞추어 프레임을 할당
2. 전역 대 지역 할당
	- 전역 교체
		- 각 프로세스가 교체할 프레임을 다른 프로세스에 속한 프레임을 포함한 모든 프레임을 대상으로 찾는 경우
	- 지역 교체
		- 각 프로세스가 자기에게 할당된 프레임 중에서만 교체할 프레임을 찾는 경우

## 스레싱 _Thrashing
1. 원인
	- 프로세스에 충분한 프레임이 없는 경우, 작업 집합의 페이지를 지원하는데 필요한 최소 프레임도 없는 경우 발생 (멀티프로그래밍 정도가 높은 경우 발생)
	- 페이지 폴트가 계속해서 발생하며 이에 따라 swapping page in out이 발생
	![2023-02-19-os-virtual-memory-17](/assets/img/illustrations/2023-02-19-os-virtual-memory-17.jpg)
2. 결과
	- CPU 사용률은 낮아지고 Memory 사용률은 높아짐
2. 해결
	- 작업 집합 모델 _Workinhg-Set Model
		- 프로그램 지역성을 바탕으로 하는 모델
		![2023-02-19-os-virtual-memory-18](/assets/img/illustrations/2023-02-19-os-virtual-memory-18.jpg)
		- $$\vartriangle$$를 크기로 하는 working-set window(sliding window)로 각 프로세스가 필요로 하는 최소한의 프레임 개수 보장
		- page가 현재 사용된다면 working-set window에 속해있을 것이고, 사용되지 않는다면 속해있지 않을 것이라는 것이 기본 전제
		![2023-02-19-os-virtual-memory-19](/assets/img/illustrations/2023-02-19-os-virtual-memory-19.jpg)

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html