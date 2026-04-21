---
description: "Born2beRoot 에서 수정 또는 확인해야 하는 설정 파일의 스펙·필드·기본값."
---

# 📚 Reference — 설정 파일 스펙

> **문서 유형**: Reference. 각 파일의 경로·필드·기본값만 기록. "왜" 는 [03-explanation/](../03-explanation/) 참조.

## 목차

| 파일 | 섹션 |
|---|---|
| `/etc/ssh/sshd_config` | [SSH](#etc-ssh-sshd-config) |
| `/etc/default/ufw`, `/etc/ufw/` | [UFW](#etc-ufw) |
| `/etc/sudoers`, `/etc/sudoers.d/` | [sudo](#etc-sudoers) |
| `/etc/pam.d/common-password` | [PAM](#etc-pam-d-common-password) |
| `/etc/login.defs` | [login.defs](#etc-login-defs) |
| `/etc/crontab`, `/var/spool/cron/crontabs/` | [cron](#etc-crontab) |
| `/etc/apparmor.d/` | [AppArmor](#etc-apparmor-d) |
| `/etc/crypttab` | [LUKS](#etc-crypttab) |
| `/etc/fstab` | [fstab](#etc-fstab) |

---

## <a id="etc-ssh-sshd-config"></a>/etc/ssh/sshd_config

| 필드 | Born2beRoot 값 | 기본값 | 의미 |
|---|---|---|---|
| `Port` | `4242` | `22` | SSH 데몬 리스닝 포트 |
| `PermitRootLogin` | `no` | `prohibit-password` | root 계정 SSH 접속 금지 |
| `PasswordAuthentication` | `yes` (또는 `no` + 키) | `yes` | 비번 인증 허용 여부 |
| `LoginGraceTime` | 기본 유지 | `2m` | 로그인 대기 시간 |
| `MaxAuthTries` | 기본 유지 | `6` | 인증 재시도 제한 |

**변경 후**:
```bash
sudo sshd -t                # 문법 검증
sudo systemctl reload ssh   # 재적용
```

**원문 참고**: `man 5 sshd_config`.

---

## <a id="etc-ufw"></a>UFW 구성

| 파일 | 역할 |
|---|---|
| `/etc/default/ufw` | UFW 전역 기본값 (IPv6, LOGLEVEL, DEFAULT_\*_POLICY) |
| `/etc/ufw/before.rules`, `after.rules` | iptables 룰 전/후 hook |
| `/etc/ufw/user.rules` | `ufw allow/deny` 로 추가된 룰 저장 위치 |
| `/var/lib/ufw/user.rules` | (구 버전) 동일 역할 |

**주요 기본 정책** (`/etc/default/ufw`):
```
DEFAULT_INPUT_POLICY="DROP"
DEFAULT_OUTPUT_POLICY="ACCEPT"
DEFAULT_FORWARD_POLICY="DROP"
```

**주요 명령**:
```bash
ufw enable                   # 활성 + systemd enable
ufw status verbose           # 전체 상태
ufw status numbered          # 룰 번호 함께 출력
ufw allow 4242/tcp
ufw delete allow 4242/tcp    # 또는 ufw delete <N>
ufw reset                    # 전체 초기화
```

---

## <a id="etc-sudoers"></a>/etc/sudoers · /etc/sudoers.d/

| 파일 | 권장 |
|---|---|
| `/etc/sudoers` | 원본 유지 (`@includedir /etc/sudoers.d` 만 확인) |
| `/etc/sudoers.d/born2beroot` | **신규 파일** 에 Born2beRoot 전용 Defaults 기록 |

**편집 명령**: `sudo visudo -f /etc/sudoers.d/born2beroot` (직접 편집 금지 — 문법 오류 시 sudo 가 잠김).

**Born2beRoot Defaults (예시)**:
```
Defaults    passwd_tries=3
Defaults    badpass_message="WRONG PASSWORD. Retry."
Defaults    logfile="/var/log/sudo/sudo.log"
Defaults    log_input, log_output
Defaults    iolog_dir="/var/log/sudo"
Defaults    requiretty
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

**`/var/log/sudo/` 디렉토리 사전 생성**:
```bash
sudo mkdir -p /var/log/sudo
sudo chmod 750 /var/log/sudo
```

---

## <a id="etc-pam-d-common-password"></a>/etc/pam.d/common-password

비밀번호 변경 시 검사 스택. `pam_pwquality.so` 라인 하나에 옵션 모두 붙음.

**Born2beRoot 목표 라인**:
```
password  requisite  pam_pwquality.so  retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

| 옵션 | 의미 |
|---|---|
| `retry=3` | 실패 시 재시도 횟수 |
| `minlen=10` | 최소 길이 10 자 |
| `ucredit=-1` | 대문자 최소 1 자 (음수 = 강제, 양수 = 가산점) |
| `lcredit=-1` | 소문자 최소 1 자 |
| `dcredit=-1` | 숫자 최소 1 자 |
| `maxrepeat=3` | 같은 문자 연속 3 초과 금지 |
| `reject_username` | 비번에 사용자명 포함 금지 |
| `difok=7` | 이전 비번 대비 7 자 이상 달라야 함 (root 예외) |
| `enforce_for_root` | root 계정에도 적용 (단 difok 는 pwquality 내부 로직상 root 제외) |

**사전 설치**:
```bash
sudo apt install libpam-pwquality
```

---

## <a id="etc-login-defs"></a>/etc/login.defs

사용자 계정 전반의 기본 만료 정책.

| 필드 | Born2beRoot | 기본값 | 의미 |
|---|---|---|---|
| `PASS_MAX_DAYS` | `30` | `99999` | 비번 최대 유효 일수 |
| `PASS_MIN_DAYS` | `2` | `0` | 비번 최소 사용 일수 |
| `PASS_WARN_AGE` | `7` | `7` | 만료 경고 일수 |

**기존 계정에 재적용** (login.defs 는 신규 계정에만 자동 반영):
```bash
sudo chage -M 30 -m 2 -W 7 <login>
sudo chage -M 30 -m 2 -W 7 root
```

**검증**: `chage -l <login>`.

---

## <a id="etc-crontab"></a>/etc/crontab · /var/spool/cron/crontabs/

| 위치 | 편집 방법 | 사용자 |
|---|---|---|
| `/etc/crontab` | 텍스트 편집기로 직접 | 시스템 전역 (사용자명 지정 필요) |
| `/etc/cron.d/<file>` | 직접 | 시스템, 패키지 설치 시 표준 |
| `/var/spool/cron/crontabs/<user>` | `crontab -e` (필수) | 해당 사용자 |
| `/etc/cron.hourly/`, `daily/`, `weekly/`, `monthly/` | 스크립트 drop-in | 시스템 |

**crontab 포맷**:
```
m  h  dom  mon  dow  command
```
- `*/10 * * * *` — 10 분마다
- `* * * * *` — 매분
- `@reboot` — 부팅 직후 1 회

**Born2beRoot 권장 (root crontab)**:
```bash
sudo crontab -u root -e
```
```
@reboot         /bin/bash /root/monitoring.sh | wall
*/10 * * * *    /bin/bash /root/monitoring.sh | wall
```

**주의 — Debian 13+ wall 변경**: `setgid tty` 박탈 → SSH 세션(pts)에 wall 메시지 미도달. 루프백 테스트 시 직접 TTY(`tty1`) 로그인 필요.

---

## <a id="etc-apparmor-d"></a>/etc/apparmor.d/

AppArmor 프로필 디렉토리. 각 프로그램 경로를 이름으로 사용 (예: `usr.sbin.sshd`).

**주요 명령**:
```bash
/usr/sbin/aa-status                # 로드된 프로필 목록
sudo aa-enforce /etc/apparmor.d/*  # enforce 모드
sudo aa-complain /etc/apparmor.d/* # complain 모드 (로그만)
```

**일반적 프로필**: `usr.sbin.sshd`, `bin.ping`, `usr.bin.man`, `usr.sbin.cupsd` 등.

**Born2beRoot 에서의 역할**: SC-06 충족을 위해 `systemctl is-enabled apparmor` 가 `enabled` 이기만 하면 됨. 커스텀 프로필 작성은 요구 안 됨.

---

## <a id="etc-crypttab"></a>/etc/crypttab

부팅 시 LUKS 볼륨을 풀도록 커널에 알려주는 테이블. Guided encrypted LVM 설치 시 자동 생성.

**형식**:
```
<name>   <source device>   <key file>   <options>
```

**Born2beRoot 예시**:
```
sda5_crypt  UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  none  luks,discard
```

- `none` = 부팅 시 패스프레이즈 입력
- `discard` = SSD TRIM 전달

---

## <a id="etc-fstab"></a>/etc/fstab

영구 마운트 테이블. 설치 시 자동 생성.

**형식**:
```
<device>  <mount>  <fstype>  <options>  <dump>  <pass>
```

**Born2beRoot 예시 (발췌)**:
```
/dev/mapper/<vg>-root   /          ext4  errors=remount-ro  0  1
UUID=xxxx-boot-uuid     /boot      ext2  defaults           0  2
/dev/mapper/<vg>-home   /home      ext4  defaults           0  2
/dev/mapper/<vg>-var    /var       ext4  defaults           0  2
/dev/mapper/<vg>-var--log /var/log ext4  defaults           0  2
/dev/mapper/<vg>-srv    /srv       ext4  defaults           0  2
/dev/mapper/<vg>-tmp    /tmp       ext4  defaults           0  2
/dev/mapper/<vg>-swap_1 none       swap  sw                 0  0
```

**`<pass>`**: `fsck` 검사 순서. root=1, 나머지=2, swap=0.

---

## 관련 문서

- [Subject v5.2 조항](subject-v5.2-clauses.md)
- [명령 치트시트](commands-cheatsheet.md)
- [설명: 접근 제어](../03-explanation/access-control.md)
- [설명: 저장 스택](../03-explanation/storage-stack.md)
