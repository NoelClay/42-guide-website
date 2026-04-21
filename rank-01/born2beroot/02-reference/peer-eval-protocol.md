---
description: "42 intra 에 등록된 peer evaluation 양식의 구조적 요약. 평가자-학생 상호작용의 전 단계를 조항화."
---

# 📚 Reference — Peer Evaluation Protocol

> **문서 유형**: Reference (정보 지향). 평가지 PDF 의 각 섹션을 조건 · 평가자 행동 · 학생 예상 응답 · 실패 결과 로 분해.
> **원본**: [`Intra Projects Born2beroot Edit (1).pdf`](../_reference/Intra%20Projects%20Born2beroot%20Edit%20%281%29.pdf) (intra URL: `https://projects.intra.42.fr/scale_teams/.../edit`)
> **법칙**: 7 개 "예/아니요" 게이트 모두 AND. 하나라도 "아니요" → 즉시 종료, 점수 0.

## 게이트 요약

| PE# | 섹션 | 실패 시 결과 |
|---|---|---|
| [PE-G1](#pe-g1) | 예비 테스트 (signature 일치 + 스냅샷 없음) | 평가 중단 |
| [PE-G2](#pe-g2) | 일반 지침 + README.md 확인 | 평가 중단 |
| [PE-G3](#pe-g3) | 프로젝트 개요 (구두 설명) | 평가 중단 |
| [PE-G4](#pe-g4) | 간단한 설정 (GUI 없음, UFW/SSH 작동) | 평가 중단 |
| [PE-G5](#pe-g5) | 사용자 단계 1 + 2 | 평가 중단 |
| [PE-G6](#pe-g6) | 호스트 이름 + 파티션 | 평가 중단 |
| [PE-G7](#pe-g7) | sudo + UFW + SSH + monitoring | 평가 중단 |
| PE-B | 보너스 | 필수 부분 완벽 시 가점 |

---

## <a id="pe-g1"></a>PE-G1. 예비 테스트

**조건**: 평가 대상 학생 본인 참석 · 원격 클론 가능 · VM 재부팅 상태.

**평가자 행동**:
1. `git clone` 을 빈 폴더에서 실행.
2. `signature.txt` 존재 확인.
3. 호스트에서 `sha1sum` 으로 `.vdi` 해시 계산 → `diff <(cat signature.txt) <(sha1sum *.vdi | awk '{print $1}')`.
4. 제출된 `.vdi` 를 다른 디렉토리로 복사 → VBox 에서 해당 복사본으로 부팅.
5. Snapshots 탭에 스냅샷이 없음을 확인.
6. 이미 실행 중인 VM 재사용 금지.

**학생 예상 응답**: 서명 생성법 시연 (`sha1sum`), VBox 의 VM 상태 즉시 조작 가능.

**실패 조건**:
- 두 서명이 다름 → 평가 종료 (SC-41).
- 스냅샷 존재 → 평가 종료 (SC-03).

---

## <a id="pe-g2"></a>PE-G2. README.md 검사

**평가자 체크리스트**:
1. `README.md` 가 Git 루트에 존재.
2. 첫 줄이 정확히 다음 형식 (이탤릭체):
   > *This project has been created as part of the 42 curriculum by <login1>[, <login2>[, ...]]*.
3. Description / Instructions / Resources 섹션 전부 존재.
4. Project description 에 OS 선택 근거 포함.
5. 4 가지 비교 모두 포함:
   - Debian vs Rocky Linux
   - AppArmor vs SELinux
   - UFW vs firewalld
   - VirtualBox vs UTM

**실패 조건**: 요소 하나라도 누락 → 평가 즉시 종료 (SC-33 ~ SC-35).

---

## <a id="pe-g3"></a>PE-G3. 프로젝트 개요 (구두)

**평가자 질문 목록**:
1. VM 이 어떻게 동작하는가?
2. 선택한 OS 는 무엇이며 왜?
3. Rocky 와 Debian 의 기본 차이는?
4. VM 의 목적은 무엇인가?
5. (Rocky 선택 시) SELinux 와 DNF 는 무엇인가?
6. (Debian 선택 시) **aptitude 와 apt 의 차이**는 무엇인가?
7. AppArmor 란 무엇인가?

**추가 요구**: 발표 중 10 분마다 실행되는 `wall` 메시지가 정말 나타나는지 같이 기다려 확인.

**실패 조건**: 설명이 불명확 → 평가 종료.

---

## <a id="pe-g4"></a>PE-G4. 간단한 설정

**평가자 체크**:
1. 부팅 시 그래픽 환경이 뜨지 않음 (TTY 콘솔만).
2. 사용자 계정으로 로그인 가능하며 비밀번호가 정책 준수.
3. UFW 서비스 작동 (`systemctl is-active ufw`).
4. SSH 서비스 작동 (`systemctl is-active ssh`).
5. OS 가 Debian 또는 Rocky 임을 확인.

**실패 조건**: 위 항목 불만족 또는 설명 불명확.

---

## <a id="pe-g5"></a>PE-G5. 사용자 단계 1·2

### 단계 1: 기본 계정 + 비번 정책 시연

**평가자 행동**:
1. `getent passwd <login>` 으로 본인 login 사용자 확인.
2. `groups <login>` 에 `sudo` 와 `user42` 포함 확인.
3. 학생에게 **새 사용자 생성** 요청.
4. 과목 정책 준수 비밀번호로 설정하게 함 (약한 비번 거부 시연 포함).
5. 학생은 수정된 설정 파일(보통 1~2 개) 과 그 내용을 설명.

**실패**: 정책 미적용 / 파일 설명 불가.

### 단계 2: "evaluating" 그룹 생성 시연

**평가자 행동**:
1. 학생에게 **"evaluating" 이라는 이름의 그룹** 을 생성하라고 요청.
2. 그 그룹을 단계 1 에서 만든 새 사용자에게 할당하라고 요청.
3. 해당 사용자가 `evaluating` 그룹에 속해 있는지 확인.
4. 학생에게 **비밀번호 정책의 장단점** 을 설명하라고 요청 (과목 요구라는 답변은 인정 안 됨).

**실패**: 그룹 추가 실패 / 설명 부족.

---

## <a id="pe-g6"></a>PE-G6. 호스트 이름 + 파티션

**평가자 행동**:
1. `hostname` 출력이 `<login>42` 형식인지 확인.
2. 학생에게 **호스트 이름을 평가자 정보로 변경** 하고 **재시작** 하게 함.
3. 재시작 후 변경 반영 확인.
4. 학생에게 원래 호스트 이름으로 복원 가능함을 보여달라 요청.
5. 학생에게 **파티션을 보는 방법** 을 묻고 `lsblk` 결과를 Subject 예시와 비교.
6. LVM 의 작동 방식과 목적을 간략히 설명하게 함.

**실패**: 호스트 이름 변경이 반영 안 됨 / LVM 설명 불가.

---

## <a id="pe-g7"></a>PE-G7. sudo · UFW · SSH · monitoring

### sudo
1. `sudo` 설치 확인.
2. 학생이 새 사용자를 `sudo` 그룹에 할당하는 과정 시연.
3. 학생이 선택한 sudo 의 가치와 동작 원리 설명.
4. 학생이 Subject 가 강제한 엄격 규칙의 구현 방식을 보여줌.
5. `/var/log/sudo/` 존재 + 최소 1 개 파일 확인.
6. 해당 파일에 sudo 사용 흔적 확인.
7. `sudo` 로 명령 실행 후 `/var/log/sudo/` 파일이 갱신되는지 확인.

### UFW
1. UFW 설치 + 작동 확인.
2. 학생이 UFW 의 정의와 가치 설명.
3. 활성 규칙 목록에 **포트 4242** 규칙 존재 확인.
4. **포트 8080 을 여는 새 규칙** 추가 요청 → 활성 규칙 확인.
5. 학생이 그 새 규칙을 **삭제** 하게 함.

### SSH
1. SSH 서비스 설치 + 작동 확인.
2. 학생이 SSH 의 정의와 가치 설명.
3. **포트 4242 만** 사용 확인.
4. 학생이 **새 사용자 계정으로 SSH 로그인** 방법 시연.
5. 단순한 비번이어도 됨 (시연용).
6. root 계정으로 SSH 접속 금지 확인 (root 시도 → 거부).

### monitoring
1. 코드만 보면서 스크립트 작동 방식 설명.
2. cron 이 무엇인지 설명.
3. 서버 시작 시점부터 10 분 주기로 실행되도록 어떻게 설정했는지 설명.
4. 정상 작동이 확인되면 **매분 실행되도록 변경** 요청.
5. 아무 작업이나 수행해 동적 값이 갱신되는지 확인.
6. **스크립트 자체는 수정하지 않고** 실행을 중지 시키게 함.
7. 서버 재시작.
8. 재시작 후 스크립트가 원래 위치에 있고 권한도 바뀌지 않았음을 확인.

---

## 최종 Rating 플래그

평가자가 추가로 선택 가능한 플래그 (경고 vs 가점):

| 플래그 | 의미 |
|---|---|
| ✓ Ok | 정상 통과 |
| ★ Outstanding project | 특별히 우수 |
| Empty work | 제출물 없음 (0점) |
| Incomplete work | 미완성 (0점) |
| Cheat | 부정행위 (-42점) |
| Crash | 시연 중 크래시 |
| Concern | 기술적 우려 |
| Can't support / explain code | 본인 코드 설명 불가 |

## 관련 문서

- [Subject v5.2 조항](subject-v5.2-clauses.md)
- [How-to: 평가 통과 플레이북](../01-how-to/pass-peer-evaluation.md)
- [How-to: 잠김 복구](../01-how-to/recover-from-lockout.md)
