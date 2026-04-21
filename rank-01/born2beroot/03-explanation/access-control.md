---
description: "root·sudo, 그룹, PAM 과 pwquality, AppArmor MAC, 그리고 aptitude vs apt."
---

# 💡 Explanation — 접근 제어

> **문서 유형**: Explanation. Born2beRoot 의 권한·인증·강제 정책 배경.

## root 와 sudo

Linux 권한 모델 = **DAC** (Discretionary Access Control) — 파일 소유자가 접근 권한 결정.

**root** (UID 0) = 거의 모든 권한 검사 우회. 직접 상시 작업은 **모든 실수가 즉시 시스템 파괴**.

**sudo** = `/etc/sudoers` 에 허용된 사용자가 비밀번호 인증 후 root 권한으로 특정 명령 실행 + **전체 호출·입력·출력 로그**.

Born2beRoot 의 엄격 sudo Defaults:
- `passwd_tries=3` — 오인증 3 회 후 종료
- `badpass_message` — 커스텀 오류 문구
- `logfile + log_input + log_output + iolog_dir` — `/var/log/sudo/` 에 완전 기록
- `requiretty` — TTY 없는 데몬/웹서버로부터의 sudo 호출 차단
- `secure_path` — `PATH` 변수 주입 공격 차단

### 30 초 답변
"Linux 는 DAC 기반이며, root(UID 0) 는 사실상 모든 권한을 가집니다. sudo 는 sudoers 에 허용된 사용자에게 비번 재인증 후 일시적으로 root 권한을 부여하고 모든 동작을 `/var/log/sudo/` 에 기록합니다. Born2beRoot 는 `requiretty`, `secure_path`, I/O 로깅 등 엄격 옵션으로 설정합니다."

### 더 깊이
- `"sudo vs su differences"`
- `"sudoers Defaults options reference"`
- `"Linux DAC vs MAC access control"`

---

## 그룹과 getent

**그룹** = 사용자 묶음, GID 식별. `/etc/group`:

```
groupname:x:GID:member1,member2,...
```

사용자는 **primary group** (`/etc/passwd` 의 gid) 1 개 + **supplementary groups** 여러 개.

파일 권한 `rwxrwxrwx` 의 가운데 3 자리가 그룹 멤버십 기반.

`getent group sudo` = **NSS** (Name Service Switch) 를 통해 조회 — `/etc/group` + LDAP + SSSD 등 여러 소스 통합.

Born2beRoot 의 사용자 소속:
- `<login>` → primary: `<login>`, supplementary: `sudo`, `user42`
- 평가 시연 시 임시로 `evaluating` 그룹 추가

`usermod -aG <group>` 의 `-a` (append) 가 **매우 중요** — 없으면 **기존 supplementary 그룹이 전부 삭제** 됨.

---

## PAM 과 pwquality

**PAM** (Pluggable Authentication Modules) = 인증 관련 작업을 모듈로 분리한 프레임워크.

`/etc/pam.d/<service>` 에 **스택** 정의. 4 타입:
- `auth` — 사용자 신원 확인
- `account` — 계정 유효성 (만료 등)
- `password` — 비밀번호 변경 검증
- `session` — 로그인/로그아웃 셋업

각 라인의 **Control flag**:
- `requisite` — 실패 즉시 전체 종료
- `required` — 실패해도 스택 끝까지 진행 후 실패
- `sufficient` — 성공 시 즉시 통과

**pam_pwquality.so** = 비밀번호 품질 검사 모듈. Born2beRoot 옵션:

| 옵션 | 효과 |
|---|---|
| `minlen=10` | 최소 길이 |
| `dcredit/ucredit/lcredit=-1` | 각 문자 클래스 최소 1 자 강제 (음수=강제, 양수=가산점) |
| `difok=7` | 이전 비번 대비 다른 글자 수 |
| `maxrepeat=3` | 같은 문자 연속 초과 금지 |
| `reject_username` | 비번에 사용자명 포함 금지 |
| `enforce_for_root` | root 에도 적용 (단 `difok` 는 pwquality 내부 로직상 root 예외) |

`/etc/login.defs` 는 **별개 영역** — `PASS_MAX_DAYS`, `PASS_MIN_DAYS`, `PASS_WARN_AGE` 등 **만료 정책**. `chage` 는 기존 계정에 정책을 덮어씀.

### 30 초 답변
"PAM 은 인증을 모듈 스택으로 추상화하는 프레임워크입니다. `/etc/pam.d/common-password` 에 pam_pwquality.so 를 requisite 로 걸면, 모든 비밀번호 변경이 그 조건(`minlen`, 문자 클래스, `difok`) 을 통과해야 합니다. 만료 정책은 별도로 `/etc/login.defs` 와 `chage` 로 관리합니다."

---

## AppArmor vs SELinux (MAC)

둘 다 **MAC** (Mandatory Access Control) — DAC(사용자 설정) 와 달리 **시스템이 강제** 하는 접근 제어.

**AppArmor** (Ubuntu/Debian 기본):
- **Path-based** profile. `/etc/apparmor.d/`
- 예: `/usr/sbin/sshd` 가 `/etc/ssh/**` 만 읽기 가능
- 설정이 직관적 (파일 경로 패턴 + 허용 동작)

**SELinux** (RHEL/Rocky/Fedora 기본):
- **Label-based**. 모든 객체(파일·프로세스·소켓)에 `user:role:type:level` 라벨
- Type Enforcement (TE) 로 type 간 관계 규정
- 모드: `enforcing` / `permissive` / `disabled`

Subject 는 Debian 에서 **AppArmor 시작 시 작동** 강제 (SC-06).

### 30 초 답변
"AppArmor 와 SELinux 는 둘 다 MAC 모듈로, 프로그램이 접근 가능한 자원을 커널 레벨에서 제한합니다. AppArmor 는 파일 경로 기반이라 설정이 쉽고, Ubuntu/Debian 기본입니다. SELinux 는 라벨 기반으로 더 정교하지만 학습 부담이 크고, RHEL/Rocky 기본입니다."

### 더 깊이
- `"AppArmor vs SELinux comparison"`
- `"MAC vs DAC Linux security"`
- `"SELinux type enforcement policy"`

---

## aptitude vs apt (Debian 단골 질문)

**dpkg** = Debian 저수준 패키지 관리 도구. `.deb` 파일 하나를 설치·제거.

**APT** (Advanced Package Tool) = dpkg 위의 상위 도구군. 의존성 해결 + 저장소 관리.

**apt** 명령 (현대 권장):
- `apt update`, `apt install`, `apt upgrade`, `apt remove`
- CLI 전용, 간단한 동사
- 컬러 출력, 진행률 표시
- Debian 8+ (Jessie) 부터 표준

**aptitude** (구형 대안):
- TUI(ncurses 기반) + CLI 모두 제공
- 의존성 충돌 해결 전략이 apt 보다 **공격적** (여러 해결안 제시)
- 2000 년대 초반 주력, 지금은 비주류

**둘 다 내부적으로 dpkg 를 호출**. 일상 작업은 `apt` 권장, 복잡한 dependency hell 에서 `aptitude` 가 도움.

### 30 초 답변
"apt 는 Debian 의 현대 표준 CLI 패키지 관리 명령입니다 — `apt install`, `apt upgrade` 등. aptitude 는 더 오래된 대안으로 TUI 도 제공하고 의존성 해결이 apt 보다 공격적입니다. 둘 다 내부적으로 dpkg 를 호출하며, 현재는 apt 가 권장됩니다."

### 더 깊이
- `"apt vs aptitude vs dpkg"`
- `"APT dependency resolution"`
- `"Debian package management explained"`

---

## 관련 문서

- [튜토리얼 E5 (PAM)·E6 (비번 로테이션)](../00-tutorial/install-your-first-vm.md)
- [비번 로테이션](../01-how-to/rotate-passwords.md)
- [설정 파일: common-password, login.defs, sudoers](../02-reference/config-files.md)
- [설계 결정 ADR-002 (AppArmor vs SELinux)](design-decisions.md#adr-002-apparmor-over-selinux)
