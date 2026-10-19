# 01. VMware 가상 네트워크 및 서버 구성

## 1. 실습 개요

### 1.1 실습 목적

Rocky Linux 9.8 기반 사내 업무 포털 인프라 구축을 위해 VMware Workstation의 가상 네트워크 환경을 구성한다.

외부 인터넷 연결을 위한 NAT 네트워크와 서버 간 내부 통신을 위한 Host-only 네트워크를 분리하여, 각 서버가 용도에 맞는 네트워크 인터페이스를 사용하도록 설계한다.

### 1.2 실습 목표

- VMware Workstation 가상 네트워크 구조 이해
- NAT 및 Host-only 네트워크 구성
- 내부 서비스망의 IP 주소 체계 설계
- Windows 호스트의 VMware 네트워크 어댑터 검증
- Rocky Linux 가상머신의 하드웨어 및 네트워크 어댑터 구성

### 1.3 실습 환경

| 구분 | 환경 |
|---|---|
| Host OS | Windows |
| Virtualization | VMware Workstation |
| Guest OS | Rocky Linux 9.8 |
| CPU | Intel Core i5-12400 계열 |
| Host RAM | 16GB |
| 가상 네트워크 | VMnet8 (NAT), VMnet2 (Host-only) |

---

## 2. 네트워크 설계

### 2.1 네트워크 구성

| 네트워크 | 유형 | 네트워크 주소 | 서브넷 마스크 | DHCP | 용도 |
|---|---|---|---|---|---|
| VMnet8 | NAT | 10.10.10.0/24 | 255.255.255.0 | Enabled | 인터넷 연결 및 패키지 설치 |
| VMnet2 | Host-only | 192.168.100.0/24 | 255.255.255.0 | Disabled | 내부 서버 통신 및 SSH 관리 |

### 2.2 NAT 네트워크를 사용하는 이유

NAT(Network Address Translation)는 가상머신이 호스트 PC의 네트워크 연결을 통해 외부 인터넷에 접근할 수 있도록 하는 방식이다.

본 프로젝트에서는 다음 목적으로 사용한다.

- Rocky Linux 패키지 설치 및 업데이트
- 외부 패키지 저장소 접근
- 각종 서버 소프트웨어 다운로드

VMnet8의 DHCP 기능을 활성화하여 가상머신의 NAT 인터페이스가 IP 주소를 자동으로 할당받도록 구성하였다.

### 2.3 Host-only 네트워크를 사용하는 이유

Host-only 네트워크는 Windows 호스트와 동일 가상 네트워크에 연결된 가상머신들이 내부적으로 통신할 수 있도록 하는 방식이다.

본 프로젝트에서는 다음 목적으로 사용한다.

- 웹 서버와 데이터베이스 서버 간 통신
- 웹 서버와 NFS 서버 간 파일 공유
- HAProxy와 웹 서버 간 통신
- 중앙 모니터링 서버의 상태 정보 수집
- Windows 호스트에서 Linux 서버로 SSH 접속

내부 서버는 고정 IP 주소를 사용하므로 VMnet2의 DHCP 기능을 비활성화하였다.

Host-only 네트워크에는 외부 인터넷으로 직접 연결되는 기본 경로를 구성하지 않는다.

### 2.4 서버별 내부 IP 주소 설계

| Hostname | IP Address | 주요 역할 |
|---|---|---|
| lb01 | 192.168.100.10 | HAProxy 로드밸런서 |
| web01 | 192.168.100.21 | Apache/PHP 웹 서버 |
| web02 | 192.168.100.22 | Apache/PHP 웹 서버 |
| db01 | 192.168.100.31 | MariaDB 데이터베이스 |
| nas01 | 192.168.100.41 | NFS 및 LVM 스토리지 |
| mon01 | 192.168.100.51 | Prometheus/Grafana 모니터링 |

※ 위 주소는 고정 IP이며, Rocky Linux 설치 후 실제 인터페이스에 설정할 예정이다.

---

## 3. VMware 가상 네트워크 구성

### 3.1 Virtual Network Editor 실행

VMware Workstation에서 다음 경로로 이동하였다.

`Edit → Virtual Network Editor`

기존 VMnet0, VMnet1, VMnet8 구성을 확인하고 프로젝트 전용 네트워크인 VMnet2를 추가하였다.

### 3.2 VMnet8 NAT 설정

기존 VMnet8의 NAT 설정을 유지하고 DHCP 기능을 활성화하였다.

**적용 설정**

| 항목 | 설정값 |
|---|---|
| Network | VMnet8 |
| Network Type | NAT |
| Subnet IP | 10.10.10.0 |
| Subnet Mask | 255.255.255.0 |
| Host Virtual Adapter | Enabled |
| Local DHCP Service | Enabled |

**설정 화면**

![VMnet8 NAT 설정](../screenshots/network/01-VMnet8-configuration.png)

### 3.3 VMnet2 Host-only 설정

Virtual Network Editor에서 `Add Network`를 선택하여 VMnet2를 생성하였다.

**적용 설정**

| 항목 | 설정값 |
|---|---|
| Network | VMnet2 |
| Network Type | Host-only |
| Subnet IP | 192.168.100.0 |
| Subnet Mask | 255.255.255.0 |
| Host Virtual Adapter | Enabled |
| Local DHCP Service | Disabled |

**설정 화면**

![VMnet2 Host-only 설정](../screenshots/network/01-VMnet2-configuration.png)

DHCP를 비활성화한 이유는 각 서버에 고정 IP를 직접 지정하여 서버 간 통신 주소를 일관되게 관리하기 위해서이다.

---

## 4. Windows 호스트 네트워크 검증

### 4.1 검증 명령어

Git Bash에서 다음 명령어를 실행하였다.

```bash
ipconfig.exe
```

Windows에 생성된 VMware 가상 네트워크 어댑터의 IPv4 주소와 서브넷 마스크를 확인하였다.

### 4.2 VMnet8 검증 결과

```text
이더넷 어댑터 VMware Network Adapter VMnet8:

IPv4 주소         : 10.10.10.1
서브넷 마스크      : 255.255.255.0
기본 게이트웨이    : 없음
```

### 4.3 VMnet2 검증 결과

```text
이더넷 어댑터 VMware Network Adapter VMnet2:

IPv4 주소         : 192.168.100.1
서브넷 마스크      : 255.255.255.0
기본 게이트웨이    : 없음
```

### 4.4 검증 결과 분석

| 검증 항목 | 예상 결과 | 실제 결과 | 판정 |
|---|---|---|---|
| VMnet8 IPv4 | 10.10.10.1 | 10.10.10.1 | 정상 |
| VMnet8 Subnet Mask | 255.255.255.0 | 255.255.255.0 | 정상 |
| VMnet2 IPv4 | 192.168.100.1 | 192.168.100.1 | 정상 |
| VMnet2 Subnet Mask | 255.255.255.0 | 255.255.255.0 | 정상 |

Windows 호스트에서 VMnet8과 VMnet2 네트워크 어댑터가 계획한 IP 대역으로 설정된 것을 확인하였다.

두 어댑터의 기본 게이트웨이가 비어 있는 것은 Windows 가상 어댑터 설정상 문제가 되지 않는다.

**검증 범위:** 현재까지 확인한 것은 Windows 호스트의 가상 어댑터 설정이다. Rocky Linux 가상머신의 인터넷 접속과 서버 간 실제 통신은 OS 설치 후 추가 검증할 예정이다.

---

## 5. WEB01 가상머신 생성 및 하드웨어 구성

### 5.1 가상머신 사양

첫 번째 웹 서버인 WEB01 가상머신을 생성하였다.

| 항목 | 설정값 |
|---|---|
| VM Name | WEB01 |
| vCPU | 1 |
| RAM | 1GB |
| Disk Type | SCSI |
| Disk Capacity | 20GB |
| Network Adapter 1 | NAT |
| Network Adapter 2 | Custom (VMnet2) |
| CD/DVD | SATA |


### 5.2 네트워크 어댑터 구성

WEB01에는 두 개의 네트워크 어댑터를 구성하였다.

**Network Adapter 1**

- Type: NAT
- Network: VMnet8
- 용도: 인터넷 연결 및 패키지 설치
- IP 할당: DHCP 예정

**Network Adapter 2**

- Type: Custom
- Network: VMnet2 (Host-only)
- 용도: 내부 서버 간 통신
- 예정 IP: 192.168.100.21/24

NAT 인터페이스는 기본 경로로 사용하고 Host-only 인터페이스에는 별도의 기본 게이트웨이를 설정하지 않을 예정이다.

### 5.3 하드웨어 설정 확인

VMware Workstation의 Virtual Machine Settings에서 CPU, RAM, SCSI 디스크, 네트워크 어댑터 2개가 구성된 것을 확인하였다.

![WEB01 하드웨어 구성](../screenshots/network/03-web01-hardware-configuration.png)

---

## 6. 현재 진행 상황

- [x] VMware Virtual Network Editor 설정 확인
- [x] VMnet8 NAT 및 DHCP 설정
- [x] VMnet2 Host-only 네트워크 생성
- [x] VMnet2 DHCP 비활성화
- [x] Windows VMnet8 IP 확인
- [x] Windows VMnet2 IP 확인
- [x] WEB01 가상머신 생성
- [x] WEB01 CPU, RAM, SCSI 디스크 구성
- [x] WEB01 NAT 및 Host-only 어댑터 추가
- [ ] Rocky Linux 9.8 설치
- [ ] Rocky Linux 네트워크 인터페이스 확인
- [ ] NAT 인터페이스 DHCP 할당 확인
- [ ] Host-only 인터페이스 고정 IP 설정
- [ ] Windows ↔ WEB01 통신 검증
- [ ] WEB01 인터넷 접속 및 DNS 검증
- [ ] 나머지 서버 구성 및 서버 간 통신 검증

---

## 7. 배운 점

### 7.1 NAT와 Host-only의 차이

NAT 네트워크는 가상머신이 외부 인터넷에 접근하는 데 사용하며, Host-only 네트워크는 호스트와 가상머신 사이의 내부 통신 환경을 구성하는 데 사용한다.

두 네트워크를 분리함으로써 외부 연결과 내부 서비스 통신을 목적에 따라 관리할 수 있다.

### 7.2 DHCP와 고정 IP

DHCP는 IP 주소 등의 네트워크 정보를 자동으로 할당하므로 패키지 설치용 NAT 인터페이스에 사용하기 적합하다.

반면 웹 서버, DB 서버, NFS 서버 등은 다른 서버에서 항상 동일한 주소로 접근할 수 있어야 하므로 내부 통신 인터페이스에 고정 IP를 사용하도록 설계하였다.

### 7.3 Windows 가상 어댑터와 VM 내부 NIC

Windows의 VMware Network Adapter VMnet2는 호스트 PC의 가상 네트워크 인터페이스이다.

Rocky Linux에 설정할 `192.168.100.21`은 WEB01 내부 NIC의 주소이며, Windows의 `192.168.100.1`과 서로 다른 장치에 할당된다.

따라서 두 장치는 같은 서브넷에 속하지만 IP 주소는 중복되지 않아야 한다.

---

## 8. 다음 실습

**Rocky Linux 9.8 설치 및 WEB01 네트워크 설정**

예정 작업:

1. Rocky Linux 9.8 ISO 연결
2. Minimal Install 진행
3. 시스템 호스트명 및 관리자 계정 설정
4. NAT/Host-only 네트워크 인터페이스 확인
5. VMnet2에 192.168.100.21/24 고정 IP 설정
6. Windows와 WEB01 간 통신 확인
7. DNS 및 외부 패키지 저장소 접근 확인

실습 결과를 검증한 뒤 이 문서에 내용을 추가할 예정이다.

