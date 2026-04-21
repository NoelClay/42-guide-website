# 📋 Born2beRoot v5.2 — WHAT Subject 매핑 (2026-04-18)

**3 문서 체계 중 ② WHAT (평가자의 시선)** · Subject 원문 ↔ HOW 단계 ↔ WHY 이론 삼각 연결

| 문서 | 역할 | 파일 |
|---|---|---|
| ① HOW | 키 입력 단위 단계별 가이드 | `Born2beRoot_v5_2_HOW_2026-04-18.md` |
| ② **WHAT** (본 문서) | Subject `must/have to` 조항 → HOW/WHY 매핑 | `Born2beRoot_v5_2_WHAT_2026-04-18.md` |
| ③ WHY | 각 개념 3 단 이론 (중학생/학부/구글) | `Born2beRoot_v5_2_WHY_2026-04-18.md` |

**본 문서 사용법**:
- **평가 직전 체크리스트**: 41 개 조항을 하나씩 훑으며 본인 VM 이 각 조항을 만족하는지 검증 명령으로 확인
- **평가 질문 대응**: 평가자가 특정 조항을 물으면 WHAT-NN 번호로 HOW 단계와 WHY 이론을 즉답

**Subject 원문 파일**: [`en_subject_born2beroot.pdf`](./_reference/en_subject_born2beroot.pdf) (Version 5.2, 18 pages)

---

## 목차

| 구분 | 범위 | 조항 수 |
|---|---|---|
| [Chapter IV — General](#ch-iv) | WHAT-01 ~ 03 | 3 |
| [Chapter V — Mandatory](#ch-v) | WHAT-04 ~ 31 | 28 |
| [Chapter VI — README Requirements](#ch-vi) | WHAT-32 ~ 35 | 4 |
| [Chapter VII — Bonus](#ch-vii) | WHAT-36 ~ 38 | 3 |
| [Chapter VIII — Submission](#ch-viii) | WHAT-39 ~ 41 | 3 |
| **합계** | | **41** |

---

# <a id="ch-iv"></a>Chapter IV — General guidelines

## <a id="what-01"></a>WHAT-01. VirtualBox (또는 UTM) 사용 강제

**Subject 원문 (Chapter IV, p.6)**:
> "The use of **VirtualBox** (or **UTM** if you can't use **VirtualBox**) is mandatory."

**만족 단계**: [→ HOW Part A (VM 생성 전체)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-part-a)
**이론 배경**: [→ WHY-01 하이퍼바이저](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-01)
**검증 명령**: (없음 — VBox GUI 에서 VM 존재 확인)

---

## <a id="what-02"></a>WHAT-02. signature.txt 제출

**Subject 원문 (Chapter IV, p.6)**:
> "You only have to submit a **signature.txt** file at the root of your repository. You must paste the signature of your machine's virtual disk in it."

**만족 단계**: [→ HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9)
**이론 배경**: [→ WHY-25 해시 함수](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-25)
**검증 명령** (호스트):
```bash
cd "~/VirtualBox VMs/born2beroot"
sha1sum *.vdi                             # 40 자 16 진수 출력
cat signature.txt                         # 제출물 일치 확인
```

---

## <a id="what-03"></a>WHAT-03. 평가 시작 시 스냅샷 없음

**Subject 원문 (Chapter IV, p.6 + Chapter VIII, p.17)**:
> "No snapshots may exist at the beginning of each evaluation."
> "The use of snapshots is restricted. Each defense must start without any snapshots."

**만족 단계**: [→ HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9)
**이론 배경**: [→ WHY-26 스냅샷 (COW)](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-26)
**검증**: VBox 메인 창 → VM 선택 → Snapshots 탭에 "Current State" 만 있고 스냅샷 없음 확인

---

# <a id="ch-v"></a>Chapter V — Mandatory part

## <a id="what-04"></a>WHAT-04. GUI(그래픽 서버) 금지

**Subject 원문 (Chapter V, p.7, 빨간 경고)**:
> "It is therefore **forbidden** to install **X.org, Wayland**, or any other equivalent graphics server. Otherwise, your grade will be **0**."

**만족 단계**: [→ HOW D3 소프트웨어 선택](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-d3)
**이론 배경**: (선택 규칙, 이론 없음)
**검증 명령**:
```bash
dpkg -l | grep -E "xserver|wayland|gnome|kde|xfce"   # 출력이 비어야 정답
systemctl status graphical.target                     # inactive 확인
```

---

## <a id="what-05"></a>WHAT-05. Debian 최신 안정판 또는 Rocky 최신 안정판

**Subject 원문 (Chapter V, p.7)**:
> "You must choose as your operating system either the latest stable version of **Debian** (no testing/unstable) or the latest stable version of **Rocky**. Debian is highly recommended if you are new to system administration."

**만족 단계**: [→ HOW A2 (ISO 선택)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-a2)
**이론 배경**: (배포판 정책, 이론 없음)
**검증 명령**:
```bash
head -n 2 /etc/os-release
# PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
# NAME="Debian GNU/Linux"
```

---

## <a id="what-06"></a>WHAT-06. AppArmor (Debian) 시작 시 작동

**Subject 원문 (Chapter V, p.7, 파란 정보박스)**:
> "**AppArmor** for Debian must be running at startup too."

**만족 단계**: [→ HOW E2.5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2-5)
**이론 배경**: [→ WHY-19 AppArmor vs SELinux](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-19)
**검증 명령**:
```bash
/usr/sbin/aa-status                       # "apparmor module is loaded." 확인
systemctl is-enabled apparmor             # "enabled" 확인
```

---

## <a id="what-07"></a>WHAT-07. 최소 2 개의 암호화 파티션 + LVM

**Subject 원문 (Chapter V, p.7)**:
> "You must create at least **2 encrypted partitions** using **LVM**."

**만족 단계**: [→ HOW C1~C13 파티셔닝 전체](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-part-c)
특히 [C1 Guided encrypted LVM](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c1),
[C6 LUKS 암호](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c6),
[C10 LV 생성](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c10)
**이론 배경**: [→ WHY-11 LVM](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-11) · [→ WHY-12 LUKS](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-12)
**검증 명령**:
```bash
lsblk                                     # sda5_crypt 위에 LV 7 개 확인
cat /etc/crypttab                         # sda5_crypt 엔트리 확인
vgs                                       # VG 확인
lvs                                       # LV 목록 확인
```

---

## <a id="what-08"></a>WHAT-08. SSH 포트 4242 실행

**Subject 원문 (Chapter V, p.8)**:
> "An **SSH service** must be running on **port 4242** in your virtual machine."

**만족 단계**: [→ HOW E2](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2)
**이론 배경**: [→ WHY-14 SSH](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-14)
**검증 명령**:
```bash
ss -tunlp | grep 4242                     # 4242 LISTEN 확인
systemctl status ssh                      # active (running) 확인
```

---

## <a id="what-09"></a>WHAT-09. SSH root 로그인 금지

**Subject 원문 (Chapter V, p.8)**:
> "For security reasons, it must not be possible to connect using **SSH as root**."

**만족 단계**: [→ HOW E2](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2)
**이론 배경**: [→ WHY-14 SSH](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-14) · [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
grep -i permitrootlogin /etc/ssh/sshd_config   # "PermitRootLogin no" 확인
ssh root@localhost -p 2222                # 호스트에서 시도 → "Permission denied" 가 정답
```

---

## <a id="what-10"></a>WHAT-10. UFW 설치 + 4242 만 허용

**Subject 원문 (Chapter V, p.8)**:
> "You have to configure your operating system with the **UFW** firewall (or firewalld for Rocky) firewall and leave only **port 4242 open** in your virtual machine."

**만족 단계**: [→ HOW E3](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e3)
**이론 배경**: [→ WHY-15 UFW/iptables](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-15)
**검증 명령**:
```bash
/usr/sbin/ufw status                      # Status: active + 4242 ALLOW
ufw status verbose                        # Default: deny (incoming), allow (outgoing)
```

---

## <a id="what-11"></a>WHAT-11. 방화벽 VM 시작 시 활성

**Subject 원문 (Chapter V, p.8, 파란 정보박스)**:
> "Your firewall must be **active** when you launch your virtual machine."

**만족 단계**: [→ HOW E3](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e3) (`ufw enable` 이 systemd enable 도 수행)
**이론 배경**: [→ WHY-15 UFW/iptables](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-15)
**검증 명령**:
```bash
systemctl is-enabled ufw                  # enabled 확인
# 재부팅 후에도 ufw status → active 여야 함
```

---

## <a id="what-12"></a>WHAT-12. hostname = `<login>42`

**Subject 원문 (Chapter V, p.8)**:
> "The **hostname** of your virtual machine must be your login ending with **42** (e.g., wil42). You will have to modify this hostname during your peer review."

**만족 단계**: [→ HOW B6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-b6)
**이론 배경**: (명명 규칙)
**검증 명령**:
```bash
hostname                                  # <login>42 출력
hostnamectl                               # Static hostname: <login>42
# 평가 시 변경 시연: hostnamectl set-hostname <newname>
```

---

## <a id="what-13"></a>WHAT-13. 강한 비밀번호 정책 구현

**Subject 원문 (Chapter V, p.8)**:
> "You have to implement a **strong password policy**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**세부 조항**: [WHAT-17](#what-17) ~ [WHAT-24](#what-24) 참조
**검증 명령**:
```bash
cat /etc/pam.d/common-password | grep pwquality
cat /etc/login.defs | grep -E "PASS_MAX|PASS_MIN|PASS_WARN"
```

---

## <a id="what-14"></a>WHAT-14. sudo 엄격 설정 구현

**Subject 원문 (Chapter V, p.8)**:
> "You have to install and configure **sudo** following strict rules."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**세부 조항**: [WHAT-25](#what-25) ~ [WHAT-29](#what-29) 참조
**검증 명령**:
```bash
which sudo                                # /usr/bin/sudo 출력
sudo visudo -c                            # 문법 검증 OK
cat /etc/sudoers | grep -E "Defaults|secure_path|requiretty|log_"
```

---

## <a id="what-15"></a>WHAT-15. root 외에 본인 login 사용자 존재

**Subject 원문 (Chapter V, p.8)**:
> "In addition to the root user, a user with your login as the username has to be present."

**만족 단계**: [→ HOW B9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-b9)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
getent passwd <login>                     # 사용자 엔트리 출력
id <login>                                # uid=1000, gid 여러 개
```

---

## <a id="what-16"></a>WHAT-16. 사용자 = user42 + sudo 그룹 멤버

**Subject 원문 (Chapter V, p.8)**:
> "This user has to belong to the **user42** and **sudo** groups."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4)
**이론 배경**: [→ WHY-17 그룹](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-17)
**검증 명령**:
```bash
getent group sudo | grep <login>
getent group user42 | grep <login>
# 또는 한 줄: groups <login>
```

---

## <a id="what-17"></a>WHAT-17. 비번 만료 30 일

**Subject 원문 (Chapter V, p.8)**:
> "Your password has to **expire every 30 days**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`PASS_MAX_DAYS 30` + `chage -M 30`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
chage -l <login>                          # Maximum number of days: 30
chage -l root                             # root 도 30
```

---

## <a id="what-18"></a>WHAT-18. 비번 변경 최소 간격 2 일

**Subject 원문 (Chapter V, p.9)**:
> "The minimum number of days between password changes must be set to **2**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`PASS_MIN_DAYS 2` + `chage -m 2`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
chage -l <login>                          # Minimum number of days: 2
```

---

## <a id="what-19"></a>WHAT-19. 만료 7 일 전 경고

**Subject 원문 (Chapter V, p.9)**:
> "The user has to receive a warning message **7 days** before their password expires."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`PASS_WARN_AGE 7` + `chage -W 7`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
chage -l <login>                          # Number of days of warning before password expires: 7
```

---

## <a id="what-20"></a>WHAT-20. 비번 10 자 + 대/소/숫자 + 연속 3 초과 금지

**Subject 원문 (Chapter V, p.9)**:
> "Your password must be at least **10 characters long**. It must contain an **uppercase letter**, a **lowercase letter**, and a **number**. Also, it must not contain more than **3 consecutive identical characters**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`minlen=10 dcredit=-1 ucredit=-1 lcredit=-1 maxrepeat=3`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령** (실제 비번 변경 시도):
```bash
passwd <login>
# "aaaa" 같은 약한 비번 입력 시 "BAD PASSWORD" 거부되는지 확인
```

---

## <a id="what-21"></a>WHAT-21. 비번에 사용자명 미포함

**Subject 원문 (Chapter V, p.9)**:
> "The password must **not include the name of the user**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`reject_username`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
grep reject_username /etc/pam.d/common-password
# 실제 시도: passwd <login> → "<login>123" 시도 → 거부
```

---

## <a id="what-22"></a>WHAT-22. (root 제외) 이전 비번과 7 자 이상 달라야 함

**Subject 원문 (Chapter V, p.9)**:
> "The following rule does not apply to the root password: the password must contain at least **7 characters that were not part of the previous password**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`difok=7`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**중요**: `enforce_for_root` 를 설정해도 **difok 만은 root 예외** (pwquality 내부 로직)
**검증 명령**:
```bash
grep difok /etc/pam.d/common-password     # difok=7 확인
# 실제 시도: 사용자는 difok 적용, root 는 미적용
```

---

## <a id="what-23"></a>WHAT-23. root 도 위 정책 준수

**Subject 원문 (Chapter V, p.9)**:
> "Of course, your **root password has to comply with this policy**."

**만족 단계**: [→ HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) (`enforce_for_root`)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
grep enforce_for_root /etc/pam.d/common-password
passwd root                               # 약한 비번 거부되는지 확인
```

---

## <a id="what-24"></a>WHAT-24. 설정 후 전체 계정 비번 재설정

**Subject 원문 (Chapter V, p.9, 빨간 경고박스)**:
> "After setting up your configuration files, you will have to **change all the passwords** of the accounts present on the virtual machine, **including the root account**."

**만족 단계**: [→ HOW E5 (맨 끝)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5)
**이론 배경**: [→ WHY-18 PAM + pwquality](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18)
**검증 명령**:
```bash
chage -l <login> | grep "Last password change"     # 최근 날짜 확인
chage -l root | grep "Last password change"        # root 도 최근 날짜
```

---

## <a id="what-25"></a>WHAT-25. sudo 3 회 오인증 제한

**Subject 원문 (Chapter V, p.9)**:
> "Authentication using sudo has to be limited to **3 attempts** in the event of an incorrect password."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) (`Defaults passwd_tries=3`)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
grep passwd_tries /etc/sudoers            # passwd_tries=3
sudo -k && sudo ls                        # 3 회 틀리면 종료 확인
```

---

## <a id="what-26"></a>WHAT-26. sudo 오인증 커스텀 메시지

**Subject 원문 (Chapter V, p.9)**:
> "A **custom message** of your choice has to be displayed if an incorrect password is entered when using sudo."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) (`Defaults badpass_message="WRONG PASSWORD"`)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
grep badpass_message /etc/sudoers
sudo -k && sudo ls                        # 틀린 비번 입력 → "WRONG PASSWORD" 출력
```

---

## <a id="what-27"></a>WHAT-27. sudo 입/출력 로그 → /var/log/sudo/

**Subject 원문 (Chapter V, p.9)**:
> "Each action performed with sudo has to be **logged**, including both **inputs and outputs**. The log file has to be saved in the **/var/log/sudo/** folder."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) (`logfile`, `log_input`, `log_output`, `iolog_dir`)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
ls -la /var/log/sudo/                     # sudo.log + I/O 로그 파일 확인
grep -E "logfile|log_input|log_output|iolog_dir" /etc/sudoers
```

---

## <a id="what-28"></a>WHAT-28. sudo TTY 모드 활성

**Subject 원문 (Chapter V, p.9)**:
> "The **TTY mode** has to be enabled for security reasons."

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) (`Defaults requiretty`)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
grep requiretty /etc/sudoers              # Defaults requiretty 확인
```

---

## <a id="what-29"></a>WHAT-29. sudo 경로 제한 (secure_path)

**Subject 원문 (Chapter V, p.9)**:
> "For security reasons, the paths that can be used by sudo must also be restricted. Example:
> `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`"

**만족 단계**: [→ HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) (`Defaults secure_path="..."`)
**이론 배경**: [→ WHY-16 root/sudo](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16)
**검증 명령**:
```bash
grep secure_path /etc/sudoers             # Subject 예시와 동일한지 확인
```

---

## <a id="what-30"></a>WHAT-30. monitoring.sh (bash) 시작 시 + 10 분 주기

**Subject 원문 (Chapter V, p.10)**:
> "Finally, you have to create a simple script called **monitoring.sh**. It must be written in **bash**.
> At server startup, the script will display the information listed below on all terminals, and **every 10 minutes** (take a look at **wall**). The banner is optional. No errors should be displayed."

**만족 단계**: [→ HOW E6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e6)
**이론 배경**: [→ WHY-20 cron 과 wall](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-20)
**검증 명령**:
```bash
cat /root/monitoring.sh                   # bash 스크립트 확인
crontab -l | grep monitoring              # */10 * * * * 확인
# 시작 시 실행 확인 (방법 A 또는 B):
crontab -l | grep @reboot                 # 방법 A
systemctl is-enabled monitoring-startup   # 방법 B
# 재부팅 후 wall 메시지 즉시 표시되는지 확인
```

---

## <a id="what-31"></a>WHAT-31. monitoring.sh 12 항목 출력

**Subject 원문 (Chapter V, p.10)**:
> "Your script must always be able to display the following information:
> - The architecture of your operating system and its kernel version.
> - The number of physical processors.
> - The number of virtual processors.
> - The currently available RAM on your server and its utilization rate as a percentage.
> - The currently available storage on your server and its utilization rate as a percentage.
> - The current CPU utilization rate as a percentage.
> - The date and time of the last reboot.
> - Whether LVM is active or not.
> - The number of active connections.
> - The number of users using the server.
> - The IPv4 address of your server and its MAC (Media Access Control) address.
> - The number of commands executed with the sudo program."

**만족 단계**: [→ HOW E6 (스크립트 본체)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e6)
**이론 배경**: [→ WHY-21 monitoring 12 항목](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-21)
**검증 명령**:
```bash
bash /root/monitoring.sh                  # 12 개 항목 모두 출력 확인
bash /root/monitoring.sh 2>&1 | wc -l     # 줄 수 대략 12 (배너 + 12)
# 평가 단골: "스크립트 수정 없이 중지?"
crontab -e                                # 해당 줄 앞에 # 주석
```

---

# <a id="ch-vi"></a>Chapter VI — README Requirements

## <a id="what-32"></a>WHAT-32. README.md Git 루트 제출

**Subject 원문 (Chapter VI, p.12)**:
> "A **README.md** file must be provided at the **root** of your Git repository."

**만족 단계**: (Git 루트에 직접 작성, HOW 가이드 외 작업)
**이론 배경**: (문서화 규칙)
**검증**:
```bash
ls <repo_root>/README.md                  # 파일 존재 확인
```

---

## <a id="what-33"></a>WHAT-33. README 첫 줄 이탤릭 (정해진 문구)

**Subject 원문 (Chapter VI, p.12)**:
> "The very first line must be italicized and read: *This project has been created as part of the 42 curriculum by <login1>[, <login2>[, <login3>[...]]]*."

**만족 단계**: (README.md 작성)
**검증**:
```bash
head -n 1 README.md                       # *This project has been...* 형식
```

---

## <a id="what-34"></a>WHAT-34. Description / Instructions / Resources 섹션

**Subject 원문 (Chapter VI, p.12)**:
> "The README.md must include at least:
> - A **Description** section that clearly presents the project, including its goal and a brief overview.
> - An **Instructions** section containing any relevant information about compilation, installation, and/or execution.
> - A **Resources** section listing classic references related to the topic (documentation, articles, tutorials, etc.), as well as a **description of how AI was used** — specifying for which tasks and which parts of the project."

**만족 단계**: (README.md 3 섹션 작성)
**검증**:
```bash
grep -E "^## (Description|Instructions|Resources)" README.md   # 3 섹션 존재
```

---

## <a id="what-35"></a>WHAT-35. Project Description + 4 가지 비교

**Subject 원문 (Chapter VI, p.12)**:
> "A **Project description** section must also explain your choice of operating system (Debian or Rocky), including their respective advantages and disadvantages. It must describe the main design choices made during the setup (partitioning, security policies, user management, services installed) and provide a comparison between:
> - Debian vs Rocky Linux
> - AppArmor vs SELinux
> - UFW vs firewalld
> - VirtualBox vs UTM"

**만족 단계**: (README.md "Project description" 섹션)
**이론 배경**: [→ WHY-19 AppArmor vs SELinux](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-19) · [→ WHY-15 UFW](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-15) · [→ WHY-01 하이퍼바이저](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-01)
**검증**:
```bash
grep -iE "debian|rocky|apparmor|selinux|ufw|firewalld|virtualbox|utm" README.md
# 4 가지 비교 모두 등장 확인
```

---

# <a id="ch-vii"></a>Chapter VII — Bonus part

> **주의 (Subject Chapter VII, p.15, 빨간 경고)**: *"The bonus part will only be assessed if the mandatory part is **PERFECT**. Perfect means the mandatory part has been fully completed and works without any malfunctions."* → 필수 파트 완벽하지 않으면 보너스는 채점 안 됨.

## <a id="what-36"></a>WHAT-36. 파티션 구조 (bonus lsblk 예시)

**Subject 원문 (Chapter VII, p.14)**:
> "Set up the partitions correctly so that you obtain a structure similar to the one below:"
>
> 예시 lsblk (sda 30.8G / sda1 500M /boot / sda2 1K / sda5 30.3G / sda5_crypt 30.3G crypt / LVMGroup-root 10G / / LVMGroup-swap 2.3G [SWAP] / LVMGroup-home 5G /home / LVMGroup-var 3G /var / LVMGroup-srv 3G /srv / LVMGroup-tmp 3G /tmp / LVMGroup-var--log 4G /var/log)

**만족 단계**: [→ HOW C10 (LV 7 개 생성, Subject 예시 기준)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c10)
**이론 배경**: [→ WHY-09 파티션](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-09) · [→ WHY-11 LVM](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-11)
**검증 명령**:
```bash
lsblk                                     # Subject 예시와 동일한 계층 구조 확인
```

---

## <a id="what-37"></a>WHAT-37. WordPress (lighttpd + MariaDB + PHP)

**Subject 원문 (Chapter VII, p.14)**:
> "Set up a functional **WordPress** website with the following services: **lighttpd**, **MariaDB**, and **PHP**."

**만족 단계**: [→ HOW E7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e7)
**이론 배경**: [→ WHY-22 lighttpd + FastCGI](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-22) · [→ WHY-23 MariaDB](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-23) · [→ WHY-24 PHP/www-data](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-24)
**검증 명령**:
```bash
systemctl is-active lighttpd              # active
systemctl is-active mariadb               # active
php -v                                    # PHP version 출력
ufw status | grep 80                      # 80 ALLOW
# 호스트 브라우저: http://localhost:8080 → WordPress 페이지
```

---

## <a id="what-38"></a>WHAT-38. 추가 서비스 1 개 (NGINX/Apache 금지)

**Subject 원문 (Chapter VII, p.14)**:
> "Set up a service of your choice that you think is useful (**NGINX / Apache2 excluded!**). During the defense, you will have to **justify your choice**."

**만족 단계**: [→ HOW E8 (Netdata 예시)](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e8)
**이론 배경**: (서비스 선택 자유, 이론 고정 없음)
**검증 명령**:
```bash
# Netdata 예시:
systemctl is-active netdata               # active
ss -tunlp | grep 19999                    # LISTEN
# 호스트 브라우저 확인
```

---

# <a id="ch-viii"></a>Chapter VIII — Submission and peer-evaluation

## <a id="what-39"></a>WHAT-39. README.md + signature.txt 제출

**Subject 원문 (Chapter VIII, p.16)**:
> "You only need to submit the expected **README.md** file, and a **signature.txt** file at the root of your Git repository."

**만족 단계**: (Git 루트에 두 파일 commit + push)
**검증**:
```bash
ls <repo_root>/README.md <repo_root>/signature.txt
git log --oneline | head                  # 커밋 존재 확인
```

---

## <a id="what-40"></a>WHAT-40. sha1 형식 서명

**Subject 원문 (Chapter VIII, p.16)**:
> "Retrieve the signature from the '.vdi' file (or '.qcow2' for UTM users) of your virtual machine in **sha1** format.
> - Linux: `sha1sum rocky_serv.vdi`
> - 예시 출력: `6e657c4619944be17df3c31faa030c25e43e40af`"

**만족 단계**: [→ HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9)
**이론 배경**: [→ WHY-25 해시 함수 SHA-1](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-25)
**검증 명령**:
```bash
cat signature.txt                         # 40 자리 16 진수 확인
# 예: 6e657c4619944be17df3c31faa030c25e43e40af
grep -E '^[a-f0-9]{40}$' signature.txt    # 형식 검증
```

---

## <a id="what-41"></a>WHAT-41. VM 자체 Git 포함 금지

**Subject 원문 (Chapter VIII, p.16, 빨간 경고)**:
> "It is of course **FORBIDDEN** to include your virtual machine in your Git repository. During the defense, the signature of the signature.txt file will be compared with the one of your virtual machine. If the two of them are not identical, your **grade will be 0**."

**만족 단계**: [→ HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9)
**검증 명령**:
```bash
cd <repo_root>
find . -name "*.vdi" -o -name "*.vmdk" -o -name "*.ova" -o -name "*.qcow2"
# 결과 비어 있어야 정답
cat .gitignore | grep -E "\.vdi|\.vmdk"   # gitignore 등록 권장
```

---

# 평가 당일 체크리스트

무작위 순서로 훑으며 본인 VM 에서 검증 명령이 **전부 초록 OK** 인지 확인:

```bash
# OS 기본
head -n 2 /etc/os-release
hostname
getent passwd <login>
groups <login>

# AppArmor
/usr/sbin/aa-status
systemctl is-enabled apparmor

# 파티션
lsblk
vgs && lvs

# 네트워크
ss -tunlp | grep 4242
systemctl is-active ssh
grep PermitRootLogin /etc/ssh/sshd_config
/usr/sbin/ufw status

# 비번 정책
chage -l <login>
chage -l root
grep pwquality /etc/pam.d/common-password

# sudo
grep -E "Defaults" /etc/sudoers
ls -la /var/log/sudo/

# 모니터링
crontab -l
bash /root/monitoring.sh
systemctl is-enabled monitoring-startup 2>/dev/null || crontab -l | grep @reboot

# 보너스
systemctl is-active lighttpd mariadb netdata
ufw status | grep -E "4242|80|19999"
```

하나라도 실패하면 해당 WHAT-NN 로 돌아가 HOW 단계 재수행.

---

# 출처

- [Subject v5.2 원문 PDF](./_reference/en_subject_born2beroot.pdf)
- 짝 문서 ①: [`Born2beRoot_v5_2_HOW_2026-04-18.md`](./Born2beRoot_v5_2_HOW_2026-04-18.md)
- 짝 문서 ③: [`Born2beRoot_v5_2_WHY_2026-04-18.md`](./Born2beRoot_v5_2_WHY_2026-04-18.md) (Phase 3 작성)
- 한글 보조 번역: [`subject_ko_Born2beRoot.md`](./_reference/subject_ko_Born2beRoot.md)
- 번역 가이드 (그림): [`README_KR.md`](./_reference/README_KR.md)
