---
title: OS Synchronization Tools
author: Develiberta
date: 2022-11-12 13:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 협력적 프로세스가 동시에 데이터에 접근할 때 발생하는 문제를 이해한다.

## 실천 목표
---
1. 임계구역 문제를 설명하고 경쟁 조건(Race Condition)을 설명한다.

## 기본적인 개념 정리
--
1. 경쟁 조건 (Race Condition)
	- 다음 두 조건을 만족하는 특정한 상황
		1. 동시에 여러 개의 프로세스가 동일한 자료를 접근하여 조작
		2. 접근이 발생한 특정 순서에 따라 실행 결과가 달라짐
2. 데이터 무결성 (Data Integrity)
	- 접근이 발생한 특정 순서에 따라 실행 결과가 달라지는 것은 문맥 교환이 명령(코드)의 논리적 최소 단위 내부에서 발생해서 데이터 무결성이 손상되었기 때문
	- (예) count++
		1. register1 = count (LOAD R1, #count)
		2. register1 = register1 + 1 (ADD R1, $1)
		3. count = register1 (STORE #count, R1)
	- 위에서 2와 3 사이에 문맥 교환이 발생하는 경우 문제가 발생
	- LOAD된 공유 데이터에 대해 처리하는 부분에서 멈춘 상태에서 다른 프로세스가 LOAD된 데이터를 변경하는 상황이 되고
	- 다시 이 프로세스가 문맥을 받으면 처리를 재개한 후에 해당 처리 결과를 다시 공유 데이터에 STORE하므로
	- 다른 프로세스가 데이터를 변경한 내용에 대해서는 반영이 되지 않고 해당 명령은 실행되지 않은 것과 마찬가지가 됨
3. 동기화 (Process Synchoronization)
	- 데이터 무결성을 지키는 이론적인 방법
	- 넓은 의미 : 동기화는 시스템을 동시에 작동시키기 위해 여러 사건들을 조화시키는 것
	- 여기에서 동기화란, 여러 프로세스가 공유 데이터에 대해 일관된 데이터를 바탕으로 명령을 수행할 수 있도록
	- 데이터를 일치시킨다는 의미
4. 임계 구역 (Critical Section)
	- 여러 프로세스에게 공유된 데이터에 대해 여러 프로세스가 이를 접근/수정하는 영역(코드 조각)
5. 임계 구역 문제 (Critical Section Problem)
	- 프로세스들이 공유 데이터와 관련된 수행을 동기화하기 위해 사용하는 상호간 규약을 설계하는 것

## 임계 구역 문제의 해결안, 세 가지 조건
---
1. 상호 배제 (Mutual Exclusion) ★ 반드시 만족해야 하는 조건
	- 프로세스가 자기의 임계 구역에서 실행된다면 다른 프로세스들은 그들 자신의 임계 구역에서 실행될 수 없음
2. 진행 (Progress)
	- 데드락 (Deadlock)을 피하기 위함
	- 자신의 임계 구역에서 실행되는 프로세스가 없고, 그들 자신의 임계구역으로 진입하고자 하는 프로세스들이 있다면
	- 그 프로세스들의 진입은 무한정 연기될 수 없음
3. 한정된 대기 (Bounded Waiting)
	- 기아 현상 (Starvation)을 피하기 위함
	- 프로세스가 자신의 임계 구역에 진입하려는 요청을 한 후부터 그 요청이 허용될 때까지
	- 다른 프로세스들이 그들 자신의 임계 구역이 진입하도록 허용되는 횟수에 한계가 있음

## 임계 구역 문제 해결책
---
1. 소프트웨어 기반 해결책
	1. 피터슨 해결책 (Peterson's Solution)
2. 하드웨어 기반 해결책
	1. 메모리 장벽 (Memory Barriers)
	2. 하드웨어 명령어 (Hardware Instructions)
	3. 원자적 변수 (Atomic Variables)

## 소프트웨어 기반 해결책 - 피터슨 해결책 (Peterson's Solution)
---
- 임계 구역 문제를 가장 완전하게 해결한 알고리즘
- 상호 배제, 진행, 한정된 대기 조건을 모두 만족함을 증명 가능
- 그러나 소프트웨어 명령어가 하드웨어 상에서는 여러 개로 쪼개어져 실행되므로 (예) LOAD, STORE
- 실제 실행해보면 임계 구역 문제가 해결되지 않은 결과 출력
```c
	#include <stdio.h>
	#include <pthread.h>

	#define true 1
	#define false 0

	int sum = 0;

	int turn;
	int flag[2];

	int main()
	{
		pthread_t tid1, tid2;
		pthread_create(&tid1, NULL, producer, NULL);
		pthread_create(&tid2, NULL, consumer, NULL);
		pthread_join(tid1, NULL);
		pthread_join(tid2, NULL);
		printf("sum = %d\n", sum);
	}

	void *producer(void *param)
	{
		int k;
		for (k = 0; k < 10000; k++) {
			/* entry section */
			flag[0] = true;
			turn = 1;
			while (flag[1] && turn == 1)
				;
			
			/* critical section */
			sum++;

			/* exit section */
			flag[0] = false;

			/* remainder section */
		}
		pthread_exit(0);
	}

	void *consumer(void *param)
	{
		int k;
		for (k = 0; k < 10000; k++) {
			/* entry section */
			flag[1] = true;
			turn = 0;
			while (flag[0] && turn == 0)
				;
			
			/* critical section */
			sum--;

			/* exit section */
			flag[1] = false;

			/* remainder section */
		}
		pthread_exit(0);
	}	
```

## 하드웨어 기반 해결책 - 원자적 변수 (Atomic Variables)
---
- LOAD와 STORE하는 부분에 대해 문맥 교환이 발생하지 않도록 통합한 하나의 하드웨어 명령어를 이용해서
- 기본적인 데이터 타입에 대해 쪼개어질 수 없는 연산이 정의된
- 하나의 변수로,
- 경쟁 조건 (Race Condition) 상황에서의 상호 배제를 확실하게 보장하게 위해 사용
```java
	package os.concepts;

	import java.util.concurrent.atomic.AtomicBoolean;

	public class Peterson2 {

		static int count = 0;

		static int turn = 0;
		static AtomicBoolean[] flag;    // static boolean[] flag = new boolean[2];
		static {
			flag = new AtomicBoolean[2];
			for (int i= 0; i < flag.length; i++) {
				flag[i] = new AtomicBoolean();
			}
		}

		public static void main(String[] args) throws Exception {
			Thread t1 = new Thread(new Producer());
			Thread t2 = new Thread(new Consumer());
			t1.start(); t2.start();
			t1.join(); t2.join();
			System.out.println(Peterson2.count);
		}

		static class Producer implements Runnable {
			@Override
			public void run() {
				for (int k = 0; k < 100000; k++) {
					/* entry section */
					flag[0].set(true);  // flag[0] = true;
					turn = 1;
					while (flag[1].get() && turn == 1)  // while (flag[1] && turn == 1)
						;

					/* critical section */
					count++;

					/* exit section */
					flag[0].set(false); // flag[0] = false;

					/* remainder section */
				}
			}
		}

		static class Consumer implements Runnable {
			@Override
			public void run() {
				for (int k = 0; k < 100000; k++) {
					/* entry section */
					flag[1].set(true);  // flag[1] = true;
					turn = 0;
					while (flag[0].get() && turn == 0)  // while (flag[0] && turn == 0)
						;

					/* critical section */
					count--;

					/* exit section */
					flag[1].set(false); // flag[1] = false;

					/* remainder section */
				}
			}
		}

	}
```


## 임계 구역 문제 조건 중 '상호 배제'만 해결하는 해결책
---
1. 뮤텍스
2. 세마포어
3. 모니터


## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html