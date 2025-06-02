# Proxmox Backup Server: 구독 없이 `apt update` 및 `apt upgrade` 하는 방법

Proxmox Backup Server(PBS)는 기본적으로 **기업용 리포지터리 (enterprise repository)** 를 사용하도록 설정되어 있습니다.  
그러나 유료 구독이 없는 경우에는 아래와 같은 오류가 발생할 수 있습니다:

```
W: http://enterprise.proxmox.com/debian/pbs bookworm InRelease: 인증되지 않은 리포지터리입니다.
E: 인증되지 않은 소스에서 소프트웨어를 받을 수 없습니다.
```

이 문서는 **구독 없이도 Proxmox Backup Server에서 `apt update`와 `apt upgrade`를 정상적으로 수행할 수 있도록 설정하는 방법**을 안내합니다.

---

## ✅ 1단계: Enterprise Repository 비활성화

Proxmox는 기본적으로 유료 구독 저장소를 사용하고 있습니다. 이 설정을 비활성화해야 합니다.

```
nano /etc/apt/sources.list.d/pbs-enterprise.list
```

아래와 같은 줄이 있다면:

```
deb https://enterprise.proxmox.com/debian/pbs bookworm pbs-enterprise
```

맨 앞에 `#`을 붙여 주석 처리합니다:

```
# deb https://enterprise.proxmox.com/debian/pbs bookworm pbs-enterprise
```

---

## ✅ 2단계: No-Subscription Repository 활성화

Proxmox에서는 무료 사용자들을 위한 저장소도 별도로 제공합니다.

```
nano /etc/apt/sources.list
```

아래 한 줄을 추가합니다 (PBS에서 사용하는 버전에 따라 `bookworm` 또는 해당 배포판 이름 사용):

```
deb http://download.proxmox.com/debian/pbs bookworm pbs-no-subscription
```

저장 후 종료합니다.

---

## ✅ 3단계: 패키지 목록 갱신 및 시스템 업그레이드

이제 다음 명령어로 업데이트 및 업그레이드를 실행할 수 있습니다.

```
apt update
apt upgrade -y
```

---

## ✅ 선택: GPG 서명 키 오류 해결

서명 키 관련 오류가 발생하는 경우 다음 명령으로 GPG 키를 수동으로 등록할 수 있습니다:

```
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg
```

---

## ✅ 설정 요약표

| 항목 | 설정 내용 |
|------|-----------|
| 기업용 리포지터리 | `/etc/apt/sources.list.d/pbs-enterprise.list`에서 주석 처리 |
| 무료 저장소 추가 | `/etc/apt/sources.list`에 `pbs-no-subscription` 저장소 추가 |
| GPG 키 오류 해결 | `proxmox-release-bookworm.gpg` 키 수동 등록 |
| 사용 명령어 | `apt update`, `apt upgrade -y`, `apt full-upgrade` |
