# Proxmox VE

---

## 1. 소개

**Proxmox VE(Proxmox Virtual Environment)**는 오픈소스 기반의 엔터프라이즈급 서버 가상화 플랫폼입니다. KVM(커널 기반 가상 머신)과 LXC(리눅스 컨테이너)를 통합 관리하며, 웹 기반의 직관적인 인터페이스와 강력한 백업, 고가용성, 클러스터링, 소프트웨어 정의 스토리지 등 다양한 엔터프라이즈 기능을 제공합니다.

- **주요 특징**
  - KVM 가상 머신과 LXC 컨테이너 동시 지원
  - 고가용성(HA) 클러스터링 및 실시간 마이그레이션
  - ZFS, Ceph 등 소프트웨어 정의 스토리지(SDS) 지원
  - 강력한 백업/복구 및 스냅샷 기능
  - REST API 및 CLI 기반 자동화
- **라이선스**: 무료 사용(엔터프라이즈 지원은 별도)
- **활용 분야**: 데이터센터, 개발/테스트 환경, 사설 클라우드, 홈랩 등

---

## 2. 아키텍처 및 핵심 구성요소

### 2.1 KVM 기반 가상 머신(VM)
- 하드웨어 가상화 기술로 Windows, Linux 등 다양한 OS 지원
- CPU, RAM, 디스크, 네트워크 등 리소스 세부 설정 가능

### 2.2 LXC 컨테이너
- 리눅스 OS 레벨 가상화로, 경량화된 환경 제공
- 빠른 배포, 낮은 오버헤드, 효율적인 리소스 사용

### 2.3 스토리지 백엔드
- **로컬 스토리지**: 디스크, ZFS, LVM 등
- **공유 스토리지**: NFS, iSCSI, Ceph, GlusterFS 등
- **스토리지 풀**: 여러 디스크/스토리지 통합 관리

### 2.4 네트워크
- 브리지(Bridge), 본딩(Bonding), VLAN, SDN(Software Defined Network) 지원
- VM/컨테이너별 가상 NIC 연결 및 네트워크 격리 가능

### 2.5 클러스터링 및 고가용성(HA)
- 여러 노드를 하나의 클러스터로 묶어 통합 관리
- 장애 발생 시 자동으로 VM/컨테이너를 다른 노드로 이전(Failover)
- **Proxmox Cluster File System(pmxcfs)**: 클러스터 설정 동기화

---

## 3. 설치 및 초기 설정

### 3.1 하드웨어 및 시스템 요구사항

- 64비트 CPU (Intel VT-x/AMD-V 지원 필수)
- 최소 2GB RAM (실제 운영 환경에서는 8GB 이상 권장)
- 스토리지: SSD 권장, ZFS/RAID 사용 시 ECC RAM 권장
- 네트워크: 1GbE 이상(클러스터 구성 시 10GbE 권장)

### 3.2 설치 절차

1. [Proxmox VE 공식 홈페이지](https://www.proxmox.com/proxmox-ve)에서 ISO 이미지 다운로드
2. 부팅 USB/DVD로 서버 부팅
3. 설치 마법사에 따라 파티션, 네트워크, 관리자 비밀번호 등 설정
4. 설치 완료 후 서버 재부팅
5. 웹 브라우저에서 `https://<서버IP>:8006` 접속

### 3.3 네트워크 및 스토리지 초기 설정

- **네트워크**: `Datacenter > Node > System > Network`에서 브리지(vmbr0 등) 설정
- **스토리지**: `Datacenter > Storage`에서 로컬 디스크, ZFS 풀, NFS 마운트 등 추가

---

## 4. 관리 인터페이스

### 4.1 웹 UI
- 모든 관리 작업 가능(가상화, 네트워크, 스토리지, 백업 등)
- 실시간 리소스 모니터링, 이벤트 로그, 사용자 및 권한 관리

### 4.2 CLI(Command Line Interface)
- SSH 접속 또는 서버 콘솔에서 명령어로 세밀한 제어
- 주요 명령어: `pvecm`, `qm`, `pct`, `vzdump`, `pvesh` 등

### 4.3 REST API
- 외부 시스템 연동, 자동화 스크립트 등에 활용
- [API 문서](https://pve.proxmox.com/pve-docs/api-viewer/index.html)

---

## 5. 가상 머신(VM) 및 컨테이너(LXC) 관리

### 5.1 VM/컨테이너 생성

#### VM 생성 예시 (웹 UI)
1. `Create VM` 클릭
2. 이름, ISO 이미지, OS 종류, 디스크, CPU, 메모리, 네트워크 등 설정
3. 완료 후 VM 시작

#### LXC 컨테이너 생성 예시 (웹 UI)
1. `Create CT` 클릭
2. 이름, 템플릿, 디스크, CPU, 메모리, 네트워크 등 설정
3. 완료 후 컨테이너 시작

### 5.2 리소스 할당 및 확장

- CPU, 메모리, 디스크, 네트워크 리소스 동적 조정
- VM/컨테이너 실행 중에도 일부 리소스(메모리, 디스크) 확장 가능

### 5.3 마이그레이션 및 클러스터링

- **Live Migration**: VM/컨테이너를 중단 없이 다른 노드로 이동
- **오프라인 마이그레이션**: 중단 후 이동(스토리지 이동 포함 가능)
- **클러스터**: 여러 노드에서 가상화 자원 통합 관리

---

## 6. 스토리지 및 네트워크 구성

### 6.1 스토리지

- **ZFS**: 스냅샷, 복제, 데이터 무결성 보장, 풀(pool) 기반 관리
- **Ceph**: 분산형 오브젝트 스토리지, 대규모 환경에 적합
- **NFS/iSCSI**: 외부 스토리지 연동, 백업 및 공유 스토리지로 활용
- **스냅샷/백업**: VM/컨테이너의 특정 시점 상태 저장 및 복원

### 6.2 네트워크

- **브리지(Bridge)**: VM/컨테이너가 물리 네트워크에 직접 연결
- **VLAN**: 네트워크 분리 및 보안 강화
- **본딩(Bonding)**: 여러 NIC를 묶어 대역폭 증가 및 이중화
- **SDN**: 소프트웨어 정의 네트워크, 복잡한 네트워크 토폴로지 구현

---

## 7. 고가용성(HA) 및 클러스터링

### 7.1 클러스터 구성

- 여러 Proxmox 노드를 하나의 클러스터로 묶어, 자원 통합 및 관리
- `pvecm create <클러스터명>`으로 클러스터 생성
- `pvecm add <마스터노드IP>`로 노드 추가

### 7.2 HA 그룹 및 장애 조치

- HA 그룹에 VM/컨테이너 추가 시, 노드 장애 발생 시 자동으로 다른 노드에서 기동
- 펜싱(Fencing) 기능으로 장애 노드 격리

### 7.3 Proxmox Cluster File System(pmxcfs)

- 클러스터 전체의 설정 파일 동기화 및 일관성 유지

---

## 8. 백업 및 복구

### 8.1 내장 백업 도구(vzdump)

- VM/컨테이너 단위로 백업 파일(.vma, .tar.lzo 등) 생성
- 스냅샷, 스톱, 온라인(실행 중) 등 다양한 백업 모드 지원

### 8.2 Proxmox Backup Server(PBS) 연동

- 증분 백업, 중복 제거, 암호화 지원
- 장기 보관 및 효율적인 스토리지 사용

### 8.3 스케줄링 및 자동화

- 정기 백업 작업 예약
- 백업 정책, 보존 주기 설정

---

## 9. 실전 활용 예시 및 베스트 프랙티스

- **엔터프라이즈 환경**: 고가용성 클러스터, Ceph/ZFS 스토리지, 자동화 백업
- **개발/테스트 환경**: 빠른 VM/컨테이너 배포, 스냅샷/복구 활용
- **홈랩**: 저렴한 하드웨어로 사설 클라우드 구축, 다양한 OS 실습

### 베스트 프랙티스
- ZFS/Ceph 사용 시 ECC RAM 사용 권장
- 정기적인 백업 및 복구 테스트
- 네트워크 및 스토리지 이중화로 장애 대비
- 보안 업데이트 및 패치 주기적 적용

---

## 10. 보안 및 유지관리

### 10.1 사용자 및 권한 관리

- 역할 기반 접근 제어(RBAC)로 세분화된 권한 부여
- 2단계 인증(TOTP) 지원

### 10.2 시스템 모니터링 및 로그 관리

- 실시간 리소스 사용량, 이벤트, 알림 대시보드 제공
- `/var/log/pve/`, `/var/log/syslog` 등에서 상세 로그 확인

### 10.3 업데이트 및 패치 관리

- 웹 UI 또는 CLI에서 패키지 업데이트 가능
- 엔터프라이즈 저장소(유료) 또는 커뮤니티 저장소(무료) 선택 가능

---

## 11. 트러블슈팅 및 참고 자료

### 11.1 주요 문제와 해결 방법

- VM 부팅 실패: ISO 이미지, 디스크 설정, BIOS/UEFI 옵션 확인
- 스토리지 마운트 오류: 네트워크/스토리지 연결 상태, 권한 확인
- 클러스터 통신 문제: 네트워크 방화벽, 포트(8006, 22, 5404/5405 등) 확인

### 11.2 공식 문서 및 커뮤니티

- [Proxmox 공식 문서](https://pve.proxmox.com/pve-docs/)
- [Proxmox 포럼](https://forum.proxmox.com/)
- [Proxmox GitHub](https://github.com/proxmox)

---

## Proxmox CLI 주요 명령어 및 예시

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `pvecm` | 클러스터 관리 | `pvecm status` (클러스터 상태 확인) <br> `pvecm create mycluster` (클러스터 생성) <br> `pvecm add 192.168.1.10` (노드 추가) |
| `qm` | KVM 가상 머신 관리 | `qm create 100 --name test-vm --memory 2048 --net0 virtio,bridge=vmbr0` (VM 생성) <br> `qm start 100` (VM 시작) <br> `qm stop 100` (VM 중지) <br> `qm migrate 100 target-node` (VM 마이그레이션) |
| `pct` | LXC 컨테이너 관리 | `pct create 101 local:vztmpl/debian-11-standard_11.0-1_amd64.tar.gz --cores 2 --memory 1024` (컨테이너 생성) <br> `pct start 101` (컨테이너 시작) <br> `pct stop 101` (컨테이너 중지) <br> `pct migrate 101 target-node` (컨테이너 마이그레이션) |
| `pvesh` | REST API 호출 | `pvesh get /nodes` (노드 정보 조회) <br> `pvesh get /nodes/<node>/qemu` (해당 노드의 VM 목록) |
| `vzdump` | 백업 및 복구 | `vzdump 100 --storage backup --mode snapshot` (VM 백업) <br> `vzdump 101 --dumpdir /backup` (컨테이너 백업) |
| `pveam` | 템플릿/이미지 관리 | `pveam available` (다운로드 가능한 템플릿 목록) <br> `pveam download local debian-11-standard_11.0-1_amd64.tar.gz` (템플릿 다운로드) |

### CLI 명령어 상세 예시
```
# 클러스터 상태 확인
pvecm status

# 클러스터 생성
pvecm create mycluster

# 클러스터에 노드 추가
pvecm add 192.168.1.10

# VM 생성 (ID: 100, 이름: test-vm, 메모리: 2GB, 네트워크: vmbr0)
qm create 100 --name test-vm --memory 2048 --net0 virtio,bridge=vmbr0

# VM 시작/중지/삭제
qm start 100
qm stop 100
qm destroy 100

# VM 마이그레이션 (target-node로 이동)
qm migrate 100 target-node

# LXC 컨테이너 생성 (ID: 101, 템플릿: Debian 11, CPU 2개, 메모리 1GB)
pct create 101 local:vztmpl/debian-11-standard_11.0-1_amd64.tar.gz --cores 2 --memory 1024

# 컨테이너 시작/중지/삭제
pct start 101
pct stop 101
pct destroy 101

# 컨테이너 마이그레이션 (target-node로 이동)
pct migrate 101 target-node

# VM 백업 (snapshot 모드, backup 스토리지 사용)
vzdump 100 --storage backup --mode snapshot

# 템플릿 목록 확인 및 다운로드
pveam available
pveam download local debian-11-standard_11.0-1_amd64.tar.gz

# REST API로 노드 목록 조회
pvesh get /nodes
```
