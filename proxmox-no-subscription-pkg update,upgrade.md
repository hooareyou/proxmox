# Proxmox VE No-Subscription 환경에서 `apt update` 및 `apt upgrade` 설정 방법

Proxmox VE는 기본적으로 유료 구독용(enterprise) 저장소를 사용하도록 설정되어 있습니다. No-subscription 환경에서는 아래와 같이 설정을 변경해야 `apt update`, `apt upgrade`가 원활히 작동합니다.

---

## ✅ 1. Proxmox Enterprise 저장소 비활성화

```
nano /etc/apt/sources.list.d/pve-enterprise.list
```

내용을 모두 주석 처리하거나 삭제하세요:

```
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

---

## ✅ 2. No-Subscription 저장소 추가

아래 명령어로 `no-subscription` 저장소를 추가합니다.(Proxmox 8 기준)

```
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" | sudo tee /etc/apt/sources.list.d/pve-no-subscription.list
```

---

## ✅ 4. 시스템 업데이트 수행

```
apt update
apt upgrade -y
```
