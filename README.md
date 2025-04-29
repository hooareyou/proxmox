# Proxmox VE 및 Backup Server

## 1. Proxmox VE 개념
- Proxmox VE(Proxmox Virtual Environment)는 오픈소스 서버 가상화 플랫폼
- KVM(커널 기반 가상 머신)과 LXC(리눅스 컨테이너)를 통합하여 하나의 환경에서 효율적으로 가상 서버를 운영할 수 있음
- 사용자 친화적인 웹 GUI를 통해 가상 머신(VM), 컨테이너, 스토리지, 네트워크 설정을 손쉽게 관리할 수 있음

**주요 특징:**
- 웹 기반 관리 인터페이스 (포트 8006)
- 고가용성(HA) 클러스터 지원
- 라이브 마이그레이션
- 스냅샷 및 백업 기능
- 다양한 스토리지 지원(ZFS, LVM, NFS, Ceph 등)
- 예시: 여러 개의 리눅스 및 윈도우 서버를 단일 Proxmox 서버 안에서 VM 형태로 운용 가능하며, 각각에 별도의 자원(CPU, RAM, 디스크 등)을 할당할 수 있음

## 2. Proxmox VE 설치 권장 사양
| 항목 | 최소 사양 | 권장 사양 |
|------|------------|-------------|
| CPU | VT-x / AMD-V 지원 64비트 CPU | 다중 코어 Xeon/EPYC 시리즈 |
| 메모리 | 4GB | 16GB 이상 (VM 개수에 따라 증가) |
| 디스크 | 32GB SSD | 250GB SSD 이상 + RAID 구성 권장 |
| 네트워크 | 1Gbps NIC | 2개 이상의 NIC (클러스터 및 백업 분리) |

**주의:** ZFS 사용 시 메모리 소모가 크므로 최소 16GB RAM을 권장

## 3. Proxmox 설치 방법 (Rufus 포함)

### 1) 설치 ISO 다운로드
- 공식 사이트: https://www.proxmox.com/en/downloads

### 2) 부팅 USB 만들기 (Rufus 사용)
- Rufus(https://rufus.ie) 실행
- USB 장치 선택
- 다운로드한 Proxmox ISO 파일 선택
- 파티션 방식: GPT (UEFI 시스템일 경우)
- 파일 시스템: FAT32
- [시작] 클릭하여 USB 생성

### 3) 서버에 Proxmox 설치
- USB 삽입 후 부팅
- "Install Proxmox VE" 선택
- 설치 디스크 선택, 국가 및 시간대 설정
- 관리자 비밀번호 및 이메일 입력
- 네트워크 설정
- 설치 완료 후 재부팅 및 웹 인터페이스 접속

## 4. 주요 기능 및 DataCenter 관리
### 1) 주요 기능
- VM, LXC 컨테이너 생성 및 관리
- ISO 및 백업 이미지 업로드 및 사용
- 브라우저 기반 실시간 콘솔(VNC, SPICE)
- 고가용성 클러스터(HA) 구성 가능
- 라이브 마이그레이션 지원
- 스냅샷 및 백업 기능
- 역할 기반 접근 제어(RBAC)
- 방화벽 및 네트워크 가상화(VLAN, Linux Bridge)

## 5. Proxmox Backup Server 개념
### Proxmox Backup Server(PBS)는 Proxmox VE 환경에서 VM, CT, 파일의 백업 및 복구를 고속으로 처리할 수 있도록 최적화된 오픈소스 백업 솔루션
### 1) 주요 특징:
- 증분 백업 지원 → 빠르고 저장 공간 절약
- 중복 제거 및 데이터 압축
- SHA256 기반 무결성 검사
- 클라이언트 인증 및 암호화 지원
- CLI, 웹 인터페이스 지원

## 6. Proxmox Backup Server Datastore 관리

## 7. Proxmox VM Backup 및 Restore 방법 (CLI 기준)

## 8. 백업 스케줄 설정 방법

## 9. Proxmox 클러스터 구성

## 10. Proxmox 설치 후 기본 스토리지 구성

## 11. VM과 CT 개념 및 차이점
