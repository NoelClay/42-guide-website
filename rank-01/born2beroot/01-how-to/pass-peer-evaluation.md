---
description: "peer evaluation 당일 평가자의 7 개 게이트를 모두 통과하기 위한 시연 플레이북."
---

# 🎯 How-to — Peer evaluation 통과

> **문서 유형**: How-to (목표 지향). 이미 VM 을 설치한 사용자가 평가 당일 사용.
> **전제**: [튜토리얼](../00-tutorial/install-your-first-vm.md) 완료 · signature.txt 제출 · 스냅샷 0 개.
> **대응 Reference**: [peer-eval-protocol](../02-reference/peer-eval-protocol.md)

## 평가 30 분 전 최종 점검

```bash
# VM 전원 OFF 상태에서 호스트에서:
cd "~/VirtualBox VMs/born2beroot"
sha1sum *.vdi
cat ~/your-repo/signature.txt
# 두 해시가 같은지 diff

# VBox Snapshots 탭에 항목이 없는지 확인
```

불일치하면 [잠김 복구](recover-from-lockout.md#signature-mismatch) 참고.

VM 부팅 후 root 로 전환:

```bash
su -
# 복사-붙여넣기 가능한 위치에 VBox 창 고정
```

---

## Gate 1: 예비 테스트

평가자가 `git clone` → `diff` 로 서명 비교합니다. **당신이 할 일**: 평가자가 요청할 경우 VM 을 다른 디렉토리 복사본으로 부팅 가능함을 보여주기.

---

## Gate 2: README.md 4 비교 발표 (3 분)

준비해야 할 문장 4 쌍 — 각 비교는 **장점·단점·본 과제 선택** 을 한 문장씩:

| 비교 | 준비 문장 핵심 |
|---|---|
| **Debian vs Rocky** | Debian 은 APT 생태계가 크고 학습 자료 풍부. Rocky 는 RHEL 호환으로 엔터프라이즈에 가까움. 본 과제는 입문자 친화 이유로 Debian. |
| **AppArmor vs SELinux** | AppArmor 는 path 기반으로 설정 쉬움. SELinux 는 label 기반으로 더 정교하지만 학습 부담. Debian 기본 = AppArmor. |
| **UFW vs firewalld** | UFW 는 iptables 위 얇은 래퍼, 룰이 직관적. firewalld 는 zone 기반으로 다중 인터페이스에 유리. 본 과제 = UFW. |
| **VirtualBox vs UTM** | VirtualBox 는 x86 Linux/Windows 표준, UTM 은 Apple Silicon 에서 QEMU 기반. 본 과제 Mac ARM 사용자가 아니면 VirtualBox. |

자세히 → [설계 결정 ADR](../03-explanation/design-decisions.md)

---

## Gate 3: 프로젝트 개요 (구두 질문 7 개)

각 질문별 30 초 답변 템플릿:

### Q1. VM 이 어떻게 동작하는가?
하이퍼바이저(Type 2 VirtualBox)가 호스트 OS 위에서 게스트에게 가상 CPU·메모리·디스크를 할당. 게스트 OS 는 이를 실제 하드웨어로 인식.

### Q2. OS 선택 이유
Debian 12 Bookworm — 최신 안정판, APT 생태계, AppArmor 기본, 시스템 관리 입문에 적합.

### Q3. Rocky vs Debian
Rocky = RHEL 호환 (DNF, SELinux 기본, 엔터프라이즈용). Debian = 독립 커뮤니티 (APT, AppArmor 기본, 데스크탑~서버 범용).

### Q4. VM 의 목적
Subject 의 시스템 관리 요구사항 (암호화 LVM, SSH, UFW, sudo, PAM, monitoring) 을 구현해 Linux 시스템 구성 능력을 증명.

### Q5. AppArmor
커널 MAC (Mandatory Access Control) 모듈. 프로그램 **경로 기반** 으로 파일·기능 접근을 프로필로 제한. Subject SC-06 이 시작 시 작동 강제.

### Q6. aptitude vs apt ⚠️ (Debian 선택 시 평가자 단골 질문)
- **apt** = Debian 의 현대 표준 CLI. `apt update`, `apt install`, `apt upgrade` 등 단순한 동사.
- **aptitude** = TUI(텍스트 UI) + 의존성 해결 전략이 다른 구형 프론트엔드. 복잡한 의존성 충돌 해결에는 더 공격적.
- 둘 다 내부적으로 `dpkg` 를 호출. 현재 Debian 은 `apt` 권장.

### Q7. 10 분 monitoring 확인
평가자와 함께 10 분 기다리며 `wall` 브로드캐스트 메시지가 TTY1 에 나타나는지 확인.

> **Debian 13 주의**: `wall` 이 setgid tty 를 박탈당해 SSH pts 세션에 안 뜸. 평가자는 VBox 콘솔(TTY1) 로 확인해야 함. 평가자에게 미리 안내.

---

## Gate 4: 간단한 설정 확인 (한 번에 검증)

VBox 콘솔에 다음을 복붙:

```bash
systemctl is-active ufw ssh apparmor
cat /etc/os-release | head -n 2
```

모두 `active` + Debian 12 표시.

---

## Gate 5: 사용자 단계 1·2 시연

### 시연 A. 새 사용자 생성 + 약한 비번 거부 시연

```bash
sudo adduser testeval
# 비번: "123"   ← 거부 (BAD PASSWORD) 보여주기
# 비번: "TestEval2026!"  ← 통과
# 이름 등 나머지는 Enter 로 skip
```

**설명 문장**: "pam_pwquality 가 `minlen=10 ucredit=-1 lcredit=-1 dcredit=-1` 로 설정되어 있어, 10 자 미만이거나 대/소/숫자 부족 시 거부합니다. 설정 파일은 `/etc/pam.d/common-password` 입니다."

### 시연 B. "evaluating" 그룹 즉석 생성

```bash
sudo groupadd evaluating
sudo usermod -aG evaluating testeval
getent group evaluating
groups testeval
```

출력에 `evaluating` 포함 확인.

### 설명: 비번 정책 장단점

- **장점**: 사전 공격·무차별 대입 내성 + 정기 교체로 탈취 피해 기간 제한.
- **단점**: 강한 비번 강제 → 포스트잇에 적거나 비번 재사용 유발 가능. 30 일 주기 → 사용자 피로. 실무는 MFA 병행.

---

## Gate 6: 호스트 이름 + 파티션 시연

### 호스트 이름 변경 → 재시작 → 복원

```bash
# 현재 확인
hostname
hostnamectl

# 변경 (평가자 login + 42 로)
sudo hostnamectl set-hostname evaluator42
hostname

# 재시작
sudo reboot
```

재부팅 후 로그인하면 프롬프트가 `<login>@evaluator42` 로 바뀐 것을 보여줍니다.

```bash
# 원래대로 복원
sudo hostnamectl set-hostname <login>42
```

### 파티션 확인

```bash
lsblk
```

평가자와 함께 Subject Chapter VII 의 예시와 비교:
- `sda1` = `/boot`
- `sda5` = LUKS `crypt`
- LVM 안에 `root, swap, home, var, var-log, srv, tmp` 7 개 LV

### LVM 설명 (30 초)

"LVM 은 PV(Physical Volume, 예: `sda5_crypt`) 를 묶어 VG(Volume Group) 를 만들고, 그 안에서 LV(Logical Volume) 를 잘라내 가상 파티션처럼 사용합니다. 장점은 `lvextend` 로 실행 중 크기 변경 가능, 스냅샷 가능. 전통 파티션 테이블의 경직성을 극복."

자세히 → [저장 스택](../03-explanation/storage-stack.md)

---

## Gate 7: sudo · UFW · SSH · monitoring 시연

### sudo

```bash
# 새 사용자를 sudo 그룹에 추가 (시연용)
sudo usermod -aG sudo testeval
groups testeval

# 로그 확인
ls -la /var/log/sudo/
sudo cat /var/log/sudo/sudo.log | tail -10

# sudo 오인증 3 회 시연
sudo -k && sudo ls       # 틀린 비번 3 회 → "WRONG PASSWORD" 메시지 나옴
```

### UFW 8080 추가·삭제 시연

```bash
# 현재 상태
sudo ufw status numbered

# 8080 규칙 추가
sudo ufw allow 8080/tcp
sudo ufw status numbered   # "8080/tcp  ALLOW" 추가된 것 확인

# 삭제 (번호 또는 규칙명)
sudo ufw delete allow 8080/tcp
sudo ufw status numbered   # 다시 4242 만 남은 것 확인
```

### SSH 새 사용자 로그인 시연

평가자가 자기 노트북에서 (또는 호스트에서):

```bash
# 호스트에서 VM 으로 (포트 포워딩 2222 → 4242 가정)
ssh testeval@localhost -p 2222

# root 접속 시도 → 실패 확인
ssh root@localhost -p 2222
# "Permission denied" 또는 "root login not allowed" 메시지
```

### monitoring 매분 변경 → 수정 없이 중지 → 재시작 검증

```bash
# 매분으로 변경
sudo crontab -e
# */10 → * 으로 수정 (즉 */10 * * * *  →  * * * * *)

# 1 분 기다리며 wall 메시지 확인

# 수정 없이 중지 (스크립트 자체는 건드리지 않음)
sudo crontab -e
# 해당 줄 앞에 # 붙여 주석화

# 재시작
sudo reboot
```

재부팅 후:

```bash
ls -la /root/monitoring.sh       # 존재 + 권한 유지
stat /root/monitoring.sh         # Modify 시간이 처음 작성 시점과 같음
```

재연결 후 평가자와 함께 **wall 메시지가 나타나지 않음** 을 확인 → 중지 성공.

원복하려면:

```bash
sudo crontab -e
# 주석 # 제거
```

---

## 🔴 레드 플래그 회피 체크리스트

이 중 하나라도 해당되면 **즉시 0점**:

- [ ] GUI (X.org, GNOME, XFCE 등) 설치됨 — `dpkg -l | grep xserver` 출력 없어야 함
- [ ] signature.txt 와 .vdi 해시 불일치
- [ ] Git 저장소에 `.vdi`/`.vmdk`/`.ova`/`.qcow2` 파일 포함
- [ ] 스냅샷 존재
- [ ] SSH root 로그인 허용
- [ ] 4242 외 포트 열림

## 평가 종료 후

```bash
# testeval 사용자 정리 (다음 평가자가 이걸 보면 혼란)
sudo deluser --remove-home testeval
sudo groupdel evaluating
# monitoring crontab 복원 (주석 제거)
sudo crontab -e
```

## 관련 문서

- [peer-eval-protocol](../02-reference/peer-eval-protocol.md) (평가지 구조화)
- [Subject v5.2 조항](../02-reference/subject-v5.2-clauses.md)
- [잠김 복구](recover-from-lockout.md)
- [설계 결정 ADR](../03-explanation/design-decisions.md)
