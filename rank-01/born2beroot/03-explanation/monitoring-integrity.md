---
description: "cron 과 wall, monitoring.sh 12 항목, SHA-1 해시, VirtualBox 스냅샷 CoW."
---

# 💡 Explanation — 모니터링 · 무결성

> **문서 유형**: Explanation. monitoring 자동화와 서명·스냅샷 이론.

## cron 과 wall

**cron** = 주기 작업 스케줄러 데몬.

작업 등록 위치:
| 위치 | 용도 |
|---|---|
| `crontab -e` → `/var/spool/cron/crontabs/<user>` | 사용자별 |
| `/etc/cron.d/<file>` | 시스템용 (패키지 표준) |
| `/etc/cron.{hourly,daily,weekly,monthly}/` | 시간대별 drop-in |
| `/etc/crontab` | 시스템 전역, user 필드 포함 |

crontab 형식:
```
m  h  dom  mon  dow  command
```

- `*/10 * * * *` — 10 분마다
- `* * * * *` — 매분
- `@reboot` — 부팅 직후 1 회

**wall** (write all) = `/var/run/utmp` 의 활성 터미널 사용자들에게 메시지 브로드캐스트.

```bash
echo "hello all" | wall
```

Born2beRoot 의 `monitoring.sh | wall` = 스크립트 출력을 모든 TTY 에 뿌림.

### Debian 13 `wall` 변경
setgid tty 박탈 → **SSH pts 세션에 메시지 미도달**. TTY1 같은 직접 콘솔만 수신. 평가 시 주의 사항.

### 30 초 답변
"cron 은 Linux 의 주기 작업 스케줄러로, `crontab` 으로 분·시·일·월·요일 단위 스케줄을 등록합니다. `@reboot` 은 부팅 직후 1 회 실행됩니다. wall 은 로그인된 모든 TTY 에 메시지를 뿌리는 유틸리티로, monitoring 출력을 여기 파이프해 전체 사용자에게 알립니다."

---

## monitoring 12 항목 (왜 이 조합?)

Subject SC-31 의 12 항목은 서버 운영자가 **실시간 알아야 할 최소 지표** 를 4 카테고리로 커버:

| 카테고리 | 항목 | 명령 |
|---|---|---|
| 정체성 | 아키텍처 + 커널 | `uname -a` |
| 정체성 | 물리 CPU 수 | `grep "physical id" /proc/cpuinfo \| sort -u \| wc -l` |
| 정체성 | 가상 CPU 수 | `nproc` |
| 정체성 | LVM 활성 여부 | `lsblk \| grep -c lvm` |
| 자원 | RAM 사용률 % | `free -m` |
| 자원 | Disk 사용률 % | `df -h --total` |
| 자원 | CPU 부하 % | `top -bn1` |
| 시간 | 마지막 부팅 | `who -b` |
| 활동 | TCP ESTABLISHED 수 | `ss -t \| grep -c ESTAB` |
| 활동 | 로그인 사용자 수 | `who \| wc -l` |
| 활동 | IPv4 + MAC | `hostname -I`, `ip link` |
| 활동 | sudo 실행 횟수 | `journalctl _COMM=sudo \| grep -c COMMAND` |

**10 분 주기** = 1 분은 너무 잦음 (로그 범람), 1 시간은 너무 느림 (감지 지연) 의 중간. 산업 표준 경량 헬스 체크.

### 더 깊이
- `"bash sysadmin monitoring script example"`
- `"/var/log/auth.log sudo count"`
- `"Linux system health metrics"`

---

## SHA-1 해시 함수

**암호학적 해시 함수** 의 3 가지 성질:
1. **preimage resistance**: 출력 → 입력 역산 불가
2. **second-preimage resistance**: 특정 입력의 동일 해시 다른 입력 찾기 불가
3. **collision resistance**: 임의 두 입력의 같은 해시 찾기 불가

**SHA-1** = 160-bit 출력 (40 자 16 진수).

**2017 Google "SHAttered" 공격** 으로 충돌 데모 → 암호학적 신뢰 하락. Git, TLS 서명 등 보안 중요 용도는 SHA-256 이상 권장.

**그러나** — Born2beRoot 의 용도는 **단순 무결성 확인** (내가 낸 `.vdi` == 평가자가 받은 `.vdi`). 고의적 충돌 공격이 없는 환경이므로 SHA-1 여전히 충분. Subject 가 `sha1sum` 지정.

### 30 초 답변
"SHA-1 은 160-bit 암호학적 해시로, 입력의 지문 역할을 합니다. 2017 SHAttered 공격으로 충돌이 발견되었지만, 우리의 용도는 '제출 파일과 평가 파일이 동일한가' 확인이라 충분합니다. Subject 가 `sha1sum` 을 지정한 이유입니다."

### 더 깊이
- `"SHA-1 SHAttered collision attack"`
- `"cryptographic hash function properties"`
- `"sha1sum vs sha256sum"`

---

## VirtualBox 스냅샷 (Copy-on-Write)

**snapshot** = 디스크 + 메모리 상태 시점 보존.

동작 원리:
1. 스냅샷 찍는 순간의 `.vdi` 를 **immutable base** 로 고정
2. 이후 모든 변경은 **differencing `.vdi`** (`xxx-Snapshot-yyy.vdi`) 에 기록
3. **삭제** = differencing 을 base 로 merge
4. **복원** = differencing 을 버리고 base 로 되돌림

Git branch · btrfs CoW 와 유사 개념.

### Subject 정책 (SC-03)
평가 시작 시 스냅샷 **없음** 강제 — `.vdi` 해시 일치성 검증을 위해. 평가 중 보험으로 찍는 건 허용되나 평가 끝나면 삭제.

### 왜 이게 중요한가
- 스냅샷이 있으면 `.vdi` 파일 자체는 immutable base 고정 → 해시는 일치할 수 있지만 실제 실행 중인 상태는 differencing 이 적용된 다른 상태 → Subject 는 이걸 "제출물과 시연이 다름" 으로 간주.

### 30 초 답변
"VirtualBox 스냅샷은 Copy-on-Write 방식으로, 원본 `.vdi` 를 immutable 로 두고 변경분만 별도 파일에 기록합니다. Subject 가 평가 시작 시 스냅샷을 금지하는 이유는, `.vdi` 해시가 일치해도 실제 실행은 differencing 에 의해 달라질 수 있기 때문입니다."

### 더 깊이
- `"VirtualBox snapshot differencing disk"`
- `"copy-on-write filesystem btrfs"`
- `"VDI merge snapshot"`

---

## 관련 문서

- [튜토리얼 E7 (monitoring)·E9 (서명)](../00-tutorial/install-your-first-vm.md#e7-monitoring-sh)
- [cron 침묵 복구](../01-how-to/recover-from-lockout.md#cron-silent)
- [서명 재생성](../01-how-to/recover-from-lockout.md#signature-mismatch)
- [Subject SC-30·31·40·03](../02-reference/subject-v5.2-clauses.md)
