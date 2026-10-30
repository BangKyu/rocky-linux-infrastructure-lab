# Rocky Linux 9.8 기반 사내 업무 포털 인프라 구축 및 장애 복구

## 1. 프로젝트 소개

VMware Workstation 환경에서 Rocky Linux 9.8 기반 사내 업무 포털 인프라를 구축하고, Linux 서버 운영에 필요한 네트워크, 웹 서비스, 데이터베이스, 스토리지, 보안, 모니터링 및 장애 복구 기술을 실습하는 프로젝트이다.

각 서버의 역할을 분리하고 실제 서비스 연동과 장애 상황을 검증하여 Linux 시스템 엔지니어의 인프라 구축 및 운영 역량을 학습하는 것을 목표로 한다.

실습 과정은 기술 주제별로 구분하며, 각 단원 완료 후 명령어, 설정 내용, 실제 검증 결과 및 스크린샷을 GitHub에 기록한다.

## 2. 프로젝트 목표

- **Linux 인프라:** Rocky Linux, VMware 네트워크, SSH, 사용자 및 서비스 관리
- **웹 서비스:** Apache, PHP, MariaDB 기반 LAMP 환경 구축
- **로드밸런싱:** HAProxy Round Robin, 헬스 체크 및 장애 전환
- **스토리지:** RAID, LVM, NFS, UID/GID 및 ACL 관리
- **파일 배포 및 백업:** rsync, Bash, cron
- **보안:** Linux Hardening, firewalld, SELinux
- **운영 관리:** Process & Resource Management, Log Management
- **모니터링:** Prometheus, Node Exporter, Grafana, Alertmanager
- **장애 복구:** 웹·DB·스토리지 장애 재현 및 복구 검증
- **문서화:** GitHub 실습 문서, 인프라 구성도 및 최종 발표 자료

## 3. 실습 환경

| 항목 | 환경 |
|---|---|
| 가상화 | VMware Workstation |
| 운영체제 | Rocky Linux 9.8 |
| 호스트 운영체제 | Windows |
| 호스트 메모리 | 16GB |
| NAT 네트워크 | VMnet8 / 10.10.10.0/24 |
| Host-only 네트워크 | VMnet2 / 192.168.100.0/24 |
| Git | Git Bash |
| 문서 관리 | GitHub Markdown |

호스트의 메모리 자원을 고려하여 실습에 필요한 가상머신만 선택적으로 실행한다.

## 4. 인프라 구성

### 4.1 서버 역할

| 서버 | 내부 IP | 역할 | 주요 기술 |
|---|---|---|---|
| LB01 | 192.168.100.10 | 로드밸런서 | HAProxy |
| WEB01 | 192.168.100.21 | 웹 서버 1 | Apache, PHP |
| WEB02 | 192.168.100.22 | 웹 서버 2 | Apache, PHP |
| DB01 | 192.168.100.31 | 데이터베이스 서버 | MariaDB |
| NAS01 | 192.168.100.41 | 공유 스토리지 서버 | RAID, LVM, NFS |
| MON01 | 192.168.100.51 | 모니터링 서버 | Prometheus, Grafana, Alertmanager |

### 4.2 목표 아키텍처

![사내 업무 포털 목표 아키텍처: HAProxy, WEB01·WEB02, MariaDB, NFS, Prometheus·Grafana](diagrams/infrastructure-architecture.svg)

> 목표 구성도이다. 현재 HAProxy, Apache/PHP, MariaDB, 사내 업무 포털 CRUD 및 rsync 동기화, NAS01의 RAID 1·LVM·NFS 구성과 WEB01·WEB02 NFS 연동을 검증하였다. MON01에 Prometheus를 구축하고 6대 서버의 Node Exporter를 연동하여 전체 수집 대상 7 / 7 UP을 검증하였다. Grafana에서 6대 서버의 리소스 대시보드를 검증했고, Alertmanager에서 WEB01 Node Exporter 장애 발생 및 복구 알림을 로컬 Webhook으로 검증하였다.

웹 애플리케이션 코드는 WEB01과 WEB02에 각각 배포한다.

업무 데이터는 DB01의 MariaDB에 저장하고, 공유 파일은 NAS01의 NFS 스토리지에서 관리하는 구조를 목표로 한다.

MON01의 Prometheus에서 6대 서버의 CPU·메모리·디스크 등 시스템 리소스 메트릭을 수집하고, Grafana에서 서버별로 시각화하였다. Alertmanager는 WEB01 Node Exporter의 장애 발생과 복구 알림을 로컬 Webhook으로 전달하도록 구성하였다.

## 5. 실습 문서 및 진행 계획

전체 프로젝트는 기술 주제별 **33개 단원**으로 진행한다.

기존 완료 단원은 유지하고, 보안 실습 이후 운영 관리·모니터링·복구 실습을 별도 Phase로 구분하였다.

### PHASE 1. Linux 기본 인프라 구축

| 번호 | 실습 문서 | 상태 |
|---|---|---|
| 01 | [VMware 가상 네트워크 구축](docs/01-network.md) | 완료 |
| 02 | [WEB01 서버 구축 및 검증](docs/02-web01.md) | 완료 |
| 03 | [WEB02 서버 구축 및 검증](docs/03-web02.md) | 완료 |
| 04 | [LB01 서버 구축 및 검증](docs/04-lb01.md) | 완료 |

### PHASE 2. Apache 및 HAProxy 로드밸런싱

| 번호 | 실습 문서 | 상태 |
|---|---|---|
| 05 | [WEB01 Apache 웹 서버 구축 및 HTTP 검증](docs/05-web01-apache.md) | 완료 |
| 06 | [WEB02 Apache 웹 서버 구축 및 HTTP 검증](docs/06-web02-apache.md) | 완료 |
| 07 | [LB01 HAProxy 설치 및 로드밸런싱 구성](docs/07-haproxy.md) | 완료 |
| 08 | [HAProxy 장애 전환 및 복구 검증](docs/08-haproxy-failover.md) | 완료 |

### PHASE 3. MariaDB 및 LAMP 웹 서비스

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 09 | [DB01 서버 구축 및 MariaDB 설치·연동 검증](docs/09-mariadb.md) | 완료 |
| 10 | [WEB01/WEB02 PHP 설치 및 MariaDB 연동 검증](docs/10-php-mariadb.md) | 완료 |
| 11 | [PHP + MariaDB 기반 사내 업무 포털 CRUD 구현 및 HAProxy 통합 검증](docs/11-php-portal-crud.md) | 완료 |
| 12 | [rsync 기반 웹 애플리케이션 배포 및 동기화](docs/12-rsync-deployment.md) | 완료 |

### PHASE 4. RAID · LVM · NFS 스토리지

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 13 | [NAS01 서버 구축 및 네트워크 구성](docs/13-nas01.md) | 완료 |
| 14 | [RAID 구성, 디스크 장애 및 복구 검증](docs/14-raid.md) | 완료 |
| 15 | [LVM 논리 볼륨 구성 및 용량 확장](docs/15-lvm.md) | 완료 |
| 16 | [NAS01 NFS 공유 스토리지 구축](docs/16-nfs-server.md) | 완료 |
| 17 | [WEB01/WEB02 NFS 연동 및 파일 권한 검증](docs/17-nfs-client.md) | 완료 |

RAID 1을 NFS 스토리지의 기본 구성으로 활용하고, RAID 0·5·6은 구조 비교 및 선택적 추가 실습으로 다룬다.

### PHASE 5. Linux 보안 강화

| 번호 | 실습 문서 | 상태 |
|---|---|---|
| 18 | [SSH 보안 강화](docs/18-ssh-hardening.md) | 완료 |
| 19 | [firewalld 및 SELinux 보안 정책](docs/19-firewall-selinux.md) | 완료 |
| 20 | [사용자 및 서비스 보안 강화](docs/20-account-service-hardening.md) | 완료 |
| 21 | [보안 업데이트 및 패키지 의존성 충돌 해결](docs/21-security-updates.md) | 완료 |

18~21단원은 WEB01·WEB02의 SSH, 방화벽, 계정·서비스 및 보안 업데이트를 다룬다. 문서에 기록된 검증 범위는 각 단원에서 확인할 수 있다.

### PHASE 6. Linux 운영 관리

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 22 | [프로세스 및 리소스 관리](docs/22-process-resource.md) | 완료 |
| 23 | [로그 관리 및 로그 기반 장애 분석](docs/23-log-management.md) | 완료 |

### PHASE 7. 모니터링 및 장애 알림

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 24 | [Prometheus 모니터링 서버 구축 및 메트릭 수집 검증](docs/24-prometheus.md) | 완료 |
| 25 | [Node Exporter 기반 Linux 서버 메트릭 수집 및 통합 모니터링](docs/25-node-exporter.md) | 완료 |
| 26 | [Grafana 기반 Linux 서버 리소스 대시보드 구축 및 통합 모니터링](docs/26-grafana.md) | 완료 |
| 27 | [Alertmanager 기반 Linux 서버 장애 감지 및 알림 검증](docs/27-alertmanager.md) | 완료 |

### PHASE 8. 백업 자동화 및 장애 복구

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 28 | [Bash·rsync 기반 파일 백업 및 복구 검증](docs/28-file-backup.md) | 완료 |
| 29 | [cron을 이용한 백업 자동화](docs/29-cron-backup.md) | 완료 |
| 30 | [MariaDB 데이터베이스 백업 및 복구 검증](docs/30-mariadb-backup.md) | 완료 |
| 31 | [NFS 데이터 백업 및 복구 검증](docs/31-nfs-backup.md) | 완료 |
| 32 | [통합 장애 시나리오 및 서비스 복구](docs/32-integrated-recovery.md) | 완료 |

### PHASE 9. 최종 포트폴리오

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 33 | [전체 인프라 최종 검증](docs/33-final-verification.md) | 완료 |

> 33개 단원 문서와 최종 검증 캡처를 저장소에 기록했다.

## 6. 주요 장애 대응 및 검증

### 6.1 서비스 연동과 장애 분석

| 분야 | 확인한 결과 | 상세 기록 |
|---|---|---|
| 네트워크 | WEB01에서 Windows로의 Ping 장애를 분석하고 복구 | [02단원](docs/02-web01.md) |
| 로드밸런싱 | HAProxy의 WEB01 장애 전환 및 복구, Round Robin 응답 확인 | [08단원](docs/08-haproxy-failover.md) |
| DB 접근 | MariaDB 원격 접속과 최소 권한 검증 | [09단원](docs/09-mariadb.md) |
| PHP·DB | WEB02의 HTTP 500을 PHP-FPM 로그에서 확인하고 MariaDB ERROR 1045 인증 문제를 복구. 두 웹 서버의 공통 DB 조회 검증 | [10단원](docs/10-php-mariadb.md) |
| 업무 포털 | WEB01·WEB02의 CRUD와 HAProxy 쿠키 기반 Sticky Session 검증 | [11단원](docs/11-php-portal-crud.md) |

10단원의 임시 PHP 테스트 파일은 검증 후 제거했으며 두 웹 서버에서 HTTP 404를 확인했다. 11단원의 웹 서버 장애 시 PHP 세션 자동 복구는 검증하지 않았다.

### 6.2 구축 후 운영 검증

| 분야 | 확인한 결과 | 상세 기록 |
|---|---|---|
| 배포 | rsync 최초·증분 동기화, 포털 파일의 스테이징 복사와 권한·SELinux·HTTP 확인. 스테이징과 운영 파일이 같아 운영 파일은 덮어쓰지 않음 | [12단원](docs/12-rsync-deployment.md) |
| 스토리지 | RAID 1 장애·복구, LVM 확장, NFS 서버 재부팅 후 유지, 두 웹 서버의 NFSv4.2 접근과 권한·자동 마운트 확인 | [14](docs/14-raid.md) · [15](docs/15-lvm.md) · [16](docs/16-nfs-server.md) · [17단원](docs/17-nfs-client.md) |
| 보안 | SSH, firewalld·SELinux, 계정·서비스, 보안 업데이트와 재부팅 후 동작 점검 | [18](docs/18-ssh-hardening.md) · [19](docs/19-firewall-selinux.md) · [20](docs/20-account-service-hardening.md) · [21단원](docs/21-security-updates.md) |
| 운영 관리 | CPU 부하 종료 후 Apache HTTP 200, 의도한 HTTP 404를 Access 로그에서 추적한 뒤 HTTP 200 확인 | [22](docs/22-process-resource.md) · [23단원](docs/23-log-management.md) |
| 모니터링·알림 | Prometheus와 6대 서버의 Node Exporter 전체 7/7 UP, Grafana 자원 그래프, WEB01 장애·복구의 로컬 Webhook 알림 확인 | [24](docs/24-prometheus.md) · [25](docs/25-node-exporter.md) · [26](docs/26-grafana.md) · [27단원](docs/27-alertmanager.md) |
| 백업·복원 | 파일·MariaDB·NFS 데이터의 백업과 별도 경로 복원, cron 실행, 대상 파일의 SHA-256 비교 | [28](docs/28-file-backup.md) · [29](docs/29-cron-backup.md) · [30](docs/30-mariadb-backup.md) · [31단원](docs/31-nfs-backup.md) |

### 6.3 통합 장애 복구와 최종 상태

[32단원](docs/32-integrated-recovery.md)에서는 세 가지 장애를 발생 전·중·복구 후로 비교했다.

| 장애 지점 | 장애 중 영향 | 복구 후 확인 |
|---|---|---|
| DB01 MariaDB 중지 | WEB01 포털 HTTP 500 | 포털 HTTP 200, 공지사항 1건 유지 |
| NAS01 SSH 중지 | 원격 rsync 백업 실패 | SSH 복구 후 백업 성공, `portal/index.php` SHA-256 일치 |
| LB01 HAProxy 중지 | 경유 HTTP 연결 거부. WEB01 직접 접속은 HTTP 200 | HAProxy 경유 HTTP 200 |

MariaDB 장애 중 포털 HTTP 500은 확인했지만, PHP 예외 메시지 원문은 확인하지 못했다. NAS 백업 해시 비교는 해당 파일에 한정된다.

[33단원 최종 검증](docs/33-final-verification.md)에서는 WEB01↔LB01 Ping 손실 0%, 웹·HAProxy HTTP 200, MariaDB 데이터, NAS01 RAID 1·LVM·NFS, 최근 백업, Prometheus 7/7 UP, Grafana 그래프 및 Alertmanager 연동을 캡처와 함께 정리했다. 이 결과는 각 점검 시점의 상태이며 장기 가용률을 의미하지 않는다.

## 7. 프로젝트 진행 현황

### 7.1 전체 진행률

| 구분 | 단원 수 |
|---|---:|
| 전체 계획 | 33 |
| 완료 | 33 |
| 예정 | 0 |

**현재 진행 단계: 33/33단원 완료(100%)**

진행률은 단원 개수를 기준으로 계산하며, 실제 작업 시간과 난이도에 따른 진행률을 의미하지는 않는다.

### 7.2 서버별 구축 현황

| 서버 | IP 주소 | 기본 구축 | 서비스 구축 |
|---|---|---|---|
| LB01 | 192.168.100.10 | 완료 | HAProxy Round Robin / Sticky Session 검증 완료 |
| WEB01 | 192.168.100.21 | 완료 | Apache/PHP 업무 포털 CRUD 완료 |
| WEB02 | 192.168.100.22 | 완료 | Apache/PHP 업무 포털 배포 및 CRUD 검증 완료 |
| DB01 | 192.168.100.31 | 완료 | MariaDB 완료 |
| NAS01 | 192.168.100.41 | 완료 | RAID 1/LVM/NFS 구성 및 웹 서버 연동 검증 완료 |
| MON01 | 192.168.100.51 | 완료 | Prometheus 3.13.5 / Node Exporter 1.12.1 / Grafana 13.3.0 / Alertmanager 0.34.1 구축 및 검증 완료 |

## 8. 실습 문서화 원칙

각 단원은 실제 수행한 작업과 검증된 결과를 중심으로 기록한다.

- 실습 목적 및 구성 환경
- 서버별 설정과 사용 명령어
- 주요 명령어 옵션 설명
- 실제 실행 결과 및 검증
- 장애 발생 시 원인 분석과 해결 과정
- 핵심 스크린샷 및 결과 정리
- 배운 점과 다음 실습

비밀번호, 인증 정보 및 기타 민감한 설정값은 공개 저장소에 포함하지 않는다.

## 9. 최종 통합 검증

[33단원](docs/33-final-verification.md)에서 네트워크, 웹·DB, RAID/LVM/NFS, 백업 무결성, Prometheus 7/7 UP, Grafana 그래프 및 Alertmanager 연동의 최종 상태를 기록하였다. 각 결과는 점검 시점의 캡처에 근거한다.
