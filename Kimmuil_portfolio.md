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

### [실습 2] 백업 방법
