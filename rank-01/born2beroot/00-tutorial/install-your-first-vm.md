---
description: "처음부터 끝까지 손잡고 함께 걷는 Born2beRoot 첫 설치. Happy path 한 경로만."
---

# 🎓 Tutorial — 첫 Born2beRoot VM 설치

> **문서 유형**: Tutorial (학습 지향). 한 경로만 따라가면 끝에 평가 가능한 VM 이 완성됩니다.
> **예상 소요**: 약 2~3 시간 (LUKS 디스크 랜덤 채우기 때문에 중간에 10~30 분 대기 구간).
> **끝나면 완성될 것**: Debian 12 기반 암호화 LVM VM · SSH 4242 접속 가능 · UFW 활성 · sudo 규칙 · 비번 정책 · monitoring.sh 10 분 주기 + 부팅 시 · signature.txt 생성.

{% hint style="info" %}
이 문서는 **튜토리얼** 입니다. 옵션이나 대안을 설명하지 않고, 가장 안정적인 한 경로만 따라갑니다.
각 단계의 "왜" 가 궁금하면 단계 끝의 "자세히 →" 링크를 눌러 [Explanation](../03-explanation/) 으로 이동하세요.
{% endhint %}

## 준비물

- VirtualBox 7.1 이상 (Qt6)
- `debian-12.x.0-amd64-netinst.iso` 다운로드 ([debian.org](https://www.debian.org/distrib/netinst))
- 호스트 자유 디스크 20 GB 이상, 자유 메모리 4 GB 이상

---

## Part A — VirtualBox VM 생성

### A1. 새 VM 마법사 시작

VirtualBox 메인 창에서 `Ctrl+N` 을 누르세요.

`Create Virtual Machine` 마법사가 열립니다.

### A2. Name and OS

다음과 같이 입력:

| 필드 | 값 |
|---|---|
| `Name` | `born2beroot` |
| `Folder` | 기본값 |
| `ISO Image` | 다운로드한 `debian-12.x.0-amd64-netinst.iso` |
| `☐ Skip Unattended Installation` | **체크 → ☑** |

`[Next]` 를 누르세요.

> Skip Unattended 를 켜야 VBox 가 사용자명/비번/파티션을 자동 답하지 않고 우리가 직접 설치 결정을 내릴 수 있습니다.

### A3. Hardware

| 필드 | 값 |
|---|---|
| `Base Memory` | `4096` MB |
| `Processors` | `3` (호스트 물리 코어 ≥ 3 개일 때) |
| `☐ Enable EFI` | **체크하지 않음** |

`[Next]` 를 누르세요.

자세히 → [vCPU·pCPU 이해](../03-explanation/virtualization.md#vcpu) · [BIOS vs UEFI](../03-explanation/boot-sequence.md#bios-uefi)

### A4. Virtual Hard Disk

| 필드 | 값 |
|---|---|
| `Create a Virtual Hard Disk Now` | 선택 |
| `Disk Size` | `20.00 GB` |
| `☐ Pre-Allocate Full Size` | **체크 → ☑** |
| `Hard Disk File Type` | `VDI` (기본) |

`[Next]` 를 누르고, Summary 화면에서 `[Finish]` 를 누르세요.

**이 시점에서**: VBox 메인 창 왼쪽 목록에 `born2beroot` 항목이 새로 보여야 합니다. ✓

### A5. VM 시작

메인 창에서 `born2beroot` 를 선택하고 `[Start]` 를 누르세요.

Debian installer 의 검정 화면과 파란 메뉴 박스가 새 창에 뜹니다.

---

## Part B — Debian installer 기본 설정

### B1. 설치 유형

화살표 키로 `Install` (텍스트 모드) 을 선택 → `Enter`.

### B2. 언어·지역·키보드

| 화면 | 선택 |
|---|---|
| `Select a language` | `English - English` |
| `Select your location` | `other` → `Asia` → `Korea, Republic of` |
| `Configure locales` | `United States - en_US.UTF-8` |
| `Configure the keyboard` | 본인 키보드 (한글은 `Korean`, 영문은 `American English`) |

몇 초간 ISO/패키지 자동 로딩 → DHCP 네트워크 자동 설정.

### B3. 호스트네임

`Hostname:` 입력란에 **본인 login + 42** 를 입력하세요. 예: `dongjki42`.

`Tab` → `<Continue>` → `Enter`.

### B4. 도메인 이름

**비워둔 채로** `Tab` → `<Continue>` → `Enter`.

### B5. Root 비밀번호

강한 비밀번호(10 자 이상, 대소문자·숫자·특수문자 혼합) 를 입력하세요. 재입력 후 `<Continue>`.

### B6. 일반 사용자 생성

| 화면 | 입력 |
|---|---|
| `Full name` | 본인 이름 (예: `Donggi Kim`) |
| `Username` | 본인 **login** 만 (예: `dongjki`, **42 는 붙이지 않음**) |
| `Password` | 강한 비밀번호 (root 와 다르게) |

### B7. 시간대

`Select your time zone` 에서 `Seoul` → `Enter`.

다음 화면: **Partition disks**. Part C 로 갑니다.

---

## Part C — 파티셔닝 (핵심)

이 부분이 가장 많이 막히는 구간입니다. 모든 키 입력을 그대로 따라가세요.

### C1. 방식 선택

`Guided - use entire disk and set up encrypted LVM` 을 선택 → `Enter`.

### C2. 디스크 선택

목록에 `sda - 21.5 GB ATA VBOX HARDDISK` 하나만 보입니다. `Enter`.

### C3. 데이터 삭제 확인

`<Yes>` 선택 → `Enter`.

### C4. 파티셔닝 스킴

`Separate /home, /var and /tmp partitions` 선택 → `Enter`.

### C5. 디스크 랜덤 채우기 (대기 구간)

`Erasing data on /dev/sda` 진행률 표시. **10~30 분 소요**. 기다리세요.

자세히 → [LUKS 와 엔트로피](../03-explanation/storage-stack.md#luks)

### C6. LUKS 패스프레이즈

**이 암호는 매 부팅마다 입력합니다.** 기억할 수 있는 12 자 이상을 입력하세요. root/사용자 비번과 다르게.

재입력 → `<Continue>`.

### C7. Volume Group 할당량

`Amount of volume group to use for guided partitioning:` 입력란이 `20.9 GB` 로 채워져 있습니다. **그대로 두고** `Tab` → `<Continue>` → `Enter`.

### C8. 메인 파티션 화면 진입

자동 생성된 파티션 표가 보입니다. LVM 안에 `home`, `root`, `swap_1`, `tmp`, `var` 5 개 LV 가 있습니다.

우리는 보너스 파티션 구조 (총 7 LV) 로 재구성할 것입니다.

`↑` 로 `Configure the Logical Volume Manager` 선택 → `Enter`.

"Write the changes to disks and configure LVM?" 화면 → `<Yes>`.

### C9. 기존 LV 삭제 (home, var, tmp)

`Delete logical volume` → `Enter` → VG 선택 → `home` → `<Yes>`.

LVM 메뉴로 돌아오면 같은 방식으로 `var`, `tmp` 도 삭제.

### C10. 새 LV 7 개 생성

`Create logical volume` 을 반복 선택하며 아래 표대로 생성:

| Name | Size |
|---|---|
| `home` | `3G` |
| `var` | `2G` |
| `var-log` | `2G` |
| `srv` | `1G` |
| `tmp` | `1G` |

> 기존에 남아 있는 `root` (7~10 GB), `swap_1` (1.5~2 GB) 은 그대로 둡니다.

### C11. LVM 메뉴 종료

`Finish` → `Enter`. 메인 파티션 화면으로 돌아옵니다.

### C12. 각 LV 마다 ext4 + 마운트 포인트 지정

메인 파티션 화면에서 각 LV 의 `#1` 줄 (처음엔 `do not use`) 을 선택 → `Enter` 하면 **파티션 설정 화면** 이 열립니다.

각 LV 마다 다음 3 단계 반복:

1. `Use as:` → `Ext4 journaling file system` 선택
2. `Mount point:` → 아래 표의 마운트 경로 선택 (`/var/log` 는 목록에 없으므로 `Enter manually` → 타이핑)
3. `Done setting up the partition` 선택

| LV | 마운트 포인트 |
|---|---|
| `home` | `/home` |
| `var` | `/var` |
| `var-log` | `/var/log` (manually 입력) |
| `srv` | `/srv` |
| `tmp` | `/tmp` |
| `root` | `/` (이미 설정되어 있음) |
| `swap_1` | swap area (이미 설정되어 있음) |

**이 시점에서**: 메인 파티션 화면의 모든 LV 줄 끝에 `f ext4` 와 마운트 경로가 보여야 합니다. ✓

### C13. 파티셔닝 쓰기

`Finish partitioning and write changes to disk` → `Enter`.

확인 화면에서 `<Yes>`.

디스크에 파일시스템 생성 (자동, 수 분).

---

## Part D — 패키지 · 소프트웨어 · GRUB

### D1. 아카이브 미러

| 화면 | 선택 |
|---|---|
| `Scan extra installation media?` | `<No>` |
| `Debian archive mirror country` | `Korea, Republic of` |
| `Debian archive mirror` | `deb.debian.org` |
| `HTTP proxy information` | 비워두고 `<Continue>` |

`popularity-contest` 설문은 `<No>` 로.

### D2. 소프트웨어 선택 (매우 중요)

`Software selection` 화면에서 **GUI 관련 항목을 전부 해제** 하고 **SSH server 와 standard system utilities 만 체크** 합니다.

| 항목 | 체크 여부 |
|---|---|
| `Debian desktop environment` | ☐ |
| `... GNOME` | ☐ |
| `... Xfce` 등 기타 DE | ☐ |
| `web server` | ☐ |
| `print server` | ☐ |
| `SSH server` | **☑** |
| `standard system utilities` | **☑** |

`Space` 로 체크를 토글합니다. `Tab` → `<Continue>`.

자세히 → [GUI 금지의 이유 (SC-04)](../03-explanation/design-decisions.md)

### D3. GRUB 부트로더 설치

`Install the GRUB boot loader to your primary drive?` → `<Yes>`.

디스크 선택 화면 → `/dev/sda` → `Enter`.

### D4. 설치 완료 + 재부팅

`Installation complete` 화면에서 `<Continue>`.

VM 이 자동으로 꺼졌다 켜지며, **검정 화면에 LUKS 패스프레이즈 프롬프트** 가 나타납니다.

C6 에서 설정한 LUKS 암호를 입력하세요.

### D5. 첫 로그인

로그인 프롬프트에서:
- Username: `<login>` (B6 에서 설정한 것. 예: `dongjki`)
- Password: B6 의 사용자 비번

**이 시점에서**: `$` 프롬프트가 보이면 설치는 성공했습니다. ✓

---

## Part E — 후설정 (평가 대상)

여기부터는 터미널에서 명령을 입력하는 구간입니다. 복사-붙여넣기 가능합니다.

**먼저 root 로 전환**:

```bash
su -
```

(root 비밀번호 입력)

### E1. 시스템 업데이트

```bash
apt update && apt upgrade -y
```

### E2. SSH 포트 4242

```bash
nano /etc/ssh/sshd_config
```

파일에서 두 줄을 찾아 수정하세요:

```
Port 4242
PermitRootLogin no
```

저장 (`Ctrl+O` → `Enter` → `Ctrl+X`) 후:

```bash
systemctl restart ssh
systemctl status ssh | head -5
```

`Active: active (running)` 확인.

### E3. AppArmor 활성화

```bash
apt install apparmor apparmor-utils -y
systemctl enable apparmor
systemctl start apparmor
/usr/sbin/aa-status
```

출력에 `apparmor module is loaded.` 보이면 OK.

### E4. UFW 방화벽

```bash
apt install ufw -y
ufw default deny incoming
ufw default allow outgoing
ufw allow 4242/tcp
ufw enable
ufw status verbose
```

`Status: active` + `4242/tcp  ALLOW` 확인.

### E5. sudo 엄격 설정

```bash
apt install sudo -y
usermod -aG sudo <login>         # <login> 은 본인
groupadd user42
usermod -aG user42 <login>
mkdir -p /var/log/sudo
chmod 750 /var/log/sudo
visudo -f /etc/sudoers.d/born2beroot
```

열린 편집기에 다음을 붙여넣고 저장:

```
Defaults    passwd_tries=3
Defaults    badpass_message="WRONG PASSWORD. Retry."
Defaults    logfile="/var/log/sudo/sudo.log"
Defaults    log_input, log_output
Defaults    iolog_dir="/var/log/sudo"
Defaults    requiretty
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

저장하면 `visudo` 가 자동으로 문법 검증 합니다.

검증:

```bash
sudo visudo -c
groups <login>
```

### E6. 비밀번호 정책 (PAM + login.defs)

```bash
apt install libpam-pwquality -y
nano /etc/pam.d/common-password
```

`pam_pwquality.so` 로 시작하는 줄을 찾아 **옵션을 다음처럼 교체**:

```
password  requisite  pam_pwquality.so  retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

그리고:

```bash
nano /etc/login.defs
```

세 줄을 찾아 값을 수정:

```
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
PASS_WARN_AGE   7
```

기존 계정에 반영:

```bash
chage -M 30 -m 2 -W 7 <login>
chage -M 30 -m 2 -W 7 root
```

모든 계정 비밀번호 재설정 (SC-24):

```bash
passwd <login>
passwd root
```

### E7. monitoring.sh

```bash
nano /root/monitoring.sh
```

다음 스크립트를 붙여넣으세요:

```bash
#!/bin/bash

arch=$(uname -a)
pcpu=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)
vcpu=$(grep -c "processor" /proc/cpuinfo)
mem=$(free -m | awk 'NR==2 {printf "%s/%sMB (%.2f%%)",$3,$2,$3*100/$2}')
disk=$(df -h --total | awk '/^total/ {print $3"/"$2" ("$5")"}')
cpu=$(top -bn1 | grep "Cpu(s)" | awk '{printf "%.1f%%", $2+$4}')
last=$(who -b | awk '{print $3" "$4}')
lvm=$(if [ $(lsblk | grep -c lvm) -gt 0 ]; then echo yes; else echo no; fi)
tcp=$(ss -t | grep -c ESTAB)
ulog=$(who | wc -l)
ip=$(hostname -I | awk '{print $1}')
mac=$(ip link show | awk '/ether/ {print $2; exit}')
sudocnt=$(journalctl _COMM=sudo | grep -c COMMAND)

cat <<EOF
#Architecture: $arch
#CPU physical: $pcpu
#vCPU: $vcpu
#Memory Usage: $mem
#Disk Usage: $disk
#CPU load: $cpu
#Last boot: $last
#LVM use: $lvm
#Connections TCP: $tcp ESTABLISHED
#User log: $ulog
#Network: IP $ip ($mac)
#Sudo: $sudocnt cmd
EOF
```

권한 설정 + 테스트 실행:

```bash
chmod +x /root/monitoring.sh
/root/monitoring.sh
```

12 개 행이 모두 출력되는지 확인하세요.

crontab 등록:

```bash
crontab -e
```

편집기에 두 줄 추가:

```
@reboot         /bin/bash /root/monitoring.sh | wall
*/10 * * * *    /bin/bash /root/monitoring.sh | wall
```

저장 후:

```bash
crontab -l
```

두 줄이 보이면 OK.

### E8. (보너스) WordPress + Netdata

보너스는 **필수 부분이 완벽할 때만** 채점됩니다. [보너스 How-to](../01-how-to/add-wordpress-and-netdata.md) 를 따로 참고하세요.

### E9. 서명 생성

VM 을 완전히 종료:

```bash
shutdown -h now
```

**호스트 터미널** 에서:

```bash
cd "~/VirtualBox VMs/born2beroot"
sha1sum *.vdi
```

출력된 40 자리 16 진수 해시를 Git 저장소 루트의 `signature.txt` 에 붙여넣으세요.

```bash
cd ~/your-repo
echo "<붙여넣은 해시>" > signature.txt
cat signature.txt
```

### E10. 스냅샷 제거 확인

VBox 메인 창 → VM 선택 → `Snapshots` 탭. "Current State" 외에 아무것도 없어야 합니다.

---

## 🎉 완성

여기까지 따라왔다면 다음을 달성한 상태입니다:

- ✅ Debian 12 암호화 LVM VM (LV 7 개)
- ✅ AppArmor · UFW · SSH 4242
- ✅ sudo 엄격 설정 + /var/log/sudo/
- ✅ PAM pwquality + chage 30/2/7
- ✅ monitoring.sh 10 분 주기 + @reboot
- ✅ signature.txt

## 다음은?

- **평가를 앞둔 경우**: [How-to: peer evaluation 통과](../01-how-to/pass-peer-evaluation.md) 로 시연 리허설
- **보너스 진행**: [How-to: WordPress + Netdata](../01-how-to/add-wordpress-and-netdata.md)
- **문제 발생 시**: [How-to: 잠김 복구](../01-how-to/recover-from-lockout.md)
- **개념 이해**: [Explanation](../03-explanation/) 의 8 개 주제별 설명
- **정확한 스펙 확인**: [Reference](../02-reference/)
