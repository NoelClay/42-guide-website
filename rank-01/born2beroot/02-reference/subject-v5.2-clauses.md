---
description: "Subject v5.2 의 'must/have to' 조항 41 개와 각 조항별 검증 명령. 중립적 사실만 기록."
---

# 📚 Reference — Subject v5.2 조항

> **문서 유형**: Reference (정보 지향). 순수 사실만 담음. 절차·권장·의견 없음.
> **원본**: [`en_subject_born2beroot.pdf`](../_reference/en_subject_born2beroot.pdf) (Version 5.2, 18 pages)

## 목차

| Chapter | 조항 번호 | 항목 수 |
|---|---|---|
| [IV. General](#chapter-iv) | SC-01 ~ 03 | 3 |
| [V. Mandatory](#chapter-v) | SC-04 ~ 31 | 28 |
| [VI. README](#chapter-vi) | SC-32 ~ 35 | 4 |
| [VII. Bonus](#chapter-vii) | SC-36 ~ 38 | 3 |
| [VIII. Submission](#chapter-viii) | SC-39 ~ 41 | 3 |
| **합계** | | **41** |

---

## <a id="chapter-iv"></a>Chapter IV — General guidelines

### SC-01. VirtualBox 강제
> "The use of **VirtualBox** (or **UTM** if you can't use **VirtualBox**) is mandatory."

**검증**: VBox GUI 에서 VM 존재 확인.

### SC-02. signature.txt 제출
> "You only have to submit a **signature.txt** file at the root of your repository. You must paste the signature of your machine's virtual disk in it."

**검증**:
```bash
sha1sum path/to/born2beroot.vdi
cat signature.txt
# 두 출력이 일치해야 함
```

### SC-03. 평가 시작 시 스냅샷 없음
> "No snapshots may exist at the beginning of each evaluation."

**검증**: VBox → VM → Snapshots 탭 → "Current State" 외 항목이 없어야 함.

---

## <a id="chapter-v"></a>Chapter V — Mandatory part

### SC-04. GUI 금지 (X.org, Wayland, 기타)
> "It is therefore **forbidden** to install **X.org, Wayland**, or any other equivalent graphics server. Otherwise, your grade will be **0**."

**검증**:
```bash
dpkg -l | grep -E "xserver|wayland|gnome|kde|xfce"  # 출력 없음이 정답
systemctl get-default                                # multi-user.target
```

### SC-05. Debian 또는 Rocky 최신 안정판
> "You must choose as your operating system either the latest stable version of **Debian** (no testing/unstable) or the latest stable version of **Rocky**."

**검증**:
```bash
cat /etc/os-release | head -n 2
```

### SC-06. AppArmor 시작 시 작동 (Debian)
> "**AppArmor** for Debian must be running at startup too."

**검증**:
```bash
/usr/sbin/aa-status
systemctl is-enabled apparmor
```

### SC-07. 최소 2 개 암호화 파티션 + LVM
> "You must create at least **2 encrypted partitions** using **LVM**."

**검증**:
```bash
lsblk
cat /etc/crypttab
vgs && lvs
```

### SC-08. SSH 포트 4242
> "An **SSH service** must be running on **port 4242** in your virtual machine."

**검증**:
```bash
ss -tunlp | grep 4242
systemctl is-active ssh
```

### SC-09. SSH root 로그인 금지
> "For security reasons, it must not be possible to connect using **SSH as root**."

**검증**:
```bash
grep -i "^PermitRootLogin" /etc/ssh/sshd_config
# 출력: PermitRootLogin no
```

### SC-10. UFW + 포트 4242 만 허용
> "You have to configure your operating system with the **UFW** firewall ... and leave only **port 4242 open**."

**검증**:
```bash
/usr/sbin/ufw status verbose
```

### SC-11. 방화벽 시작 시 활성
> "Your firewall must be **active** when you launch your virtual machine."

**검증**:
```bash
systemctl is-enabled ufw
```

### SC-12. hostname = `<login>42`
> "The **hostname** of your virtual machine must be your login ending with **42** (e.g., wil42). You will have to modify this hostname during your peer review."

**검증**:
```bash
hostnamectl
```

### SC-13. 강한 비밀번호 정책
> "You have to implement a **strong password policy**."

**세부 조항**: SC-17 ~ SC-24.

**검증**:
```bash
grep pwquality /etc/pam.d/common-password
cat /etc/login.defs | grep -E "PASS_MAX|PASS_MIN|PASS_WARN"
```

### SC-14. sudo 엄격 설정
> "You have to install and configure **sudo** following strict rules."

**세부 조항**: SC-25 ~ SC-29.

**검증**:
```bash
which sudo
sudo visudo -c
```

### SC-15. root 외 본인 login 사용자 존재
> "In addition to the root user, a user with your login as the username has to be present."

**검증**:
```bash
getent passwd <login>
```

### SC-16. 사용자 = user42 + sudo 그룹 멤버
> "This user has to belong to the **user42** and **sudo** groups."

**검증**:
```bash
groups <login>
# 출력에 user42 와 sudo 포함
```

### SC-17. 비번 만료 30 일
> "Your password has to **expire every 30 days**."

**검증**:
```bash
chage -l <login>
chage -l root
# Maximum number of days: 30
```

### SC-18. 비번 변경 최소 간격 2 일
> "The minimum number of days between password changes must be set to **2**."

**검증**:
```bash
chage -l <login>
# Minimum number of days: 2
```

### SC-19. 만료 7 일 전 경고
> "The user has to receive a warning message **7 days** before their password expires."

**검증**:
```bash
chage -l <login>
# Number of days of warning before password expires: 7
```

### SC-20. 비번 10 자 + 대/소/숫자 + 연속 3 초과 금지
> "Your password must be at least **10 characters long**. It must contain an **uppercase letter**, a **lowercase letter**, and a **number**. Also, it must not contain more than **3 consecutive identical characters**."

**검증**:
```bash
grep pwquality /etc/pam.d/common-password
# minlen=10 dcredit=-1 ucredit=-1 lcredit=-1 maxrepeat=3
```

### SC-21. 비번에 사용자명 미포함
> "The password must **not include the name of the user**."

**검증**:
```bash
grep reject_username /etc/pam.d/common-password
```

### SC-22. 이전 비번과 7 자 이상 달라야 함 (root 제외)
> "The password must contain at least **7 characters that were not part of the previous password**."

**검증**:
```bash
grep difok /etc/pam.d/common-password
# difok=7
```

### SC-23. root 도 정책 준수
> "Of course, your **root password has to comply with this policy**."

**검증**:
```bash
grep enforce_for_root /etc/pam.d/common-password
```

### SC-24. 설정 후 전체 계정 비번 재설정
> "After setting up your configuration files, you will have to **change all the passwords** of the accounts present on the virtual machine, **including the root account**."

**검증**:
```bash
chage -l <login> | grep "Last password change"
chage -l root    | grep "Last password change"
```

### SC-25. sudo 3 회 오인증 제한
> "Authentication using sudo has to be limited to **3 attempts**."

**검증**:
```bash
grep passwd_tries /etc/sudoers /etc/sudoers.d/*
# Defaults passwd_tries=3
```

### SC-26. sudo 오인증 커스텀 메시지
> "A **custom message** of your choice has to be displayed if an incorrect password is entered when using sudo."

**검증**:
```bash
grep badpass_message /etc/sudoers /etc/sudoers.d/*
```

### SC-27. sudo 입/출력 로그 → /var/log/sudo/
> "Each action performed with sudo has to be **logged**, including both **inputs and outputs**. The log file has to be saved in the **/var/log/sudo/** folder."

**검증**:
```bash
ls -la /var/log/sudo/
grep -E "logfile|log_input|log_output|iolog_dir" /etc/sudoers /etc/sudoers.d/*
```

### SC-28. sudo TTY 모드 활성
> "The **TTY mode** has to be enabled for security reasons."

**검증**:
```bash
grep requiretty /etc/sudoers /etc/sudoers.d/*
```

### SC-29. sudo 경로 제한 (secure_path)
> "For security reasons, the paths that can be used by sudo must also be restricted. Example:
> `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`"

**검증**:
```bash
grep secure_path /etc/sudoers /etc/sudoers.d/*
```

### SC-30. monitoring.sh (bash) 시작 시 + 10 분 주기
> "You have to create a simple script called **monitoring.sh**. It must be written in **bash**. At server startup, the script will display the information listed below on all terminals, and **every 10 minutes** (take a look at **wall**)."

**검증**:
```bash
file /root/monitoring.sh          # bash script
crontab -l | grep monitoring       # */10 * * * *
crontab -l | grep @reboot          # @reboot 행 존재
```

### SC-31. monitoring.sh 12 항목 출력
> - architecture + kernel version
> - physical processors
> - virtual processors
> - available RAM + utilisation %
> - available storage + utilisation %
> - CPU utilisation %
> - last reboot
> - LVM active yes/no
> - active connections
> - users using the server
> - IPv4 + MAC address
> - number of sudo commands executed

**검증**:
```bash
bash /root/monitoring.sh
# 12 개 항목 모두 출력
```

---

## <a id="chapter-vi"></a>Chapter VI — README requirements

### SC-32. README.md Git 루트 제출
> "A **README.md** file must be provided at the **root** of your Git repository."

### SC-33. README 첫 줄 이탤릭 (정해진 문구)
> "The very first line must be italicized and read: *This project has been created as part of the 42 curriculum by <login1>[, <login2>[, <login3>[...]]]*."

### SC-34. Description / Instructions / Resources 섹션
> "The README.md must include at least:
> - A **Description** section ...
> - An **Instructions** section ...
> - A **Resources** section listing classic references ... as well as a **description of how AI was used**."

### SC-35. Project description + 4 가지 비교
> "A **Project description** section must also explain your choice of operating system ... and provide a comparison between:
> - Debian vs Rocky Linux
> - AppArmor vs SELinux
> - UFW vs firewalld
> - VirtualBox vs UTM"

---

## <a id="chapter-vii"></a>Chapter VII — Bonus part

> "The bonus part will only be assessed if the mandatory part is **PERFECT**."

### SC-36. 파티션 구조 (bonus lsblk 예시)
> "Set up the partitions correctly so that you obtain a structure similar to the one below:"
> (sda 30.8G / sda1 500M /boot / sda2 1K / sda5 30.3G crypt / LVMGroup-root 10G / ... LVMGroup-var--log 4G /var/log)

### SC-37. WordPress (lighttpd + MariaDB + PHP)
> "Set up a functional **WordPress** website with the following services: **lighttpd**, **MariaDB**, and **PHP**."

### SC-38. 추가 서비스 1 개 (NGINX/Apache 금지)
> "Set up a service of your choice ... (**NGINX / Apache2 excluded!**). During the defense, you will have to **justify your choice**."

---

## <a id="chapter-viii"></a>Chapter VIII — Submission and peer-evaluation

### SC-39. README.md + signature.txt 제출
> "You only need to submit the expected **README.md** file, and a **signature.txt** file at the root of your Git repository."

### SC-40. sha1 형식 서명
> "Retrieve the signature from the '.vdi' file ... of your virtual machine in **sha1** format."

### SC-41. VM 자체 Git 포함 금지
> "It is of course **FORBIDDEN** to include your virtual machine in your Git repository. ... If the two of them are not identical, your **grade will be 0**."

**검증**:
```bash
find . \( -name "*.vdi" -o -name "*.vmdk" -o -name "*.qcow2" -o -name "*.ova" \) -print
# 출력 없음이 정답
```

---

## 관련 문서

- [평가 프로토콜 (peer-eval)](peer-eval-protocol.md)
- [설정 파일 스펙](config-files.md)
- [명령 치트시트](commands-cheatsheet.md)
- [튜토리얼: 첫 VM 설치](../00-tutorial/install-your-first-vm.md)
- [How-to: 평가 통과](../01-how-to/pass-peer-evaluation.md)
