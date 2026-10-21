# Rocky Linux 9.8 기반 사내 업무 포털 인프라 구축 및 장애 복구

## 1. 프로젝트 소개

VMware Workstation의 Rocky Linux 9.8 가상 서버 6대로 사내 업무 포털 인프라를 구축하고, 서비스 연동·모니터링·백업 및 장애 복구를 검증하는 실습 프로젝트다.

## 2. 프로젝트 목표

- Linux 서버와 가상 네트워크 구축
- HAProxy를 통한 Apache 웹 서버 2대의 HTTP 로드밸런싱 및 장애 전환
- PHP 업무 포털과 MariaDB 연동
- RAID 1, LVM, NFS 기반 공유 스토리지 구성
- Prometheus와 Grafana를 통한 모니터링 및 알림
- 데이터 백업과 장애 복구 절차 검증

## 3. 실습 환경

- 가상화: VMware Workstation
- 운영체제: Rocky Linux 9.8
- 호스트: Intel Core i5-12450, RAM 16GB
- 실습 네트워크: `192.168.100.0/24`

## 4. 목표 아키텍처

![사내 업무 포털 목표 아키텍처: LB01, WEB01·WEB02, DB01, NAS01, MON01](diagrams/infrastructure-architecture.svg)

> 그림은 **목표 구성**이다. PHP, NAS 및 모니터링 등 아직 완료되지 않은 구성도 함께 표시되어 있다.

클라이언트 요청은 LB01의 HAProxy를 거쳐 WEB01과 WEB02로 분산된다. 두 웹 서버는 DB01의 MariaDB와 연동하고 NAS01의 NFS 공유 스토리지를 사용한다. MON01은 서비스 상태를 수집·시각화하고 장애를 알린다.

## 5. 서버 구성 및 진행 현황

| 서버 | IP 주소 | 역할 | 기본 구축 | 서비스 구축 |
|---|---|---|---|---|
| LB01 | 192.168.100.10 | HAProxy 로드밸런서 | 완료 | HAProxy 완료 |
| WEB01 | 192.168.100.21 | Apache/PHP 웹 서버 | 완료 | Apache 완료, PHP 예정 |
| WEB02 | 192.168.100.22 | Apache/PHP 웹 서버 | 완료 | Apache 완료, PHP 예정 |
| DB01 | 192.168.100.31 | MariaDB 데이터베이스 | 완료 | MariaDB 설치 완료, 포털 연동 예정 |
| NAS01 | 192.168.100.41 | RAID 1/LVM/NFS 공유 스토리지 | 예정 | 예정 |
| MON01 | 192.168.100.51 | Prometheus/Grafana 모니터링 | 예정 | 예정 |

## 6. 실습 문서 및 27단원 계획

완료한 단원은 문서 링크를 연결했다. 예정 단원은 실습과 문서 작성 후 링크와 상태를 갱신한다.

| 번호 | 단원 | 진행 상태 |
|---|---|---|
| 01 | [VMware 가상 네트워크 구축](docs/01-network.md) | 완료 |
| 02 | [WEB01 서버 구축 및 검증](docs/02-web01.md) | 완료 |
| 03 | [WEB02 서버 구축 및 검증](docs/03-web02.md) | 완료 |
| 04 | [LB01 서버 구축 및 검증](docs/04-lb01.md) | 완료 |
| 05 | [WEB01 Apache 웹 서버 구축 및 HTTP 검증](docs/05-web01-apache.md) | 완료 |
| 06 | [WEB02 Apache 웹 서버 구축 및 HTTP 검증](docs/06-web02-apache.md) | 완료 |
| 07 | [LB01 HAProxy 설치 및 로드밸런싱 구성](docs/07-haproxy.md) | 완료 |
| 08 | [HAProxy 장애 전환 및 복구 검증](docs/08-haproxy-failover.md) | 완료 |
| 09 | [DB01 서버 구축 및 MariaDB 설치](docs/09-mariadb.md) | 완료 |
| 10 | WEB01/WEB02 PHP 설치 및 MariaDB 연동 | 예정 |
| 11 | 사내 업무 포털 기능 구현 및 검증 | 예정 |
| 12 | NAS01 서버 구축 및 디스크 구성 | 예정 |
| 13 | NAS01 RAID 1 구성 및 장애 대응 검증 | 예정 |
| 14 | NAS01 LVM 볼륨 구성 및 확장 검증 | 예정 |
| 15 | NAS01 NFS 공유 구성 | 예정 |
| 16 | WEB01/WEB02 NFS 마운트 및 공유 파일 검증 | 예정 |
| 17 | MON01 서버 구축 | 예정 |
| 18 | Prometheus 및 서버 지표 수집 구성 | 예정 |
| 19 | Grafana 대시보드 구성 | 예정 |
| 20 | 모니터링 알림 구성 및 검증 | 예정 |
| 21 | 데이터 백업 정책 및 자동화 구성 | 예정 |
| 22 | MariaDB 백업 및 복구 검증 | 예정 |
| 23 | 포털 파일·설정 백업 및 복구 검증 | 예정 |
| 24 | 웹 서버 장애 전환 및 복구 재검증 | 예정 |
| 25 | DB·스토리지 장애 시나리오 및 복구 검증 | 예정 |
| 26 | 서비스 통합·보안·성능 점검 | 예정 |
| 27 | 프로젝트 최종 검증 및 결과 정리 | 예정 |

> 실습 순서와 문서 번호는 진행 과정에서 변경될 수 있다.

## 7. 장애 대응 및 검증

- [WEB01 → Windows Ping 장애 분석 및 복구](docs/02-web01.md#7-트러블슈팅--web01에서-windows로-ping-실패)
- [HAProxy 장애 전환 및 복구 검증](docs/08-haproxy-failover.md)
