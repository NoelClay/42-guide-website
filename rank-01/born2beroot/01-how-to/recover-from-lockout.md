---
description: "UFW 실수로 SSH 잠김, LUKS 비번 오타, GRUB 파손, sudoers 문법 오류 등 자주 발생하는 잠김 상태 복구."
---

# 🎯 How-to — 잠김 상태에서 복구

> **문서 유형**: How-to. 증상별 레시피.
> **전제**: VBox 콘솔에 물리 접근 가능 (GUI VM 창을 직접 조작할 수 있음).

## 증상 색인

| 증상 | 섹션 |
|---|---|
| `ufw enable` 후 SSH 연결 차단 | [UFW 자기 잠김](#ufw-self-lockout) |
| LUKS 패스프레이즈 입력 프롬프트에서 막힘 | [LUKS 비번 오타](#luks-typo) |
| GRUB 메뉴 실종 / `grub rescue>` 프롬프트 | [GRUB 복구](#grub-recovery) |
| `sudoers` 편집 후 `sudo` 명령 전부 실패 | [sudoers 구원](#sudoers-rescue) |
| 비번 정책 강화 후 본인 비번 거부 | [PAM 우회](#pam-bypass) |
| `crontab` wall 메시지 안 보임 | [cron 침묵](#cron-silent) |
| signature.txt 와 .vdi 해시 불일치 | [서명 재생성](#signature-mismatch) |
| `apt` 의존성 충돌 (lighttpd ↔ apache2) | [apt 충돌](#apt-conflict) |

---

## <a id="ufw-self-lockout"></a>UFW 자기 잠김

**원인**: `ufw default deny incoming` 후 `ufw allow 4242` 전에 `ufw enable` 했거나, `ufw allow 22` 가 아닌 `4242` 만 열어두고 아직 SSH 가 4242 로 바꾸기 전.

**복구**: VBox 콘솔로 직접 로그인해:

```bash
sudo ufw allow 4242/tcp
sudo ufw reload
sudo systemctl status ssh
```

또는 완전 초기화:

```bash
sudo ufw reset
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 4242/tcp
sudo ufw enable
```

---

## <a id="luks-typo"></a>LUKS 패스프레이즈 오타

**증상**: 부팅 직후 `Please unlock disk sda5_crypt:` 에서 비번 여러 번 거부.

**복구**:
1. 입력 중 `Backspace` 가 터미널에 따라 다르게 작동 — 한 번에 타이핑 완료 후 `Enter`.
2. CapsLock, 한/영 상태, 키보드 레이아웃(US vs KR) 확인.
3. 너무 여러 번 틀리면 **몇 초 딜레이** 가 들어감. 천천히 재시도.

**완전 분실 시**: LUKS 의 8 개 키슬롯 중 하나만 알면 다른 키슬롯 추가 가능:

```bash
# 초기 설치 때 키슬롯 0 에 패스프레이즈 저장됨
# 추가 키슬롯에 두 번째 비번 만들기 (로그인 상태에서 미리 대비)
sudo cryptsetup luksAddKey /dev/sda5
```

**키 전부 분실**: 데이터 복구 불가. 재설치.

---

## <a id="grub-recovery"></a>GRUB 복구 (`grub rescue>` 프롬프트)

**원인**: MBR 파손, `update-grub` 도중 커널 업데이트 실패.

**복구 절차** (Live CD 또는 Debian ISO 의 rescue 모드 필요):

1. VBox 에서 VM Settings → Storage → ISO 를 다시 `debian-12.x.0-amd64-netinst.iso` 로 교체.
2. VM 부팅 → Boot menu 에서 `Advanced options` → `Rescue mode`.
3. Rescue 메뉴 → root 파일시스템 마운트 선택 → `/dev/mapper/<vg>-root` 선택 (LUKS 비번 입력).
4. `Reinstall GRUB bootloader` 선택 → `/dev/sda` 지정.
5. `Reboot`.

자세히 → [부팅 순서](../03-explanation/boot-sequence.md)

---

## <a id="sudoers-rescue"></a>sudoers 구원

**절대 하지 말 것**: `nano /etc/sudoers` 로 직접 편집. 문법 오류 시 sudo 전체 잠김.

**증상**: `sudo: /etc/sudoers: syntax error near line N` → 모든 sudo 명령 실패.

**복구**:
1. VBox 콘솔에 root 로 직접 로그인 (사용자 비번 아님):
   ```
   login: root
   Password: <root 비번>
   ```
2. `/etc/sudoers` 또는 `/etc/sudoers.d/born2beroot` 를 `visudo` 로 다시 열어 문법 복원:
   ```bash
   visudo -f /etc/sudoers.d/born2beroot
   ```
3. 저장 전 문법 검증:
   ```bash
   visudo -c
   ```

**예방책**: 항상 `visudo` 사용. `/etc/sudoers` 자체는 건드리지 말고 `/etc/sudoers.d/born2beroot` drop-in 만 편집.

---

## <a id="pam-bypass"></a>PAM 우회

**증상**: pwquality 설정 변경 후 `passwd` 가 기존 비번도 거부.

**복구**: root 로 로그인 후 pam_pwquality 모듈이 아닌 `root` 는 `enforce_for_root` 가 없으면 검사 우회:

```bash
# root 로 강제 비번 변경 (pwquality 통과)
passwd <username>
# 또는 임시로 common-password 에서 pwquality 라인 주석화 후 변경, 다시 복원
```

---

## <a id="cron-silent"></a>cron 에서 wall 메시지 안 보임

**가장 흔한 원인 (Debian 13+)**: `wall` 이 `setgid tty` 를 상실 → SSH pts 에 메시지 미도달.

**확인**:

```bash
ls -l /usr/bin/wall
# -rwxr-xr-x  ← setgid 없음 (Debian 13+)
# -rwxr-sr-x  ← setgid tty (Debian 12 이하)
```

**대처**:
1. VBox 콘솔(TTY1) 로그인 상태에서는 정상 표시 → 평가자에게 안내.
2. 또는 monitoring 출력을 파일로도 기록:
   ```
   */10 * * * *   /bin/bash /root/monitoring.sh | tee -a /var/log/monitoring.log | wall
   ```

**그 외 원인**:
- `crontab -l` 에 행이 있지만 스크립트 경로 오타 → `journalctl -u cron` 로 확인.
- `/root/monitoring.sh` 실행 권한 없음 → `chmod +x`.
- 스크립트 첫 줄 `#!/bin/bash` 없음 → 추가.

---

## <a id="signature-mismatch"></a>signature.txt 불일치

**복구**:

```bash
# 호스트 (VM 완전 종료 상태)
cd "~/VirtualBox VMs/born2beroot"
sha1sum *.vdi
# 출력 해시를 signature.txt 에 다시 기록

cd ~/your-repo
echo "<새 해시>" > signature.txt
git add signature.txt
git commit -m "Update signature"
git push
```

**주의**: VM 을 켜거나 스냅샷을 찍으면 `.vdi` 가 변경되어 해시가 다시 바뀝니다. **서명 생성 후에는 VM 을 켜지 마세요** (평가 당일 잠시 켜는 것은 평가자 양해).

---

## <a id="apt-conflict"></a>apt 의존성 충돌

**증상**: `lighttpd` 설치하려는데 `apache2` 이미 있어서 포트 80 충돌.

**복구**:

```bash
sudo systemctl stop apache2
sudo apt purge apache2 apache2-bin apache2-data apache2-utils -y
sudo apt autoremove -y
sudo apt install lighttpd -y
sudo systemctl status lighttpd
```

**Subject 제약 재확인**: SC-38 = NGINX / Apache2 **금지**. `apache2` 가 있으면 평가 0 점.

---

## 예방 원칙 (모든 작업 전 적용)

1. **위험한 변경 전 snapshot** (평가 당일 아니면):
   - VBox → Snapshots 탭 → `[Take]` → 날짜 라벨. 평가 전에는 모두 삭제.
2. **sudoers/PAM 편집 전 백업**:
   ```bash
   sudo cp /etc/sudoers.d/born2beroot /root/sudoers.bak
   sudo cp /etc/pam.d/common-password /root/pam.bak
   ```
3. **새 터미널 예약**: 위험한 변경 전 **이미 로그인된 SSH 세션 하나를 열어둠** — 잠기더라도 그 세션으로 복구.
4. **UFW 순서**: 반드시 `allow 4242` → `enable` 순서. 반대는 자기 잠김.

## 관련 문서

- [Subject v5.2 조항](../02-reference/subject-v5.2-clauses.md)
- [설정 파일 스펙](../02-reference/config-files.md)
- [peer-eval-protocol](../02-reference/peer-eval-protocol.md)
- [부팅 순서](../03-explanation/boot-sequence.md)
