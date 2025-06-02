
# Proxmox: No-subscription 팝업 제거 방법(Proxmox VE, Proxmox backup server 공통)

Proxmox VE에서는 유료 서브스크립션이 없는 경우, 로그인 시 `No valid subscription` 팝업이 표시됩니다. 기능 제한은 없지만, 이 팝업을 비활성화하려면 웹 인터페이스의 JavaScript 파일을 수정해야 합니다.

---

## 🛠️ 팝업 제거 단계

### 1. SSH로 Proxmox 서버 접속
```
ssh root@your-proxmox-ip
```

### 2. JavaScript 파일 백업
```
cp /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js.bak
```

### 3. 파일 열기
```
nano /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js
```

### 4. 팝업 관련 코드 수정

다음과 같은 코드 블록을 찾습니다.

```
if (data.status === 'Active') {
    ...
} else {
    Ext.Msg.show({
        title: gettext('No valid subscription'),
        icon: Ext.Msg.WARNING,
        message: Proxmox.Utils.getNoSubKeyHtml(res.data.url),
        buttons: Ext.Msg.OK,
    });
}
```

이를 다음과 같이 변경하거나 삭제합니다. Ext.Msg.show -> void({ //Ext.Msg.show

```
if (data.status === 'Active') {
    ...
} else {
    void({ //Ext.Msg.show({
        title: gettext('No valid subscription'),
        icon: Ext.Msg.WARNING,
        message: Proxmox.Utils.getNoSubKeyHtml(res.data.url),
        buttons: Ext.Msg.OK,
    });
}

```

### 5. pveproxy 재시작 및 캐시 정리

#### 5.1 proxmox ve
```
systemctl restart pveproxy
```

#### 5.2 proxmox backup server
```
systemctl restart proxmox-backup-proxy.service
```
