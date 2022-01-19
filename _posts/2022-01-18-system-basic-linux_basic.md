---
title: 유닉스/리눅스 기본 학습
author: Develiberta
date: 2022-01-18 21:20:00 +0900
categories: [Security, Linux]
tags: [Securiy, Linux]
---


## 학습 목표
---
1. UNIX/Linux 로그인 과정을 이해하고 설명할 수 있다.
2. UNIX/Linux 계정 정보 파일을 이해하고 설명할 수 있다.
3. UNIX/Linux 그룹 정보 파일을 이해하고 설명할 수 있다.
4. UNIX/Linux 입출력 재지정을 이해하고 설명할 수 있다.
5. UNIX/Linux 파이프를 이해하고 설명할 수 있다.

## UNIX/Linux 로그인 과정
---
1. 사용자 계정과 패스워드 입력
2. 로그인 프로그램은 입력한 정보와 /etc/passwd 파일 비교
3. 로그인 프로그램은 쉘 변수(HOME, SHELL, USER 또는 LOGNAME)들을 근거로 초기 환경 설정
4. 로그인 프로그램은 로그인 쉘(/etc/passwd의 7번째 필드에 정의) 실행 후 사용자 입력을 대기

## UNIX/Linux 계정 정보
---
- /etc/passwd
	1. 구분자 ':'를 이용해서 7개의 필드로 구분됨
	2. ${1_user_account}:${2_user_password}:${3_user_id}:${4_group_id}:${5_comment}:${6_home_directory}:${7_login_shell}
	3. 해킹 시 주로 3, 4번을 0으로 변경 : 시스템은 UID, GID 기준으로 권한을 부여하는데, 이것이 0인 경우 root가 됨
	4. 해킹 시 7번을 변경하는 경우도 있음 : 로그인 시 악성 쉘이 실행되도록 함
	5. 사용자를 확인하려면 id 명령어 사용 : 명령을 실행한 사용자 또는 특정 사용자의 UID, GID 확인 가능
	6. 패스워드를 변경하려면 passwd 명령어 사용 : 명령을 실행한 사용자 또는 (root인 경우) 특정 사용자의 비밀번호 변경 가능
	
## UNIX/Linux 그룹 정보
---
- /etc/group
	1. 구분자 ':'를 이용해서 4개의 필드로 구분됨
	2. ${1_group_name}:${2_unvariable}:${3_group_id}:${4_user_account_entry_in_group}
	3. UNIX/Linux 시스템의 계정은 하나의 기본 그룹과 복수의 보조 그룹에 소속될 수 있음
	4. 기본 그룹은 자원(파일 등) 생성 시 해당 자원의 소유 그룹을 지정하는 데 사용되며, 보조 그룹은 권한 설정이 필요한 경우에 사용됨
	
## UNIX/Linux 입출력 재지정
---

## UNIX/Linux 파이프
---