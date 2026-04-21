---
description: "30 일 만료 정책에 의해 비번을 정기 교체하는 절차. root·일반 사용자·평가 직전 체크."
---

# 🎯 How-to — 비밀번호 로테이션

> **문서 유형**: How-to. 30 일 만료 대응과 평가 직전 비번 갱신.
> **전제**: PAM pwquality + `/etc/login.defs` 설정 완료. chage 30/2/7 적용 됨.

## 언제 써야 하나

- 설치 후 29~30 일째 자동 만료 경고
- 평가 직전 "비번이 너무 오래됨" 우려
- 비번 유출 의심

---

## 1. 현재 만료 상태 확인

```bash
sudo chage -l <login>
sudo chage -l root
```

출력 예:
```
Last password change            : Mar 10, 2026
Password expires                : Apr 09, 2026   ← 만료 예정일
Password inactive               : never
Account expires                 : never
Minimum number of days ... 2
Maximum number of days ... 30
Warning before expiry ... 7
```

## 2. 일반 사용자 비번 교체

```bash
passwd <login>
```

**주의사항**:
- pwquality 로 거부될 수 있음 (`minlen=10`, 사용자명 포함 금지, 대/소/숫자 강제).
- `difok=7` 로 이전 비번 대비 7 자 이상 달라야 함.
- `SC-22` 에 따라 difok 는 root 예외 — 일반 사용자는 엄격 적용.

**통과 패턴 예시**:
```
기존: Test2026Pass!
신규: Spring2027Fresh!   ← 겹치는 글자 적음 (difok ≥ 7)
```

## 3. root 비번 교체

```bash
sudo passwd root
```

또는 root 로 전환 후:

```bash
su -
passwd
```

**주의**: `enforce_for_root` 가 적용되어 있으면 pwquality 검증 통과 필요. 단 `difok` 는 root 미적용.

## 4. 만료 정책 재적용

만료일이 과거로 밀려난 경우 (예: 설치 직후가 아니라 오늘이 Mar 15 인데 Last change 가 Feb 15 로 남은 경우):

```bash
sudo chage -d $(date +%Y-%m-%d) <login>
```

이렇게 하면 Last change 가 오늘로 갱신 → 만료일이 오늘 + 30 일로 이동.

또는 완전 초기화:

```bash
sudo chage -M 30 -m 2 -W 7 -d $(date +%Y-%m-%d) <login>
sudo chage -M 30 -m 2 -W 7 -d $(date +%Y-%m-%d) root
```

## 5. 검증

```bash
sudo chage -l <login>
```

- `Last password change` = 오늘
- `Password expires` = 오늘 + 30 일

## 평가 직전 체크리스트

```bash
# 두 계정 모두 정책 준수 + 최근 변경
for u in <login> root; do
  echo "=== $u ==="
  sudo chage -l $u | grep -E "Last|expire|Max|Min|Warn"
done
```

기대 출력 (두 계정 동일):
```
Last password change            : Apr 20, 2026
Password expires                : May 20, 2026
Password inactive               : never
Account expires                 : never
Minimum number of days ... 2
Maximum number of days ... 30
Warning before expiry ... 7
```

## 비번 정책 장단점 (평가자 Q&A 용)

- **장점**: 유출 시 피해 노출 기간 제한 · 사전/무차별 공격 내성 · 사용자명 포함 금지로 social engineering 내성.
- **단점**: 사용자가 같은 패턴 + 숫자만 증가 (`Pass2026`, `Pass2027`) 로 우회 · 짧은 주기로 포스트잇 저장 유발 · MFA 없이는 제한적.

자세히 → [접근 제어](../03-explanation/access-control.md)

## 관련 문서

- [Subject v5.2 SC-17 ~ SC-24](../02-reference/subject-v5.2-clauses.md#chapter-v)
- [설정 파일: /etc/login.defs](../02-reference/config-files.md#etc-login-defs)
- [PAM 우회 (잠김 복구)](recover-from-lockout.md#pam-bypass)
