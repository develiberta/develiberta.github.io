---
title: OS Synchronization Examples
author: Develiberta
date: 2022-12-25 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 동기화 예제들을 이해한다.

## 실천 목표
---
1. 유한 버퍼, readers-writer 및 식사하는 철학자 동기화 문제에 관해 설명한다.

## 동시성 제어 문제의 분류
---
1. 유한 버퍼 문제 The Bounded Buffer Problem
2. Readers-Writers 문제 The Readers-Writers Problem
3. 식사하는 철학자들 문제 The Dining Philosophers Problem

## 유한 버퍼 문제 (The Bounded Buffer Problem)
---
1. 생산자-소비자 문제 중 다음의 특정 조건을 만족
	- n개의 버퍼로 구성된 풀(pool)이 있으며 각 버퍼는 한 항목(item)을 저장할 수 있다고 가정
2. 다음과 같은 자료구조를 가짐
	```c
		int n;
		semaphore mutex = 1;
		semaphore empty = n;
		semaphore full	= 0;
	```
	- mutex 이진 세마포는 버퍼 풀에 접근하기 위한 상호 배제 기능을 제공 (1로 초기화)
	- empty는 비퍼 있는 버퍼의 수를, full은 꽉 찬 버퍼의 수를 기록 (empty는 n 값으로 초기화, full은 0으로 초기화)
3. 생산자 프로세스
	```c
		while (true) {
			/* ... */
			/* produce an item in next_produced */
			/* ... */
			wait(empty);	// consumer가 buffer를 다 비울 때까지 기다림
			wait(mutex);	// 여기까지 수행했다면 buffer가 비어있고 이 프로세스 혼자만 들어온 게 보장됨
			/* ... */
			/* add next_produced to the buffer */
			/* ... */
			signal(mutex);	// 끝난 후에 반납
			signal(full);	// buffer가 다 찼음을 consumer에 알림
		}
	```
4. 소비자 프로세스
	```c
		while (true) {
			wait(full);		// signal(full)을 받아야만 깨어남
			wait(mutex);	// 여기까지 수행했다면 buffer가 가득 차 있고 이 프로세스 혼자만 들어온 게 보장됨
			/* ... */
			/* remove an item from buffer to next_consumed */
			/* ... */
			signal(mutex);	// 끝난 후에 반납
			signal(empty);	// buffer가 비어있음을 producer에 알림
		}
	```
5. 예제 소스
	1. PThread Solution
		```c
			#include <stdio.h>
			#include <stdlib.h>
			#include <unistd.h>
			#include <pthread.h>
			#include <semaphore.h>

			#define true 1
			#define BUFFER_SIZE 5

			int buffer[BUFFER_SIZE];

			pthread_mutex_t mutex;
			sem_t empty, full;

			int in = 0, out = 0;

			int main(int argc, char *argv[]) {
				int i, numOfProducers = 1, numOfConsumers = 1;
				pthread_t tid;

				pthread_mutex_init(&mutex, NULL);
				sem_init(&empty, 0, BUFFER_SIZE);
				sem_init(&full, 0, 0);
				srand(time(0));

				// Create the Producers
				for (int i = 0; i < numOfProducers; i++)
					pthread_create(&tid, NULL, producer, NULL);
				
				// Create the Consumers
				for (int i = 0; i < numOfConsumers; i++)
					pthread_create(&tid, NULL, consumer, NULL);

				sleep(10);
				return 0;
			}

			void *producer(void *param) {
				int item;
				while (true) {
					usleep((1 + rand() % 5) * 100000);
					item = 1000 + rand() % 1000;
					insert_item(item);	// critical section in fuction
				}
			}

			void *consumer(void *param) {
				int item;
				while (true) {
					usleep((1 + rand() % 5) * 100000);
					remove_item(&item);	// critical section in function
				}
			}

			void insert_item(int item) {
				sem_wait(&empty);
				pthread_mutex_lock(&mutex);

				buffer[in] = item;
				in = (in + 1) % BUFFER_SIZE;
				printf("Producer: inserted $%d\n", item);

				pthread_mutex_unlock(&mutex);
				sem_post(&full);
			}

			void remove_item(int *item) {
				sem_wait(&full);
				pthread_mutex_lock(&mutex);

				*item = buffer[out];
				out = (out + 1) % BUFFER_SIZE;
				printf("Consumer: removed $%d\n", *item);

				pthread_mutex_unlock(&mutex);
				sem_post(&empty);
			}
		```
	2. Java Solution
		```c
			public class BoundedBuffer {
				public static void main(String[] args) {
					CashBox cashBox = new CashBox(1);
					Thread[] producers = new Thread[1];
					Thread[] consumers = new Thread[1];

					// Create Threads of Producers
					for (int i = 0; i < producers.length; i++) {
						producers[i] = new Thread(new ProdRunner(cashBox));
						producers[i].start();
					}

					// Create Threads of Consumers
					for (int i = 0; i < consumers.length; i++) {
						consumers[i] = new Thread(new ProdRunner(cashBox));
						consumers[i].start();
					}
				}
			}

			class ProdRunner implements Runnable {
				CashBox cashBox;
				
				public ProdRunner(CashBox cashBox) {
					this.cashBox = cashBox;
				}

				@Override
				public void run() {
					try {
						while (true) {
							Thread.sleep((long) (Math.random() * 500));
							int money = ((int) (1 + Math.ramdom() * 9)) * 10000;
							cashBox.give(money);
						}
					} catch (InterruptedException e) {}
				}
			}

			class ConsRunner implements Runnable {
				CashBox cashBox;

				public ConsRunner(CashBox cashBox) {
					this.cashBox = cashBox;
				}

				@Override
				public void run() {
					try {
						while (true) {
							Thread.sleep((long) (Math.random() * 500));
							int money = cashBox.take();
						}
					} catch (InterruptedException e) {}
				}
			}

			class CashBox {

				private int[] buffer;

				private int count, in, out;

				public CashBox(int bufferSize) {
					buffer = new int[bufferSize];
					count = in = out = 0;
				}

				synchronized public void give(int money) throws InterruptedException {
					while (count == buffer.length) {
						try {
							wait();
						} catch (InterruptedException e) {}
					}

					// critical section
					buffer[in] = money;
					in = (in + 1) % buffer.length;
					count++;
					System.out.printf("여기있어: %d원\n", money);

					notify();
				}

				synchronized public int take() throws InterruptedException {
					while (count == 0) {
						try {
							wait();
						} catch (InterruptedException e) {}
					}

					// critical section
					int money = buffer[out];
					out = (out + 1) % buffer.length;
					count--;
					System.out.printf("고마워요: %d원\n", money);

					notify();
					return money;
				}
			}
		```

## Readers-Writers 문제 (The Readers-Writers Problem)
---
1. 생산자-소비자 문제 중 다음의 특정 조건을 만족
	- 프로세스 분류
		- 공유 데이터의 내용을 읽기만 하는 프로세스 reader
		- 공유 데이터의 내용을 (읽고) 쓰는 프로세스 writer
	- 두 reader가 동시에 공유 데이터에 접근하면 문제가 발생하지 않음
	- 한 writer가 공유 데이터에 접근할 때 다른 reader나 writer가 공유 데이터에 접근하면 혼란 야기
	- 이 문제가 발생하지 않도록 보장하기 위해 writer가 쓰기 작업을 하는 동안 공유 데이터에 대해 배타적 접근 권한을 가지게 할 필요성
2. Readers-Writers 문제의 변형
	1. 첫 번째 Readers-Writers 문제
		- 단순히 Writer가 기다리고 있다는 이유만으로 다른 Reader들이 끝날 때까지 기다리는 Reader가 있어서는 안됨
		- Reader과 Writer에게 공평한 기회 제공 
	2. 두 번째 Readers-Writers 문제
		- Writer가 객체에 접근하려고 기다리고 있다면 새로운 Reader들은 읽기를 시작하지 못함
		- Reader보다 Wrtier에게 우선한 기회 제공

## 첫 번째 Readers-Writers 문제
---
1. Readers-Writers 문제의 변형
	- 단순히 Writer가 기다리고 있다는 이유만으로 다른 Reader들이 끝날 때까지 기다리는 Reader가 있어서는 안됨
	- Reader과 Writer에게 공평한 기회 제공
	- 아래 예시에서는 1개의 Writer와 n개의 Reader가 있다고 가정
2. 다음과 같은 자료구조를 가짐
	```c
		semaphore rw_mutex = 1;
		semaphore mutex = 1;
		int read_count = 0;
	```
	- rw_mutex 세마포는 Reader와 Writer가 모두 공유 (1로 초기화), 주로 Writer를 위함
	- mutex 세마포는 read_count를 갱신할 때 상호 배제를 보장하기 위해 사용 (1로 초기화)
	- read_count는 현재 객체를 읽고 있는 Reader 프로세스들의 갯수
3. Writer 프로세스
	```c
		while (true) {
			wait(rw_mutex);
			/* ... */
			/* writing is performed */
			/* ... */
			signal(rw_mutex);
		}
	```
4. Reader 프로세스
	```c
		while (true) {
			wait(mutex);
			read_count++;
			if (read_count == 1)
				wait(rw_mutex);
			signal(mutex);
			/* ... */
			/* reading is performed */
			/* ... */
			wait(mutex);
			read_count--;
			if (read_count == 0)
				signal(rw_mutex);
			signal(mutex);
		}
	```
5. 예제 소스
	1. Java Solution
		```java
			class SharedDB {
				private int readerCount = 0; // 읽고 있는 Reader 프로세스 갯수
				private boolen isWriting = false;

				public void read() {
					sharedDB.acquireReadLock();
					sharedDB.read();
					sharedDB.releaseReadLock();
				}

				public void write() {
					sharedDB.acquireWriteLock();
					sharedDB.write();
					sharedDB.releaseWriteLock();
				}
			}

			synchronized public void acquireReadLock() {
				while (isWriting == true) {
					try {
						wait();
					} catch (InterruptedException e) {}
				}
				readerCount++;
			}

			synchronized public void releaseReadLock() {
				readerCount--;
				if (readerCount == 0)
					notify();
			}

			synchronized public void acquireWriteLock() {
				while (readerCount > 0 || isWriting == true) {
					try {
						wait();
					} catch (InterrputedException e) {}
				}
				isWriting = true;
			}

			synchronized public void releaseWriteLock() {
				isWriting = true;
				notifyAll();	// 공평하게 Ready Queue에서 경쟁하도록 하기 위함
			}
		```

## 식사하는 철학자들 문제 (The Dining Philosophers Problem)
---
1. 고전적인 동기화 문제
	- 많은 부류의 벙행제어 문제의 한 예
	- 교착 상태와 기아를 발생시키지 않고 여러 스레드에게 여러 자원을 할당해야 할 필요를 단순하게 표현한 문제
2. 문제 내용
	![2022-12-25-os-synchronization-examples-00](/assets/img/illustrations/2022-12-25-os-synchronization-examples-00.png)
	- 생각하고 먹으면서 시간을 보내는 5명의 철학자
	- 철학자들은 원형의 테이블을 공유
	- 테이블 중앙에는 한 사발의 밥
	- 테이블에는 다섯 개의 젓가락
	- 밥을 먹기 위해서는 두 개의 젓가락 필요
	- 철학자는 생각할 때 상호 작용 없음
3. 해결안
	1. 세마포 해결안 (Semaphore Solution)
		- 각 젓가락을 하나의 세마포로 표현
		```c
			while (true) {
				wait(chopstick[i]);
				wait(chopstick[(i+1) % 5]);
				/* ... */
				/* eat for a while */
				/* ... */
				signal(chopstick[i]);
				signal(chopstick[(i+1) % 5]);
				/* ... */
				/* think for a while */
				/* ... */
			}
		```
		- 교착 상태를 야기할 가능성이 있기 때문에 채택하지 않음 (예) 5명의 철학자 모두가 동시에 배가 고파서 자신의 왼쪽 젓가락을 잡는 경우
		- 교착 상태 문제에 대한 여러 가지 해결책
			- 최대 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 함
			- 한 철학자가 젓가락 두 개를 모두 집을 수 있을 때만 젓가락을 집도록 허용
			- 비대칭 해결안 (홀수 번호의 철학자는 먼저 왼쪽 젓가락을 집고, 짝수 번호의 철학자는 먼저 오른쪽 젓가락을 집음)
	2. 모니터 해결안 (Monitor Solution)
		- 한 철학자가 젓가락 두 개를 모두 집을 수 있을 때만 젓가락을 집도록 허용한다는 제한을 강제
		- 철학자가 처할 수 있는 세 가지 상태를 구분하기 위한 자료구조 도입
		```c
			enum {THINKING, HUNGRY, EATING} state[5];
		```
		- 철학자 i가 배가 고프더라도 젓가락 두 개를 집을 수 없을 때에는 젓가락 집는 것을 미룰 수 있도록 함
		```c
			condition self[5];
		```
	3. Pthread를 이용한 모니터 해결안 (Pthread solution to the Dining-Philosophers Problem)
	```c
		#include <stdio.h>
		#include <stdlib.h>
		#include <unistd.h>
		#include <pthread.h>

		#define true 1
		#define NUM_PHILS 5

		enum {THINKING, HUNGRY, EATING} state[NUM_PHILS];

		pthread_mutex_t mutex_lock;
		pthread_cond_t cond_vars[NUM_PHILS];

		void init() {
			int i;
			for (i = 0; i < NUM_PHILS; i++) {
				state[i] = THINKING;
				pthread_cond_init(&cond_vars[i], NULL);
			}
			pthread_mutex_init(&mutex_lock, NULL);
			srand(time(0));
		}

		int leftOf(int i) {
			return (i + NUM_PHILS - 1) % NUM_PHILS;
		}

		int rightOf(int i) {
			return (i + 1) % NUM_PHILS;
		}

		void think(int id) {
			printf("%d: Now, I'm thinking...\n", id);
			usleep((1 + rand() % 50) * 10000);
		}

		void eat(int id) {
			printf("%d: Now, I'm eating...\n", id);
			usleep((1 + rand() % 50) * 10000);
		}

		void test(int i) {
			// If I'm hungry and my neighbors are not eating,
			// then let me eat.
			if (state[i] == HUNGRY &&
				state[leftOf(i)] != EATING && state[rightOf(i)] != EATING) {
					state[i] = EATING;
					pthread_cond_signal(&cond_vars[i]);
			}
		}

		void pickup(int i) {
			pthread_mutex_lock(&mutex_lock);

			state[i] = HUNGRY;
			test(i);
			while (state[i] != EATING) {
				pthread_cond_wait(&cond_vars[i], &mutex_lock);
			}

			pthread_mutex_unlock(&mutex_lock);
		}

		void putdown(int i) {
			pthread_mutex_lock(&mutex_lock);

			state[i] = THINKING;
			test(leftOf(i));
			test(rightOf(i));

			pthread_mutex_unlock(&mutex_lock);
		}

		void *philosopher(void *param) {
			int id = *((int *)param);
			while (true) {
				think(id);
				pickup(id);
				eat(id);
				putdown(id);
			}
		}

		int main() {
			int i;
			pthread_t tid;
			init();
			for (i = 0; i < NUM_PHILS; i++)
				pthread_create(&tid, NULL, philosopher, (void *)&i);
			for (i = 0; i < NUM_PHILS; i++)
				pthread_join(tid, NULL);
			return 0;
		}
	```
	4. Java를 이용한 모니터 해결안 (Java solution to the Dining-Philosophers Problem)
	```java
		package os.concepts;

		import java.util.concurrent.locks.Condition;
		import java.util.concurrent.locks.Lock;
		import java.util.concurrent.locks.ReentrantLock;

		enum State {
			THINKING, HUNGRY, EATING
		}

		public class DiningPhilosophers {

			public static void main(String[] args) throws Exception {
				int numOfPhils = 5;
				Philosopher[] philosophers = new Philosopher[numOfPhils];
				DiningPhilosopherMonitor monitor = new DiningPhilosopherMonitor(numOfPhils);
				for (int i = 0; i < philosophers.length; i++)
					new Thread(new Philosopher(i, monitor)).start();
			}
		}

		class Philosopher implements Runnable {

			private int id;
			private DiningPhilosopherMonitor monitor;

			public Philosopher(int id, DiningPhilosopherMonitor monitor) {
				this.id = id;
				this.monitor = monitor;
			}

			@Override
			public void run() {
				while (true) {
					think();
					monitor.pickup(id);
					eat();
					monitor.putdown(id);
				}
			}

			private void think() {
				try {
					System.out.println(id + ": Now I'm thinking.");
					Thread.sleep((long)(Math.random()*500));
				} catch (InterruptedException e) { }
			}

			private void eat() {
				try {
					System.out.println(id + ": Now I'm eating.");
					Thread.sleep((long)(Math.random()*50));
				} catch (InterruptedException e) { }
			}
		}

		class DiningPhilosopherMonitor {

			private int numOfPhils;
			private State[] state;
			private Condition[] self;
			private Lock lock;

			public DiningPhilosopherMonitor(int num) {
				numOfPhils = num;
				state = new State[num];
				self = new Condition[num];
				lock = new ReentrantLock();
				for (int i = 0; i < num ; i++) {
					state[i] = State.THINKING;
					self[i] = lock.newCondition();
				}
			}

			private int leftOf(int i) {
				return (i + numOfPhils - 1) % numOfPhils;
			}

			private int rightOf(int i) {
				return (i + 1) % numOfPhils;
			}

			private void test(int i) {
				if (state[i] == State.HUNGRY &&
						state[leftOf(i)] != State.EATING &&
						state[rightOf(i)] != State.EATING) {
					state[i] = State.EATING;
					self[i].signal();
				}
			}

			public void pickup(int id) {
				lock.lock();
				try {
					state[id] = State.HUNGRY;
					test(id);
					if (state[id] != State.EATING)
						self[id].await();
				} catch (InterruptedException e) {
				} finally {
					lock.unlock();
				}
			}

			public void putdown(int id) {
				lock.lock();
				try {
					state[id] = State.THINKING;
					test(leftOf(id));   // left neighbor
					test(rightOf(id));  // right neighbor
				} finally {
					lock.unlock();
				}
			}
		}
	```

## 대체 방안들
	1. 트랜잭션 메모리 Transactional Memory
	2. OpenMP
	3. Functional Programming Language

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html