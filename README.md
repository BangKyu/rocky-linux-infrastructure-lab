# Rocky Linux 9.8 기반 사내 업무 포털
인프라 구축 및 장애 복구

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

| 번호 | 문서 | 진행 상태 |
|---|---|---|
| 01 | [VMware 가상 네트워크 구축](docs/01-network.md) | 완료 |
| 02 | [WEB01 서버 구축 및 검증](docs/02-web01.md) | 완료 |
| 03 | [WEB02 서버 구축 및 검증](docs/03-web02.md) | 완료 |
| 04 | [LB01 서버 구축 및 검증](docs/04-lb01.md) | 완료 |
| 05 | [WEB01 Apache 웹 서버 구축 및 HTTP 검증](docs/05-web01-apache.md) | 완료 |
| 06 | [WEB02 Apache 웹 서버 구축 및 HTTP 검증](docs/06-web02-apache.md) | 완료 |

## 6. 장애 대응 및 검증
- [WEB01 → Windows Ping 장애 분석 및 복구](docs/02-web01.md#7-트러블슈팅--web01에서-windows로-ping-실패)
## 7. 프로젝트 진행 현황

| 서버 | IP 주소 | 기본 구축 | 서비스 구축 |
|---|---|---|---|
| LB01 | 192.168.100.10 | 완료 | HAProxy 예정 |
| WEB01 | 192.168.100.21 | 완료 | Apache/PHP 예정 |
| WEB02 | 192.168.100.22 | 완료 | Apache/PHP 예정 |
| DB01 | 192.168.100.31 | 예정 | MariaDB 예정 |
| NAS01 | 192.168.100.41 | 예정 | NFS/LVM 예정 |
| MON01 | 192.168.100.51 | 예정 | 모니터링 예정 |
