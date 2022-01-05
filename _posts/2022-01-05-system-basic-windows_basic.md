---
title: 윈도우 기본 학습
author: Develiberta
date: 2022-01-05 21:00:00 +0900
categories: [Security, Windows]
tags: [Securiy, Windows]
---


## 학습 목표
---
1. 윈도우 인증 구성요소를 이해하고 설명할 수 있다.
2. 윈도우 보안 식별자를 이해하고 설명할 수 있다.
3. 윈도우 인증 구조를 이해하고 설명할 수 있다.
4. 패스워드 크래킹 방법을 설명할 수 있다.

## 윈도우 인증 구성요소
---
1. LSR (Local Security Authority)
	- NT 보안의 중심
	- 모든 계정의 로그인에 대한 검증
	- 시스템 자원(파일 등)에 대한 접근 권한 검사
	- 계정명과 SID 매칭
	- SRM이 생성한 감사 로그 기록
2. SAM (Security Account Manager)
	- 사용자/그룹계정 정보에 대한 데이터베이스 관리
	- 사용자 로그인 정보와 SAM 파일에 저장된 사용자 패스워드 정보 비교해 인증 여부 결정
	- SAM(C:\Windows\System32\config\SAM, 접근 통제 필요) 파일은 사용자/그룹계정 및 암호화된 패스워드 정보 저장
3. SRM (Service Reference Monitor)
	- 인증된 사용자에게 SID 부여
	- SID를 기반으로 파일이나 디렉터리에 대한 접근 허용 여부 결정, 감사 메시지 생성

## 윈도우 보안 식별자 (SID:Security Identifier)
---
1. 개요
	- 윈도우의 각 사용자나 그룹에 부여되는 고유한 식별번호 (UNIX/Linux의 UID 및 GID)
	- 사용자가 로그인을 수행하면 접근 토큰이 생성되며, 해당 토큰에 로그인한 사용자 및 그 사용자가 속한 모든 작업 그룹들에 관한 보안 식별자(SID) 정보 있음
	- 접근 토큰의 사본은 그사용자에 의해 시작된 모든 프로세스에 할당됨
	- 사용자 계정 및 패스워드 정보를 담고 있는 SAM 파일에 SID 정보가 저장되어 있음

2. SID 구조
```console
C:\Users\shlee>wmic useraccount list brief
AccountType  Caption                             Domain           FullName  Name                SID                     
512          DESKTOP-SFHBPG9\Administrator       DESKTOP-SFHBPG9            Administrator       S-1-5-21-3368797376-1781216822-3552830911-500
512          DESKTOP-SFHBPG9\DefaultAccount      DESKTOP-SFHBPG9            DefaultAccount      S-1-5-21-3368797376-1781216822-3552830911-503
512          DESKTOP-SFHBPG9\Guest               DESKTOP-SFHBPG9            Guest               S-1-5-21-3368797376-1781216822-3552830911-501
512          DESKTOP-SFHBPG9\shlee               DESKTOP-SFHBPG9            shlee               S-1-5-21-3368797376-1781216822-3552830911-1001
512          DESKTOP-SFHBPG9\WDAGUtilityAccount  DESKTOP-SFHBPG9            WDAGUtilityAccount  S-1-5-21-3368797376-1781216822-3552830911-504
```
	- S-1
	: 윈도우 시스템을 의미
	- 5-21
	: 시스템이 도메인 컨트롤러이거나 단독 시스템(stand-alone)임을 의미
	- 3368797376-1781216822-3552830911
	: 해당 시스템만의 고유한 식별자
	- 500, 501, 503, 504, 1001
	: 사용자 식별자(ID)로, 500은 관리자 식별자, 501은 게스트 식별자, 1000 이상은 일반 사용자 식별자

## 윈도우 인증 구조
---
1. 개요
	(1) 사용자가 윈도우에 인증 요청
	(2) 윈도우가 사용자에 Challenge 값(1회성 임의 문자열) 전송
	(3) 사용자가 윈도우에 Response 값(Challenge 갑과 비밀번호를 알고리즙으로 조합해서 생성한 값) 전송
	(4) 윈도우가 Response 값 확인 후 인증 성공 여부 전송
	
2. 인증 암호 알고리즘
	- LM 해시
	: 윈도우 2000, XP의 기본 알고리즘으로, 구조적으로 취약
	- NTLM 해시
	: LM 해시에 MD4 해시가 추가된 형태
	- NTLMv2 해시
	: 윈도우 비스타 이후 윈도우 시스템의 기본 인증 프로토콜
	
## 패스워드 크래킹
---
1. 사전 공격/사전 대입 공격(Dictionary Attack)
2. 무차별 공격/무작위 대입 공격(Brute Force Attack)
3. 혼합 공격(Hybrid Attack)
4. 레인보우 테이블(Rainbow Table)을 이용한 공격