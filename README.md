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
| 31 | NFS 데이터 백업 및 복구 | 예정 |
| 32 | 통합 장애 시나리오 및 서비스 복구 | 예정 |

### PHASE 9. 최종 포트폴리오

| 번호 | 실습 주제 | 상태 |
|---|---|---|
| 33 | 전체 통합 검증 및 GitHub·PPT 최종 결과 정리 | 예정 |

> 실습 완료 후 문서와 증거 스크린샷을 업로드하고 진행 상태를 갱신한다. 예정 단원의 링크는 실제 문서를 작성한 후 추가한다.

## 6. 주요 장애 대응 및 검증

현재까지 수행한 장애 분석 및 검증 기록:

- [WEB01 → Windows Ping 장애 분석 및 복구](docs/02-web01.md#7-트러블슈팅--web01에서-windows로-ping-실패)
- [HAProxy 장애 전환 및 복구 검증](docs/08-haproxy-failover.md)
- [MariaDB 원격 접속 및 최소 권한 검증](docs/09-mariadb.md)
- [WEB02 PHP → MariaDB 인증 오류 분석 및 복구](docs/10-php-mariadb.md#9-web02-php--mariadb-연동-오류-분석-및-복구)
- [HAProxy PHP Round Robin 및 공통 DB 조회 검증](docs/10-php-mariadb.md#10-haproxy-통합-연동-검증)
- [HAProxy Sticky Session 및 공통 DB CRUD 통합 검증](docs/11-php-portal-crud.md#13-haproxy-sticky-session-구성-및-검증)

10단원에서는 WEB02의 HTTP 500 오류를 PHP-FPM 로그에서 확인하고, MariaDB ERROR 1045 인증 문제를 분석하여 복구하였다.

또한 HAProxy를 통해 WEB01과 WEB02가 번갈아 응답하면서 DB01의 동일한 데이터를 조회하는 것을 확인하였다.

검증 후 임시 PHP 테스트 파일을 제거하고 두 웹 서버에서 HTTP 404 응답을 확인하였다.

11단원에서는 PHP CRUD 예제 애플리케이션을 WEB01/WEB02에 배포하고 공통 DB 연동을 검증하였다. HAProxy에서 쿠키 기반 Sticky Session을 구성한 뒤 CRUD 작업과 HTTP 404/200/200 응답을 확인하였다. 단, 웹 서버 장애 시 PHP 세션 자동 복구는 검증 범위에 포함하지 않았다.

[12단원](docs/12-rsync-deployment.md)에서는 테스트 디렉터리의 최초·증분 rsync 동기화, 실제 포털 파일의 스테이징 복사와 백업·권한·SELinux 확인, WEB02 및 HAProxy 경유 HTTP 응답을 검증하였다. 스테이징 파일과 운영 파일이 동일해 운영 포털 파일은 덮어쓰지 않았다.

[14단원](docs/14-raid.md)에서는 RAID 1 디스크 장애 및 복구를 검증하였다. [15단원](docs/15-lvm.md)에서는 LVM 용량 확장을, [16단원](docs/16-nfs-server.md)에서는 NAS01의 NFS 서버 설정과 재부팅 후 유지를 확인하였다. [17단원](docs/17-nfs-client.md)에서는 두 웹 서버의 NFSv4.2 마운트, 서로 다른 UID의 그룹 쓰기, 재부팅 후 자동 마운트 및 Apache HTTP 연동을 검증하였다.

[18단원](docs/18-ssh-hardening.md)에서는 SSH 설정을 강화하고 신규 관리자 접속을 확인하였다. [19단원](docs/19-firewall-selinux.md)에서는 firewalld 허용 규칙과 SELinux Enforcing 상태의 Apache·NFS 연동을 점검하였다. [20단원](docs/20-account-service-hardening.md)에서는 계정·서비스 상태를 확인하고 사용하지 않는 서비스 설정을 정리하였다. [21단원](docs/21-security-updates.md)에서는 보안 업데이트, WEB02의 Python 패키지 의존성 충돌 해결 및 재부팅 후 서비스 동작을 기록하였다.

[22단원](docs/22-process-resource.md)에서는 WEB01의 프로세스·리소스와 Apache 상태를 점검하고, CPU 부하 종료 후 HTTP 200 응답을 확인하였다. [23단원](docs/23-log-management.md)에서는 시스템·Apache 로그를 분석하고 의도적인 HTTP 404 요청을 Access 로그에서 추적한 뒤 HTTP 200 정상 응답을 확인하였다. [24단원](docs/24-prometheus.md)에서는 MON01의 Prometheus 자체 메트릭 수집을 확인했고, [25단원](docs/25-node-exporter.md)에서는 6대 서버의 Node Exporter를 연동하여 전체 7 / 7 UP을 검증하였다. [26단원](docs/26-grafana.md)에서는 Grafana로 6대 서버의 CPU·메모리·디스크 대시보드를 구성하였다. [27단원](docs/27-alertmanager.md)에서는 WEB01 Node Exporter의 장애 발생과 복구 알림을 로컬 Webhook으로 검증하였다.

[28단원](docs/28-file-backup.md)에서는 WEB01 파일의 rsync 백업·별도 경로 복원을, [29단원](docs/29-cron-backup.md)에서는 SSH 키 인증과 cron 자동 백업 실행을 검증하였다. [30단원](docs/30-mariadb-backup.md)에서는 MariaDB 논리 백업, 별도 데이터베이스 복원, 원본·복원본 데이터 비교 및 NAS01 전송 파일의 SHA-256 무결성을 검증하였다.

향후 NFS 데이터 백업·복구와 통합 서비스 장애 복구를 추가 검증할 예정이다.

## 7. 프로젝트 진행 현황

### 7.1 전체 진행률

| 구분 | 단원 수 |
|---|---:|
| 전체 계획 | 33 |
| 완료 | 30 |
| 예정 | 3 |

**현재 진행 단계: 30/33단원 완료 · 3/33단원 예정 / 다음: 31단원 NFS 데이터 백업 및 복구**

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

## 9. 다음 실습

**31단원: NFS 데이터 백업 및 복구**

28단원에서 WEB01 파일 백업·복원을, 29단원에서 cron 자동 백업을, 30단원에서 MariaDB SQL 백업·복원과 NAS01 보관 및 SHA-256 무결성 검증을 완료하였다. 다음 단원에서는 NAS01의 NFS 공유 데이터를 안전하게 백업하고 별도 경로에 복원하여 검증할 예정이다.
