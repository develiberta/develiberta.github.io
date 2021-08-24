---
title: 정렬 알고리즘 (Sorting Algorithm)
author: Develiberta
date: 2021-08-24 21:25:00 +0900
categories: [Logic, Algorithm]
tags: [Sorting, Algorithm]
---


## 학습 목표
---
1. 정렬 알고리즘의 분류를 이해한다.
2. 버블 정렬, 선택 정렬, 삽입 정렬, 셸 정렬, 퀵 정렬, 힙 정렬, 합병 정렬을 이해한다.
3. 문제 상황에 따라 어떤 정렬 알고리즘이 적합한지 설명하고, 근거를 들어 설명할 수 있다.

## 정렬 알고리즘의 분류 - 실행 방법에 따른 분류
---
- 비교식 정렬(comparative sort)
: 한 번에 두 개씩 비교해서 교환
: 예) 버블 정렬(bubble sort), 선택 정렬(selection sort), 삽입 정렬(insertion sort)


- 분산식 정렬(distribute sort)
: 전체를 여러 개의 부분집합으로 분해해서 각 부분집합을 정렬 후 전체를 정렬
: 예) 셸 정렬(shell sort), 퀵 정렬(quick sort), 힙 정렬(heap sort), 합병 정렬(merge sort)


## 정렬 알고리즘
---
- 비교식 정렬(comparative sort)
: 한 번에 두 개씩 비교해서 교환

	1. 버블 정렬(bubble sort)
	: 첫 번째 원소와 두 번째 원소, 두 번째 원소와 세 번째 원소, ..., (n-1)번째 원소와 n번째 원소까지 인접한 두 원소를 비교해서 교환한다.
	: 위와 같이 한 사이클을 돌면 가장 큰 원소가 가장 오른쪽에 위치하게 된다.
	: 그러므로 교환할 항의 왼쪽 항을 기준으로, 첫 번째 사이클은 1부터 (n-1)까지, 두번째 사이클은 1부터 (n-2)까지, (n-1)번째 사이클은 1부터 1까지 비교하면 된다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	2. 선택 정렬(selection sort)
	: 첫 번째 원소를 기준으로, 두 번째 원소부터 n번째 원소까지 각각 비교해서 교환한다.
	: 위와 같이 한 사이클을 돌면 가장 작은 원소가 가장 왼쪽에 위치하게 된다.
	: 그러므로 교환할 항의 왼쪽 항을 기준으로, 첫 번째 사이클은 첫 번째 원소를 기준으로, 두 번째 사이클은 두 번째 원소를 기준으로, (n-1)번째 사이클은 (n-1)번째 원소를 기준으로 해서, 나머지 오른쪽 원소들과 비교하면 된다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	3. 삽입 정렬(insertion sort)
	: 두 번째 원소를 첫 번째 원소와 비교해서 교환한다.
	: 위와 같이 한 사이클을 돌면 첫 번째 원소와 두 번째 원소가 정렬된다.
	: 그러므로 교환할 항의 오른쪽 항을 기준으로, 두 번째 사이클은 세 번째 원소를 첫 번째부터 두 번째 원소와 비교하고, (n-1)번째 사이클은 n번째 원소를 첫 번째부터 (n-1)번째 원소와 비교하면 된다.
	: 이때 삽입할 위치를 찾았다면 그 오른쪽에 있는 항들은 한 칸씩 오른쪽 옆으로 이동하게 된다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	
- 분산식 정렬(distribute sort)
: 전체를 여러 개의 부분집합으로 분해해서 각 부분집합을 정렬 후 전체를 정렬

	1. 셸 정렬(shell sort)
	: 각 사이클 별로 정렬할 부분집합을 달리해서 각 사이클마다 삽입 정렬 방법으로 진행한다. (삽입 정렬의 보완)
	: 부분집합을 만들 때에는, 정렬해야 할 리스트의 각 k번째 요소를 추출해서 부분 리스트를 만든다. 이때 k를 "간격(Gap)"이라고 한다.
	: (각 사이클마다 k를 절반으로 줄이되 k를 홀수로 하기 위해 짝수가 나오면 +1을 해서 홀수로 만든다. k의 초기값은 (정렬할 값의 수)/2로 한다.)
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	2. 퀵 정렬(quick sort)
	: 하나의 피벗(pivot)을 기준으로 피벗의 왼쪽에는 피벗보다 값이 작은 원소들이 오고, 오른쪽에는 피벗보다 값이 큰 원소들이 오도록 한다.
	: 피벗의 왼쪽을 하나의 부분집합으로, 오른쪽을 하나의 부분집합으로 해서 반복한다.
	: 구체적으로, 리스트에서 하나의 원소를 피벗으로 선택한 후, 다음을 진행한다.
	: 1) 왼쪽의 인덱스는 오른쪽으로 이동하며 피벗보다 큰 값을 찾고 오른쪽의 인덱스는 왼쪽으로 이동하며 피벗보다 작은 값을 찾는다.
	: 2) 위에서 둘 다의 값을 찾은 경우 두 값을 교환한다.
	: 3) 왼쪽 인덱스와 오른쪽 인덱스가 교차할 때까지 이를 반복한다.
	: 4) 교차 후에는 오른쪽에서 시작한 인덱스의 원소를 피벗과 교환한다.
	: 5) 피벗을 제외한 양 쪽의 리스트에 대해 위와 같은 과정을 반복한다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	3. 힙 정렬(heap sort) - 완전 이진 트리의 일종
	: 힙에 새로운 원소가 들어오면, 새로운 노드를 힙의 마지막 노드에 이어서 삽입한다.
	: 새로운 노드를 부모들과 교환해서 힙의 성질을 만족시킨다.
	: 부모 노드부터 추출해서 배열하며, 부모의 빈 자리는 가장 마지막 노드가 채운다.
	: 부모 자리에 온 노드와 자식 노드들을 비교해서 힙의 성질을 만족시킨다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```
	
	4. 합병 정렬(merge sort)
	: 리스트를 반으로 나누어서 리스트의 크기가 1이 될 때까지 반복한다.
	: 이웃한 크기 1의 리스트끼리 비교해서 정렬된 2개의 쌍들을 얻는다.
	: 이웃한 크기 2의 리스트끼리 비교해서 정렬된 4개의 쌍들을 얻는다.
	: 위의 과정을 반복한다.
	```java
	private void writeObject(java.io.ObjectOutputStream s)
		throws java.io.IOException {
		// Write out any hidden serialization magic
		s.defaultWriteObject();
		for (E e: map.keySet()) s.writeObject(e);
	}
	```