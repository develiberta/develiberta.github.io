---
title: Security
author: Develiberta
date: 2023-03-14 18:30:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---


## 목적
---
1. 보안상의 문제점들(threats)과 공격에 대해 논의한다.
2. 암호화, 인증, 해싱의 근본 원리를 설명한다.
3. 연산에 있어서 암호 작성법의 사용을 검토한다.
4. 보안상의 공격에 대한 다양한 대응책을 논의한다.

## 실천 목표
---
1. 보안상의 문제점들(threats)과 공격에 대해 논의한다.
2. 암호화, 인증, 해싱의 근본 원리를 설명한다.
3. 연산에 있어서 암호 작성법의 사용을 검토한다.
4. 보안상의 공격에 대한 다양한 대응책을 논의한다.

## 용어
---
- 보안 _Security
	- 시스템과 데이터의 무결성이 보존될 것이라는 확신의 척도
- 위협 _Threat
	- 보안 위반에 대한 잠재적 가능성
- 공격 _Attack
	- 보안을 깨뜨리기 위한 시도

## 보안 문제 _The Security Problem
---
- 기밀성 침해 (breach of confidentiality)
- 무결성 침해 (breach of integrity)
- 가용성 침해 (breach of availability)
- 서비스 가로채기 (theft of service)
- 서비스 거부 (denial of service)

## 보안 레벨 _Four Levels of Security
---
- Physical
- Network
	- Sniffing
	- Spoofing
	- Denial of Service
	- Port Scanning
- Operating System
- Application
	- Malware (악성코드)
	- Code Injection (코드 주입)
	- Viruses and Worms (바이러스와 웜)

## 암호학 _Cryptography
---
- 개요
	- 기밀성 보장
		- 암호문은 정해진 사용자만 복호화 가능
	- 무결성 보장
		- 함께 보내는 메시지가 변조되었는지 여부 확인_verification 가능
		- 예) MAC(Message-Authentication Code), Digital Signature Algorithm, BitCoin, BlockChain
- 암호화와 복호화
	- 암호화 _Encryption \\
		$$E:K \to (M \to C)$$
	- 복호화 _Decryption \\
		$$D:K \to (C \to M)$$
- 암호 알고리즘 분류
	- 대칭 암호화 알고리즘
		- 암호화와 복호화에 동일한 키 사용
		- 키 교환 문제
		- 예) DES, AES가 포함됨
	- 비대칭 암호화 알고리즘
		- 암호화와 복호화에 다른 키 사용 (public/private)
		- 예) RSA가 포함됨

## 참고
---
1. 운영체제 공룡책 강의 | 주니온 | 인프런
	https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard
2. 운영체제 제 10판 | Abraham Silberschatz, Peter Baer Galvin, Greg Gagne 저/박민규 역 | 퍼스트북 | 2020년 02월 28일
3. 운영체제 제 10판 솔루션
	https://codex.cs.yale.edu/avi/os-book/OS10/practice-exercises/index-solu.html