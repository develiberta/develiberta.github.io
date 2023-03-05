---
title: Storage Management
author: Develiberta
date: 2023-03-05 18:00:00 +0900
categories: [CS, OS]
tags: [CS, OS]
---

## Mass-Storeage Structure
---
- **Mass-Storage는 컴퓨터의 보조 기억 장치**로, 비휘발성의 특징을 가지고 있다.
- 보통, HDD(Hard Disk Drive)나 NVM(Non-Volatile Memory)를 의미한다.
- 가끔씩 마그네틱 테이프, 광학 디스크, 클라우드 저장소를 의미하기도 한다.

## Hard Disk Drive
---
![https://media.vlpt.us/images/hoyaho/post/25bfb154-3d5c-447d-a43a-21a3dee3e0ba/image.png](https://media.vlpt.us/images/hoyaho/post/25bfb154-3d5c-447d-a43a-21a3dee3e0ba/image.png)

- 대용량 데이터를 저장하는 용도로 사용한다.
- **하드디스크의 구조**
    - Spindle : 디스크를 회전시켜 내용을 읽고 쓰는 역할로, 흔히 들은 RPM이 스핀들 모터의 회전 속도를 의미
    - Platter : 실제 데이터가 저장되는 위치, 여러 트랙으로 구성
    - Track : 데이터가 저장되는 공간
    - Sector : 데이터 저장의 최소 단위, 하나의 파일만 저장 가능
    - Cylinder : 플래터 표면 동일 트랙들의 집합
    - Arm : 헤드를 움직이는 역할
    - Read-Write Head : 데이터를 저장 혹은 삭제하며, 데이터를 읽어들이는 역할도 수행
    - Block : 여러 섹터를 하나로 묶은 것

### HDD Scheduling
---
- 스케줄링의 목적은 **탐색 시간(Seek Time)을 줄이고 데이터 대역폭(bandwith)을 늘리는 것을 목표**로 한다.
- **Seek Time** : 헤드가 저장된 **트랙**으로 이동하는데 걸리는 시간
- **Rotation Latency** : 헤드가 트랙 내에서 원하는 **섹터**로 이동하는 것까지 걸리는 시간
- **Disk Bandwidth** : 첫 요청에서 마지막 전송 완료시까지 `전송 용량 / 전체 시간` 을 계산, **즉 시간당 한 번에 전송할 수 있는 용량**`대역폭 : 정보 전송 능력, 한 점과 한 점 사이에서 얼마나 많이 전송 가능한지?`

### FIFO Scheduling
---
![https://media.vlpt.us/images/hoyaho/post/86c7d5c8-0f98-4ab1-94f7-8d20aa7e4a28/image.png](https://media.vlpt.us/images/hoyaho/post/86c7d5c8-0f98-4ab1-94f7-8d20aa7e4a28/image.png)

- **먼저 들어온 요청 섹터를 먼저 처리**하는 방법이다.
- 본질적으로 공평하나 매우 느리다.

### Scan Scheduling
---
![https://media.vlpt.us/images/hoyaho/post/b9d8c236-a95e-4fb8-915e-414385bc206f/image.png](https://media.vlpt.us/images/hoyaho/post/b9d8c236-a95e-4fb8-915e-414385bc206f/image.png)

- **디스크 한 쪽 끝에 암을 배치시키고, 다른 쪽 끝으로 암을 이동시키며 스캔**하는 방법이다.
- 이 과정에서 헤드는 반전되고, 계속해서 이동한다.

### C-SCAN Scheduling
---
![https://media.vlpt.us/images/hoyaho/post/4ba899de-d707-4d48-a10c-03607ad676b7/image.png](https://media.vlpt.us/images/hoyaho/post/4ba899de-d707-4d48-a10c-03607ad676b7/image.png)

- 스캔 스케줄링의 변형으로 각 요청에 걸리는 시간이 균등해진다.
- 한 방향으로만 헤더가 움직이며 디스크 끝에 도달하면 즉시 처음으로 돌아온다.
- **그러나 디스크 끝에 도달 후 처음으로 돌아올 때 속도를 위해 데이터를 읽어들이지 않는다.**
- 최종적으로 마지막 실린더가 첫 실린더를 감싸는 순환 구조가 성립된다.

## Boot Block
---
![https://media.vlpt.us/images/hoyaho/post/e1d633eb-b7d3-4d72-8057-62de327ed2d9/image.png](https://media.vlpt.us/images/hoyaho/post/e1d633eb-b7d3-4d72-8057-62de327ed2d9/image.png)

- **부팅에 필요한 파일을 담는 디스크 영역**을 의미한다.
- 전원이 켜졌을 때 반드시 실행되어야 하는 프로그램, 즉 1장에서 이야기했던 [부트 스트랩](https://velog.io/@hoyaho/%EC%9D%B8%ED%94%84%EB%9F%B0-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%95%EB%A6%AC-Section-01#bootstrap-program) 프로그램이 적재되어 있다.
- MBR : 하드디스크의 첫 섹터에 저장되는 데이터이며 부트 코드와 파티션 테이블이 저장되어 있다. 또한, 어떤 파티션이 부트되어야 하는지에 대한 정보도 함께 적재한다.

## RAID
---
- **RAID** : Redundant Arrays of Independent Disks (복수 배열 독립 디스크)
- **여러 디스크를 묶어 하나의 디스크처럼 사용하는 디스크 구성 기술**이다.
- 여러 개의 하드 디스크에 일부 중복된 데이터를 나눠서 저장한다.
- **드라이브를 병렬로 작동시켜 데이터의 읽기 및 쓰기 속도를 향상**시킨다.
    - 여러 디스크에 데이터를 저장할 때, 비트 단위 혹은 블록 단위로 나누어 디스크에 저장한다.
- 또한 **신뢰성을 높여 데이터 보호 능력을 향상**시킨다.
    - 한 디스크의 데이터를 다른 디스크로 미러링, 즉 중복 저장하여 어떤 디스크가 잘못되어도 데이터를 보존할 수 있게 한다.

### RAID Levels
---
![https://media.vlpt.us/images/hoyaho/post/2bb40069-eae7-4fed-9b5d-1f7473778111/image.png](https://media.vlpt.us/images/hoyaho/post/2bb40069-eae7-4fed-9b5d-1f7473778111/image.png)

- 미러링은 신뢰성을 높여주지만 비용이 비싸고, 스트리핑은 효율성은 올라가지만 신뢰도를 낮추니 **상황에 맞게 적절히 조율**해야 한다.
- 비용 대비 성능에 따라 레벨로 분류한다.
    - RAID 0 : 데이터를 여러 디스크에 분할 저장하는 방식, 한 디스크에서 장애 발생 시 데이터가 모두 손실된다.
    - RAID 1 : 디스크에 기록된 정보를 모두 미러링하여 저장한다.
    - RAID 4 : 패리티 디스크를 추가하고, 패리티 비트를 넣어 디스크에 에러가 발생하지 않았는지 검사한다.
    - RAID 5 : 각 디스크에 패리티 비트를 추가한다.
    - RAID 6 : 각 디스크에 패리티 비트를 이중으로 추가하여 더 정교하게 에러를 감지한다.

![https://media.vlpt.us/images/hoyaho/post/7e421235-3294-4387-9939-3b6c4b89bfd8/image.png](https://media.vlpt.us/images/hoyaho/post/7e421235-3294-4387-9939-3b6c4b89bfd8/image.png)

- RAID 0 + 1 : 스트라이핑한 디스크를 미러링 하는 방식으로, 미러링 전에 한 디스크가 고장나면 데이터가 손실될 위험이 있다.
- RAID 1 + 0 : 미러링한 디스크를 스트라이핑 하는 방식이며 RAID01 보다 안정성이 높아 현업에서 주로 사용된다.

## I/O Systems
---
![https://media.vlpt.us/images/hoyaho/post/39000b52-ef06-4699-83c1-c98e0e2b0396/image.png](https://media.vlpt.us/images/hoyaho/post/39000b52-ef06-4699-83c1-c98e0e2b0396/image.png)

- 컴퓨터는 대부분 계산 혹은 **I/O를 처리하는 일에 대부분의 시간을 쏟는다.**
- 운영체제가 I/O 장치와 I/O 연산을 관리한다.
- 버스를 통해 CPU가 각각의 컨트롤러에게 I/O 처리 명령을 내린다.

### Three types of I/O
---
- I/O 작업 타입에는 세 가지가 있다.
- **polling(busy-waiting)** : 바쁜 비트가 종료될 때까지 반복해서 레지스터를 읽음을 의미한다.

![https://media.vlpt.us/images/hoyaho/post/1e2a1109-226a-413b-85ec-3537b49b1140/image.png](https://media.vlpt.us/images/hoyaho/post/1e2a1109-226a-413b-85ec-3537b49b1140/image.png)

- **interrupt** : CPU에서 인터럽트를 감지하면 ISR로 보내 인터럽트를 관리하게 한다. ISR 주소는 인터럽트 벡터 테이블에 보관된다.

![https://media.vlpt.us/images/hoyaho/post/274424ad-4be4-4430-bd7f-225c9ec224ca/image.png](https://media.vlpt.us/images/hoyaho/post/274424ad-4be4-4430-bd7f-225c9ec224ca/image.png)

- **DMA** : 컨트롤러에서 직접 I/O 작업을 처리하는 것을 의미하며 대용량 데이터 전달에 적합하다.

### Memory-Mapped I/O
---
- I/O 처리를 위해 컨트롤러에 명령을 내린다고 했는데, 어떤 방식으로 명령을 내리는걸까?
- 편리하게 I/O 장치에 접근하기 위해 Memory-Mapped I/O 구조를 사용한다.
- 각각의 I/O 컨트롤러는 CPU로부터 받은 명령 혹은 데이터를 저장하는 레지스터를 가지고 있고, **이 레지스터들을 메모리에 매핑한다.**
- 이로 인해 메모리 주소로 레지스터 값을 읽고 쓸 수 있다.
- 즉, 레지스터에 메모리 주소를 부여하는 것이다.

### Blocking I/O vs Non-blocking I/O
---
- Blocking : 요청 스레드의 실행이 즉시 지연되고, 요청 사항을 모두 마무리한 뒤에야 리턴한다.
- Non-blocking : 요청 스레드의 실행이 중단되지 않고, 요청 시 바로 리턴한다.

---

## File-System Interface
---
- 파일 시스템은 O/S의 모든 사용자 데이터 혹은 프로그램을 논리적으로 저장하고, 사용자가 접근 가능하게 설정하는 것을 의미한다.
- 관련 데이터를 저장하는 파일과, 시스템 내의 모든 파일을 정리하는 디렉터리, 이 두 가지로 파일 시스템이 구성된다.
- 쉽게 이야기하면 **O/S에서 파일을 관리하는 부분**이라고 보면 된다.

### File Access Method
---
- 파일에 접근하는 방법은 두 가지가 있다.

![https://media.vlpt.us/images/hoyaho/post/b1e16f92-ecac-49fb-b8bc-2b9b8faf7e2f/image.png](https://media.vlpt.us/images/hoyaho/post/b1e16f92-ecac-49fb-b8bc-2b9b8faf7e2f/image.png)

1. **순차 접근(sequential access)** : 파일 내 정보를 순서대로 접근하는 것을 의미한다.
    - 파일의 레코드가 순차적으로 기록되어 있어 판독할 때도 순차적으로 판독한다.
2. **직접 접근(direct access)** : 파일 내에서 원하는 레코드로 바로 진입해 그 위치에서부터 파일을 읽는 것을 의미한다.

### Directory Structure
---
- 디렉터리는 파일 시스템 내부에 있는 것으로 효율적 파일 사용을 위해 디스크에 존재하는 파일에 대한 여러 정보를 가지고 있는 특수한 형태의 파일이다.
- 디렉터리는 각 파일의 위치, 크기등의 정보를 가지고 있다.
- 디렉터리 구조의 종류에는 1단계 디렉터리, 2단계 디렉터리, 트리 디렉터리, 비순환 그래프 디렉터리, 일반적인 그래프 디렉터리가 있다.

![https://media.vlpt.us/images/hoyaho/post/a3eb7a68-683e-43e3-8734-2876ef252a49/image.png](https://media.vlpt.us/images/hoyaho/post/a3eb7a68-683e-43e3-8734-2876ef252a49/image.png)

---

## File-System Implementation
---
### Allocation Method
---
- **디스크 공간에 파일을 할당할 때 어떤 방식으로 해야 효율, 속도를 개선**할 수 있을까?

### Contiguous Allocation
---
- 연속 할당은 **하나의 파일이 디스크에 연속적으로 저장**되는 것을 의미한다.
- 파일이 삭제되면 홀이 생기고, 이 작업이 반복되면 홀이 흩어지게 된다. 이럴 때 새 파일을 어느 홀에 배치할 수 있느냐에 따라 **[외부 단편화](https://velog.io/@hoyaho/%EC%9D%B8%ED%94%84%EB%9F%B0-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%95%EB%A6%AC-Section-10-%EF%BD%9C-CS-Study#memory-allocation)** 문제가 발생할 수 있다.

### Linked Allocation
---
![https://media.vlpt.us/images/hoyaho/post/b8984edd-392e-4c61-ab1c-0bf4c3dab24a/image.png](https://media.vlpt.us/images/hoyaho/post/b8984edd-392e-4c61-ab1c-0bf4c3dab24a/image.png)

- 파일을 연속적으로 저장하지 않고 링크드 리스트를 이용해 **아무 곳에나 들어갈 수 있게 만드는 방법**이다.
- 연속 할당의 문제점을 해결할 수 있는 방법이다.
- 그러나, 중간에 한 섹터에 문제가 발생하면 이 후의 링크도 모두 잃기 때문에 안정적이지 못하다.

### File Allocation Table
---
![https://media.vlpt.us/images/hoyaho/post/8b7cded8-f49b-4c88-99e4-d0e97fd9b156/image.png](https://media.vlpt.us/images/hoyaho/post/8b7cded8-f49b-4c88-99e4-d0e97fd9b156/image.png)

- 연결 할당의 변종으로, 하나의 데이터 블록에 다음 블록에 대한 정보를 담고 있는 테이블이다.
- 즉, 포인터를 별도의 테이블에 따로 보관하는 방법이다.
- 연결 할당의 문제점을 해결할 수 있는 방법이다.

### Indexed Allocation
---
![https://media.vlpt.us/images/hoyaho/post/b0c11578-fb84-407c-a743-06613fbdb2f2/image.png](https://media.vlpt.us/images/hoyaho/post/b0c11578-fb84-407c-a743-06613fbdb2f2/image.png)

- 각 파일에 포인터가 순서대로 저장된 인덱스 블록을 할당한다.
- 인덱스 블록의 i 번째 항목이 파일의 i 번째 블록을 가리킨다.
- 너무 큰 파일인 경우 하나의 블록으로 인덱스를 모두 저장할 수 없기 때문에, 다중 인덱스 블록을 구성해야 한다.