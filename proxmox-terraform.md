
# 🛠 Proxmox VE와 Terraform을 활용한 VM 자동화 배포 실전 가이드

---

## 1. Terraform이란?

**Terraform**은 HashiCorp에서 개발한 **IaC(Infrastructure as Code, 코드 기반 인프라 관리)** 오픈소스 도구입니다. 복잡한 인프라를 코드로 선언하고 관리함으로써, 수작업을 줄이고 일관된 환경 구성을 가능하게 합니다.

### ✅ Terraform의 주요 특징

| 기능 | 설명 |
|------|------|
| **선언형 구성 방식** | 인프라의 *목표 상태(desired state)*를 정의하면, Terraform이 현재 상태와 비교하여 필요한 작업을 수행 |
| **멀티 클라우드 및 멀티 플랫폼 지원** | AWS, Azure, GCP, VMware, Proxmox 등 다양한 인프라 제공자를 지원 |
| **상태 관리(state file)** | 인프라 자원의 상태를 `.tfstate` 파일로 관리하여 변경 사항을 추적하고 자동화에 활용 |
| **의존성 자동 처리** | 자원 간의 의존 관계를 분석하여 올바른 순서로 자원을 생성/변경/삭제 |
| **모듈화** | 재사용 가능한 코드 블록을 만들 수 있어, 규모가 큰 인프라도 관리가 용이 |
| **플러그인 기반 확장성** | Provider와 Module을 통해 사용자 정의 인프라 관리가 가능 |

### ✅ Terraform이 필요한 이유
- VM 수십 대 이상을 수작업으로 생성/관리하는 것은 비효율적이고 오류 가능성이 큼
- 코드로 관리하면 버전 관리(Git 등), 협업, 자동화 배포에 유리
- 테스트 환경, QA 환경, 운영 환경을 동일하게 구성할 수 있음(DevOps 기반)

---

## 2. Terraform 주요 개념
| 개념 | 설명 | 예시 |
|------|------|------|
| **Provider** | Terraform과 외부 인프라 시스템(Proxmox, AWS 등)을 연결하는 플러그인. | `telmate/proxmox` |
| **Resource** | 실제로 생성되는 인프라 자원. | VM, 볼륨, 네트워크 등 |
| **Variable** | 코드에 유연성을 부여하는 입력값. | VM 이름, IP 주소, VMID 등 |
| **Output** | 실행 후 결과로 출력할 변수. | VM의 IP, VM ID 등 |
| **Module** | 여러 자원을 묶어 재사용 가능한 구조로 만든 코드 블록. | VM을 정의한 모듈 |
| **State File** | Terraform이 인프라의 현재 상태를 저장하는 파일. | `terraform.tfstate` |
| **Data Source** | 외부 인프라 정보를 읽어와 리소스에 활용. | 이미 존재하는 템플릿 VM의 정보 불러오기 |
| **Plan** | 코드와 실제 상태 간의 차이를 분석하고 출력. | `terraform plan` |
| **Apply** | 계획대로 인프라 자원을 생성하거나 수정. | `terraform apply` |
| **Destroy** | 생성한 자원을 삭제. | `terraform destroy` |

---

## 3. Proxmox + Terraform 구성도(예시)

```
Terraform 구성(vm 설치를 위한 구성)
│
├── main.tf               # Provider 설정
├── variables.tf          # 전역 변수 정의
├── terraform.tfvars      # 민감한 값 입력
├── generated_vms.tf      # 여러 VM 선언
└── modules/
    └── vm/
        ├── main.tf       # VM 리소스 정의
        ├── variables.tf  # VM 변수 정의
        └── output.tf     # 결과 출력
```

---

## 4. 실행 흐름

1. `terraform init` → Terraform 초기화 및 provider 설치  
2. `terraform plan` → 인프라 변경 사항 미리보기 
3. `terraform apply` → 실제로 VM 생성 및 적용
4. `terraform destroy` → 생성한 VM 및 자원 삭제

---

## 5. 실전 코드 구성 예시

### ✅ main.tf

Terraform이 Proxmox VE API와 통신하기 위한 Provider 설정을 정의하는 파일

```
terraform {
  required_providers {
    proxmox = {
      source  = "telmate/proxmox"  # Proxmox용 Terraform provider 사용
      version = "3.0.1-rc6"        # 사용할 provider 버전 명시
    }
  }
}

provider "proxmox" {
  pm_api_url          = var.pm_api_url           # Proxmox API 주소 (예: https://<ip>:8006/api2/json)
  pm_api_token_id     = var.pm_api_token_id      # Proxmox 사용자 이름과 토큰 ID 조합
  pm_api_token_secret = var.pm_api_token_secret  # API 토큰의 비밀 키
  pm_tls_insecure     = var.pm_tls_insecure      # SSL 인증서 무시 여부 (테스트 환경에서 true)
}
```

### ✅ terraform.tfvars

Terraform에서 사용할 실제 변수 값을 정의하는 파일, Proxmox와 연동하려면 **Proxmox VE에서 사전 설정**이 필요

#### 🔧 Proxmox 설정 사전 준비

1. Proxmox VE 웹 UI 접속
2. `Datacenter → Permissions → API Tokens` 이동
3. 사용자(예: `terraform@pve`) 선택 후, `Add` 클릭
4. **Token ID** 및 **Secret** 생성
5. 권한 부여 (예: `VM.Allocate`, `VM.Config.*`, `Datastore.AllocateSpace` 등)

#### 예시 내용:
```
pm_api_url          = "https://124.111.181.186:8008/api2/json"  # Proxmox API URL
pm_api_token_id     = "terraform@pve!admin"                     # 생성한 토큰 ID
pm_api_token_secret = "토큰 비밀값"                              # 생성된 비밀 키
pm_tls_insecure     = true                                      # 테스트 인증서 무시 (운영환경에서는 false 권장)
```

### ✅ generated_vms.tf
```
module "vm01" {
  source    = "./modules/vm"
  providers = { proxmox = proxmox }
  vm_name    = "vm01"
  ip_address = "10.103.0.4"
  vmid       = 301
}
```

### ✅ modules/vm/main.tf (VM 정의)
```
resource "proxmox_vm_qemu" "cloudinit_vm" {
  name        = var.vm_name
  vmid        = var.vmid
  clone       = var.template
  cicustom    = "user=local:snippets/${var.vm_name}-user-data.yml"
  ipconfig0   = "ip=${var.ip_address}/8,gw=10.0.0.1"
  ...
}
```

---

### ✅ cloud-init 설정 (/var/lib/vz/snippets, proxmox host에 위치)
```
#cloud-config
hostname: vm01 # vm을 설치하면서 host의 name을 설정하는 내용
users:
  - name: app
    ssh-authorized-keys:
      - ssh-rsa ... # terraform이 설치되고 실행되는 vm의 ssh public key 정보
runcmd:
  - apt-get install -y qemu-guest-agent      # vm을 설치하면서 qemu-guest-agent을 설치하는 내용
```

---

## 6. 주요 명령어 요약

| 명령어 | 설명 |
|--------|------|
| terraform init | 초기화 및 provider 설치 |
| terraform plan | 변경 예정 사항 미리 보기 |
| terraform apply | 변경 사항 생성 적용 |
| terraform destroy | 생성된 자원 삭제 |
| terraform fmt | 코드 정리 |
| terraform output | 결과값 출력(-o json 옵션으로 json 형식으로 저장 가능 |

---

## 7. 실전 활용 팁

| 항목 | 팁 |
|------|----|
| **cloud-init 활용** | 사용자 설정 자동화 (유저, 패키지 설치, SSH 등) |
| **템플릿 사전 준비** | `ubuntu-cloudinit` 템플릿 VM 생성 필요 |
| **SSH 키 관리** | 모든 VM에서 동일한 키 또는 외부 파일 불러오기 |
| **모듈화 구성** | 다양한 VM을 손쉽게 관리 |
| **보안 강화** | `.tfvars` 파일은 Git에 포함하지 말 것 (`.gitignore`) |

---
