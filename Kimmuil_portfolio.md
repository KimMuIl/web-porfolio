# 네트워크 엔지니어 학습 노트

> **작성자**: 김무일    
> **기간**: 2025.03.17 ~   
> **도구**: Cisco Packet Tracer    
> **목표**: 실무 능력 향상  

---

## 1. 네트워크 기본 구조 및 개념 정리
### IP Address Classes
N : Network number   
H : Host number
- **class A :** N  H  H  H /8
- **class B :** N  N  H  H /16
- **class C :** N  N  N  H /24
- **class D :** for multicast
- **class A :** for research
### IP Address Bit Patterns
- **class A :** 00000000.        .        .        /8      0~127
- **class B :** 10000000.        .        .        /16     128~191
- **class C :** 11000000.        .        .        /24     192~223
- **class D :** 11100000.                                  224   
  ※127은 안쓴다 시스템에 사용한다.
- **사설 IP**
   
  10.0.0.0 ~ 10.255.255.255 (10.0.0.0/8)   
  172.16.0.0 ~ 172.31.255.255.(172.16.0.0/12)   
  192.168.0.0 ~ 192.168.255.255 (192.168.0.0/16)

### VLAN
- 부서별로 통신을 분리하여 네트워크 효율성을 증가시키는 기술


---

## 2. Packet Tracer 실습 내용 정리

### [실습 1] 장비 초기값
- **목표**: 장비들의 초기값 주기   
- **구성도**   
    - 라우터 1대   
```bash
Router>en                                     //관리자 모드로 전환
Router#configure terminal                     //시스코 장비 설정을 시작
Router(config)#line console 0                 //콘솔 포트 설정을 시작
Router(config-line)#exec-timeout 0 0          //비활성 상태일 경우 자동 로그아웃되지 않도록 설정
Router(config-line)#logging synchronous       //백그라운드에서 발생하는 시스템 메세지로 인하여 입력하던 명령어가 끊기지 않게 한다.
Router(config-line)#exit                      //현 상태에서 밖으로 나가기
Router(config)#no ip domain lookup            //모르는 명령어를 도메인이 더 이상 검색하지 않는다.
Router(config)#exit                           //현 상태에서 밖으로 나가기
Router#copy running-config startup-config     //저장 명령어
```
### [실습 2] 백업 방법
- **목표**: 장비들 백업  
- **구성도**   
    - 라우터 1대, TFTP서버 1대   
- **방법 1**
```bash
Router#copy running-config tftp:
Address or name of remote host[]? 예시)192.168.1.10               // (TFTP서버 주소 값)
Destination filename [Router-config]? 예시)Router-20250317-config // (파일 이름 설정)
```
- **방법 2**
```bash
Router#copy running-config flash:
Destination filename [running-config]? 예시)20250317-config       // (파일 이름 설정)
Building configuration...
[ok]
//백업 하기
Router#copy flash: running-config
Source filename[]? 예시)20250317-config
```
- **방법 3**
  - 메보장에 running-config를 복사해서 붙여넣기

---

### [실습3] Subnetting (서브넷팅)

- **정의**: 하나의 IP 네트워크를 여러 개의 소규모 네트워크(서브넷)로 나누는 기술  
- **필요성**
  - IP 자원 절약
  - 네트워크 성능 향상
  - 보안 및 트래픽 관리 용이

###  서브넷 마스크 예시

| 서브넷 마스크          | 프리픽스 | 사용 가능한 호스트 수 | 서브넷 수 (Class C 기준) |
|------------------------|----------|------------------------|---------------------------|
| 255.255.255.0          | /24      | 254 (2⁸ - 2)           | 1                         |
| 255.255.255.192        | /26      | 62 (2⁶ - 2)            | 4                         |
| 255.255.255.240        | /28      | 14 (2⁴ - 2)            | 16                        |
| 255.255.255.252        | /30      | 2 (2² - 2)             | 64                        |

### 🧮 계산 공식
- **호스트 수 계산**: 2ⁿ - 2 (n = 호스트 비트 수)
- **서브넷 수 계산**: 2ⁿ (n = 빌린 비트 수)

---

### [실습4] CIDR (Classless Inter-Domain Routing)

- **정의**: 클래스 구분 없이 네트워크 주소를 `/프리픽스` 형식으로 표현하는 방식  
- **형식 예시**
  - `192.168.1.0/24`: 일반적인 Class C
  - `192.168.1.0/28`: 서브넷팅된 주소
  - `10.0.0.0/8`: 대규모 네트워크 할당

###  장점
- 유연한 IP 주소 할당
- 주소 공간 절약
- 라우팅 테이블 축소 (요약 가능)

---

### [실습5] Loopback
- **정의**: 논리적 인터페이스를 의미 (실제 존재하지 않는 인터페이스)
- **필요성**
  - 라우팅 실습시 네트워크를 추가로 만들기 위해 사용
  - 라우터가 다운되지 않는한 인터페이스는 계속 UP 상태 유지 (안정적, 튼튼함)
  - 라우팅 프로토콜에서 router-id 사용
  - 장비자체 테스트용
  - 
###  Loopback 활용 예시
설정 ip 값 : 1.1.1.1/24
```base
Router(config)# int lookback0
Router(config-if)# ip add 1.1.1.1 255.255.255.0

#삭제 가능
Router(config)# no loopback0
```
---

## [실습6] ACL (Access Control List)

### ACL이란?
> **Access Control List**는 네트워크 장비(Router 등)에서 트래픽 흐름을 제어하기 위한 필터링 기술   
>  주로 **보안 정책 적용**, **접근 제한**, **트래픽 제어**에 사용

---

## [실습6-1] Standard Access List

###  특징
- **출발지 IP 주소만** 기준으로 필터링
- 번호 범위: **1~99** 또는 **1300~1999**
- 일반적으로 **목적지 가까운 쪽** 인터페이스에 적용

###  설정 예시  
> 192.168.10.0/24 네트워크에서만 접근을 허용하고, 나머지는 차단

```bash
Router(config)# access-list 10 permit 192.168.10.0 0.0.0.255
Router(config)# access-list 10 deny any
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip access-group 10 in
```

---

## [실습6-2] Extended Access List

###  특징
- **출발지 IP, 목적지 IP, 프로토콜(TCP, UDP 등), 포트 번호**까지 상세하게 제어함
- 번호 범위: **100~199** 또는 **2000~2699**
- 일반적으로 **출발지 가까운 쪽** 인터페이스에 적용

###  설정 예시  
> 192.168.20.0/24 네트워크에서 출발한 HTTP(80번 포트) 트래픽만 허용하고, 나머지는 차단

```bash
Router(config)# access-list 110 permit tcp 192.168.20.0 0.0.0.255 any eq 80
Router(config)# access-list 110 deny ip any any
Router(config)# interface fastEthernet 0/1
Router(config-if)# ip access-group 110 in
```
