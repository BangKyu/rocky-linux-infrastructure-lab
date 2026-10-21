# Rocky Linux 9.8 기반 사내 업무 포털 인프라 구축 및 장애 복구

## 1. 프로젝트 소개
VMware Workstation 환경에서 Rocky Linux
9.8 기반 사내 업무 포털 인프라 구축

## 2. 프로젝트 목표
- Linux 서버 및 네트워크 구축
- Apache/PHP 사내 업무 포털
- MariaDB 데이터베이스 연동
- NFS 및 LVM 스토리지 관리
- HAProxy 웹 서버 이중화
- Prometheus/Grafana 모니터링
- Bash 백업 및 장애 복구

## 3. 실습 환경
VMware Workstation / Rocky Linux 9.8
i5-12450 / RAM 16GB

## 4. 서버 구성
LB01, WEB01, WEB02, DB01, NAS01, MON01

## 5. 실습 문서

본 프로젝트는 Rocky Linux 9.8 환경에서 사내 업무 포털 인프라를 구축하고, 서비스 연동 및 장애 복구까지 검증하는 것을 목표로 한다.

실습 완료 후 해당 문서를 작성하고 GitHub에 업로드하며, 진행 상태를 갱신한다.

| 번호 | 문서 | 진행 상태 |
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
| 10 | WEB01/WEB02 PHP 설치 및 DB 연동 | 예정 |
| 11 | 사내 업무 포털 기능 구현 및 검증 | 예정 |
| 12 | NAS01 서버 구축 및 LVM 스토리지 구성 | 예정 |
| 13 | NFS 공유 스토리지 구축 및 웹 서버 연동 | 예정 |
| 14 | MON01 서버 구축 및 모니터링 환경 구성 | 예정 |
| 15 | 데이터 백업 및 복구 실습 | 예정 |
| 16 | 통합 장애 시나리오 및 서비스 복구 검증 | 예정 |
| 17 | 프로젝트 최종 검증 및 결과 정리 | 예정 |

> 실습 순서와 문서 번호는 진행 과정에서 변경될 수 있다. 문서 링크는 실습 완료 및 문서 작성 후 추가한다.

## 6. 장애 대응 및 검증
- [WEB01 → Windows Ping 장애 분석 및 복구](docs/02-web01.md#7-트러블슈팅--web01에서-windows로-ping-실패)
- [HAProxy 장애 전환 및 복구 검증](docs/08-haproxy-failover.md)

## 7. 프로젝트 진행 현황

| 서버 | IP 주소 | 기본 구축 | 서비스 구축 |
|---|---|---|---|
| LB01 | 192.168.100.10 | 완료 | HAProxy 완료 |
| WEB01 | 192.168.100.21 | 완료 | Apache 완료 / PHP 예정 |
| WEB02 | 192.168.100.22 | 완료 | Apache 완료 / PHP 예정 |
| DB01 | 192.168.100.31 | 예정 | MariaDB 예정 |
| NAS01 | 192.168.100.41 | 예정 | NFS/LVM 예정 |
| MON01 | 192.168.100.51 | 예정 | 모니터링 예정 |
