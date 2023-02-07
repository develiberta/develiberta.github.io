---
title: Deadlocks
author: Develiberta
date: 2023-01-15 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. Mutex 락을 사용할 때 어떻게 교착 상태가 발생할 수 있는지 이해한다.
2. 교착 상태를 특징 짓는 4가지 필수 조건을 정의한다.
3. 자원 할당 그래프에서 교착 상태 상황을 식별한다.
4. 교착 상태 예방을 위한 4가지 방법을 평가한다.
5. 교착 상태 회피를 위해 은행의 알고리즘을 적용한다.
6. 교착 상태 감지 알고리즘을 적용한다.
7. 교착 상태에서 복구하기 위한 접근법을 평가한다.

## 실천 목표
---
1. Mutex 락을 사용할 때 어떻게 교착 상태가 발생할 수 있는지 예제를 통해 확인한다.
2. 교착 상태를 특징 짓는 4가지 필수 조건과 그 이유를 이해한다.
3. 자원 할당 그래프에서 교착 상태가 나타날 수 있는 상황을 설명한다.
4. 교착 상태 예방을 위한 4가지 방법을 이해한다.

## 교착 상태
---
1. 어떤 프로세스의 집합 내의
2. 모든 프로세스가 waiting인 상황
3. 각 프로세스는 해당 집합의 다른 프로세스에 의해서만 발생할 수 있는 이벤트를 waiting
4. 예를 들어, 프로세스가 요청한 자원을 할당받기 위해 기다리고 있는데, 이 자원은 다른 waiting 중인 프로세스가 점유 중인 경우

## 교착 상태의 발생
---
```c
pthread_mutex_t first_mutex;
pthread_mutex_t second_mutex;

pthread_mutex_init(&first_mutex, NULL);
pthread_mutex_init(&second_mutex, NULL);

/* thread_one runs in this function */
void *do_work_one(void *param)
{
	pthread_mutex_lock(&first_mutex);
	pthread_mutex_lock(&second_mutex);
	/**
		* Do some work
		*/
	pthread_mutex_unlock(&second_mutex);
	pthread_mutex_unlock(&first_mutex);

	pthread_exit(0);
}

/* thread_two runs in this function */
void *do_work_two(void *param)
{
	pthread_mutex_lock(&second_mutex);
	pthread_mutex_lock(&first_mutex);
	/**
		* Do some work
		*/
	pthread_mutex_unlock(&first_mutex);
	pthread_mutex_unlock(&second_mutex);

	pthread_exit(0);
}
```

## 교착 상태가 발생하기 위한 4가지 필수 조건
---
1. Mutual Exclusion (상호 배제)
	- 적어도 하나의 자원이 공유되지 않는 상태
2. Hold and Wait (점유 대기)
	- 적어도 하나의 자원을 점유한 프로세스가 또다른 자원(또다른 프로세스가 점유)을 점유하기 위해 대기
3. No preemption (선점 불가)
	- 자원들은 선점 불가
4. Circular Wait (순환 대기)
	- 대기 중인 프로세스의 집합에 순환적인 대기 의존 그래프 존재

## Resource-Allocation Graph (자원할당 그래프, RAG)
---
1. 방향 그래프 G = (V, E)
2. 꼭짓점
	- T = {T1, T2, ..., Tn}
	- R = {R1, R2, ..., Rm}
3. 변
	- 변 Ti -> Rj : requested edge
	- 변 Rj -> Ti : assignment edge
4. 예시
	- ![2023-01-15-os-deadlocks-01](/assets/img/illustrations/2023-01-15-os-deadlocks-01.jpg)
	- ![2023-01-15-os-deadlocks-02](/assets/img/illustrations/2023-01-15-os-deadlocks-02.jpg)
	- ![2023-01-15-os-deadlocks-03](/assets/img/illustrations/2023-01-15-os-deadlocks-03.jpg)
	- ![2023-01-15-os-deadlocks-04](/assets/img/illustrations/2023-01-15-os-deadlocks-04.jpg)

## 교착 상태를 다루는 3가지 방법
---
1. 무시
2. 방지 또는 회피
3. 허용 후 추적과 회복

## 교착 상태 방지 Deadlock Prevention
---
교착 상태가 발생하기 위한 4가지 필수 조건 중 적어도 1개를 제거 (요청 방법에 제한을 두는 방식)
1. Mutual Exclusion (상호 배제)
	- 어떤 자원들은 본질적으로 공유할 수 없으므로 이 조건을 제거하는 것은 본질적으로 불가능
2. Hold and Wait (점유 대기)
	- 프로세스가 어떤 자원을 요청할 때, 어떤 자원도 가지고 있지 않을 때에만 요청 가능한 것은 실용적이지 않음
3. No preemption (선점 불가)
	- 필요 시 자원을 선점할 수 있도록 하는 방법은 대부분의 응용 프로그램에 적용되기 어려움
4. Circular Wait (순환 대기)
	- 자원 유형에 따라 순서를 매겨서
	- 프로세스가 이미 선점한 자원보다 더 높은 순서의 자원만 요청할 수 있도록 함
	- 위의 3가지를 제거하는 것보다 더 현실성있는 방법으로, 이렇게 하면 2개의 프로세스에 대해 교착 상태를 방지할 수 있음
	- 그러나 lock을 동적으로 획득할 수 있다면 교착 상태를 방지하기 어려움
		```c
		void transaction(Account from, Account to, double amount)
		{
			mutex lock1, lock2;
			lock1 = get_lock(from);
			lock2 = get_lock(to);

			acquire(lock1);
			acquire(lock2);

			withdraw(from, amount);
			deposit(to, amount);

			release(lock2);
			release(lock1);
		}

		transaction(checking_account, savings_account, 25.0);
		transaction(savings_account, checking_account, 50.0);
		```

## 교착 상태 회피 Deadlock Avoidance
---
- 미래에 일어날 가능성이 있는 교착 상태를 회피하기 위해 프로세스의의 자원 요청을 수락할지 결정 (요청한 프로세스에 대해 기다리거나 수락되거나가 결정됨)
- 이를 위해서는 다음과 같은 priori information(선행 정보) 필요
	- (필요) 프로세스가 총 실행되는 시간에 (최대로) 필요로 하는 각 자원의 개수 A
	- (필요) 프로세스에 현재 할당된 각 자원의 개수 B
	- (필요) 프로세스가 남은 시간동안에 필요로 하는 각 자원의 개수 C=A-B (계산 가능)
	- (필요) 현재 사용 가능한 각 자원의 개수
- 프로세스가 자원 요청 시 Claim Edge를 Request Edge로 바꾼다고 가정할 때, 안전 상태(Safe State)인지 여부를 판단해서 프로세스에 요청을 수락(Grant) 할지 결정
	- 안전 상태(Safe State)란 프로세스가 교착 상태 없이 실행될 수 있는 Safe Sequence가 존재한다는 의미
	- 안전 상태(Safe State)의 여집합인 Unsafe State는 Deadlock 상태를 부분집합으로 포함
- 인스턴스의 개수에 따라 다음과 같이 회피 가능
	- Single Instance
		- Claim Edge를 Request Edge로 바꾸는 경우 (Claim Edge를 포함해서) Cycle이 생기는 경우 회피
	- Multiple Instances
		- Banker's Algorithm

## 교착 상태 탐지 Deadlock Detection
---
- 교착 상태가 일어날 가능성이 있는 환경에서, 교착 상태를 탐지하고 복구
- 이를 위해서는 다음과 같은 priori information(선행 정보) 필요
	- (불필요) 프로세스가 총 실행되는 시간에 (최대로) 필요로 하는 각 자원의 개수 A
	- (필요) 프로세스에 현재 할당된 각 자원의 개수 B
	- (필요) 프로세스가 (실제) 요청한 각 자원의 개수 C
	- (필요) 현재 사용 가능한 각 자원의 개수
- 프로세스가 자원 요청 시 자원을 할당한 후에 데드락이 발생했는지 결정
- 인스턴스의 개수에 따라 다음과 같이 탐지 가능
	- Single Instance
		- Cycle이 생기는 경우 탐지
	- Multiple Instances
		- Banker's Algorithm

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html