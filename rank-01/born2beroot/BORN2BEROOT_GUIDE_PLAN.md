# Born2beRoot 최종 가이드 3 문서 체계 — 실행 계획

**작성일**: 2026-04-18
**기준**: Subject v5.2, VBox 7.1.16 r172425, Debian 12.13 netinst amd64
**목표**: 다음에 백지에서 다시 시작해도 **이 3 문서만 있으면** 완벽하게 재현 + 평가 통과 + 심층 질문 대응 가능한 체계

---

## 0. 3 문서 관계도 — 하이퍼링크 삼각 연결

**본 체계의 존재 이유**: 물리적으로 3 파일로 분리되어야 각 관점이 독립적 문서로 성립하며, 모든 점프는 **클릭 한 번**으로 이뤄져야 합니다. 단일 문서에 다 넣으면 3 관점을 동시에 렌더링하는 것과 같아 읽기 어렵고, 검색·평가 대응이 느려집니다.

```
┌──────────────────────────────────────────────────────────────┐
│                    Born2beRoot 삼각 연결                     │
│                                                              │
│                  ② WHAT (Subject 매핑)                       │
│                    "평가자의 시선"                           │
│                    ◆ what-07, what-08 ...                    │
│                       ▲    │                                 │
│           클릭 ──→    │    │    ←── 클릭                     │
│           앵커 점프   │    ▼   앵커 점프                     │
│                                                              │
│  ① HOW (진행 가이드)          ③ WHY (이론)                  │
│  "진행자의 시선"       ◄────►  "이론가의 시선"              │
│  ◆ how-a1, how-c6a ...   클릭    ◆ why-11, why-12 ...       │
│                                                              │
│   한 꼭짓점 클릭 → 다른 꼭짓점의 정확한 단락으로 즉시 점프    │
└──────────────────────────────────────────────────────────────┘
```

**요구 동작 예시** (사용자 명시):
- 문서 ②에서 "must create at least 2 encrypted partitions using LVM" 클릭 → 문서 ①의 C1~C6a 단락으로 점프
- 문서 ①에서 단계 이름 옆의 [📖 WHY] 클릭 → 문서 ③의 LVM 이론 단락으로 점프
- 문서 ③에서 "이 개념이 Subject 어느 조항을 뒷받침하는가" 클릭 → 문서 ②의 WHAT-07 로 점프

---

## 1. 3 문서 구조

| # | 문서 | 파일 경로 | 역할 | 독자 |
|---|---|---|---|---|
| ① | **HOW** | `rank-01/born2beroot/install_part2_os_install_guide.md` (**기존, 보강**) | VBox 메인창 → Debian 설치 → 부팅 후 설정 → 서명까지 **모든 키 입력/클릭 단위** 텍스트 가이드 | 처음 진행 + 재현 + 막힐 때 |
| ② | **WHAT** | `rank-01/born2beroot/born2beroot_subject_mapping.md` (**신규**) | Subject v5.2 의 모든 `must/have to` 조항을 원문 인용 → HOW 가이드의 정확한 단계 앵커로 매핑 | 평가 직전 체크 + 평가자 질문 대응 |
| ③ | **WHY** | `rank-01/born2beroot/born2beroot_theory.md` (**신규**) | 각 개념을 **중학생 1~2 문장 + 학부 3~5 문장 + 구글 검색어** 3 단 구조로 설명 | 개념 이해 + 심화 탐색 시작점 |

**보조 산출물** (필수는 아니지만 언급):
- `README.md` (Git 루트) — Subject Chapter VI 요구사항. 문서 2 완성 후 파생 작업.
- `signature.txt` — 제출 직전 생성, 단순 sha1 출력.

---

## 1.5 하이퍼링크 네비게이션 설계 (핵심)

### 1.5.1 앵커 네이밍 규칙

모든 단락의 앵커는 `<prefix>-<id>` 형태로 **영문 소문자 + 숫자 + 하이픈** 만 사용 (한글·공백·특수문자 금지 — renderer 호환성).

| 문서 | prefix | 예시 |
|---|---|---|
| 문서 1 (HOW) | `how-` | `how-a1`, `how-c6a`, `how-e2` |
| 문서 2 (WHAT) | `what-` | `what-07`, `what-23` |
| 문서 3 (WHY) | `why-` | `why-11`, `why-22` |

### 1.5.2 앵커 삽입 문법 (3 가지 지원, HTML 앵커 채택)

```markdown
<!-- ① HTML anchor (모든 renderer 호환, 채택) -->
## <a id="how-c6a"></a>C6a. Volume Group 할당량 지정

<!-- ② GitBook kramdown attribute (일부 지원) -->
## C6a. Volume Group 할당량 지정 {#how-c6a}

<!-- ③ Heading auto-slug (한글 포함 시 깨질 위험) -->
## C6a. Volume Group 할당량 지정
<!-- 자동: #c6a-volume-group-할당량-지정 -->
```

**채택**: ① HTML anchor. 이유 = GitBook · GitHub · VS Code Preview · 일반 markdown viewer 모두 100% 동작 + 한글 제목 영향 없음 + 앵커 ID 가 명시적이라 깨질 일 없음.

### 1.5.3 파일간 링크 문법

같은 폴더 내 파일이면 상대 경로 + `#앵커`:

```markdown
<!-- 문서 2 (WHAT) 안에서 문서 1 의 C6a 로 점프 -->
[→ HOW §C6a (VG 할당량)](./install_part2_os_install_guide.md#how-c6a)

<!-- 문서 1 (HOW) 안에서 문서 3 의 LVM 이론으로 점프 -->
[📖 WHY-11 LVM 이론](./born2beroot_theory.md#why-11)

<!-- 문서 3 (WHY) 안에서 문서 2 의 해당 Subject 조항으로 점프 -->
[📋 대응 Subject 조항 WHAT-07](./born2beroot_subject_mapping.md#what-07)
```

### 1.5.4 삼각 연결 실제 예시 — LVM 한 개념 주변

**문서 2 (WHAT-07)**:
```markdown
### <a id="what-07"></a>WHAT-07. 암호화 파티션 최소 2 개 + LVM

**Subject 원문 (Chapter V, p.7)**:
> "You must create at least 2 encrypted partitions using LVM."

**만족 단계**: [→ HOW §C1~C13 파티셔닝 전체](./install_part2_os_install_guide.md#how-c1)
특히 [§C6 LUKS 암호](./install_part2_os_install_guide.md#how-c6),
[§C6a VG 할당량](./install_part2_os_install_guide.md#how-c6a),
[§C10 LV 7 개 생성](./install_part2_os_install_guide.md#how-c10)

**이론 배경**:
[→ WHY-11 LVM](./born2beroot_theory.md#why-11) ·
[→ WHY-12 LUKS](./born2beroot_theory.md#why-12)

**검증 명령 (평가 시연)**: `lsblk` — sda5_crypt 위에 LVM LV 들이 매달린 형태 확인
```

**문서 1 (HOW §C6a) — 각 단계 제목에 앵커 + 우측 쇼트컷**:
```markdown
## <a id="how-c6a"></a>C6a. Volume Group 할당량 지정
[📋 Subject WHAT-07](./born2beroot_subject_mapping.md#what-07) ·
[📖 WHY-11 LVM](./born2beroot_theory.md#why-11)

**보이는 것**: ...
**할 일**: ...
**WHY (요약)**: ...
```

**문서 3 (WHY-11)**:
```markdown
### <a id="why-11"></a>WHY-11. LVM (Logical Volume Manager)

**중학생 1~2 문장**: 디스크를 "레고 블록" 처럼 쌓고 자를 수 있게 해주는 도구...

**학부 3~5 문장**: LVM 은 PV(Physical Volume) → VG(Volume Group) → LV(Logical Volume) 의 3 계층 추상화...

**더 깊이 (구글 검색)**:
- `"LVM Physical Extent size explained"`
- `"device-mapper architecture linux"`

**연결**:
- 사용처: [HOW §C8 LVM 메뉴 진입](./install_part2_os_install_guide.md#how-c8),
  [§C10 LV 생성](./install_part2_os_install_guide.md#how-c10)
- 대응 Subject: [WHAT-07 최소 2 암호화 파티션 + LVM](./born2beroot_subject_mapping.md#what-07)
```

### 1.5.5 앵커 정합성 검증

Phase 4 검증 시 다음 명령으로 깨진 링크 0 개 확인:

```bash
# 3 문서에서 정의된 앵커 추출
grep -oh 'id="[a-z0-9-]*"' *.md | sort -u > defined_anchors.txt

# 3 문서에서 참조된 앵커 추출
grep -oh '#[a-z0-9-]*)' *.md | sort -u > referenced_anchors.txt

# 참조되지만 정의되지 않은 앵커 = 깨진 링크
comm -23 referenced_anchors.txt defined_anchors.txt
```

깨진 링크 0 개가 Phase 4 완료 기준.

---

## 2. 문서 1 (HOW) — 현재 상태와 보강 계획

### 2.1 현재 상태 (1,195 줄, 40 KB)

이미 좋은 뼈대가 있습니다:
- Part A (A1~A7): VirtualBox VM 생성 마법사 5 페이지
- Part B (B1~B10): 언어/지역/키보드/네트워크/사용자
- Part C (C1~C13, **C6a 추가됨**): 파티셔닝 (VG 할당량 지정 포함)
- Part D (D1~D7): 미러/소프트웨어/GRUB/첫 부팅/로그인
- Part E (E1~E9): SSH/UFW/sudo/비번/모니터/WordPress/Netdata/서명
- Part F: 자가점검 28 개
- 부록: 빠른 진단 표

### 2.2 보강 필요 (Phase 1 에서 처리)

**A. 현재 진행 중 누락된 화면 (사용자 스크린샷으로 발견)**
- C6a (VG 할당량) ← **완료**
- 추후 사용자 피드백 대로 추가

**B. Subject 검토로 발견된 누락**
- `apparmor 시작 시 작동` 확인 단계 → Part E 에 `E2.5. AppArmor 확인` 신규
- `monitoring.sh` 의 "서버 시작 시" 실행 → systemd service 또는 `@reboot` cron 추가 단계
- `/snap/bin` 이 secure_path 예시에 포함됨 → Part E4 의 secure_path 줄 업데이트
- `difok` 가 root 에 적용 안 됨 → Part E5 의 WHY 에 주석 추가

**C. 보너스 파트 구조 반영**
- Subject Chapter VII 의 lsblk 예시 (LVMGroup-root 10G, swap 2.3G, home 5G, var 3G, srv 3G, tmp 3G, var-log 4G) 를 **Part C10 의 권장 크기 표의 기준**으로 재정렬

---

## 3. 문서 2 (WHAT — Subject Mapping) — 작성 계획

### 3.1 구조

각 Subject 조항을 다음 4 요소로 제시:

```
### WHAT-NN. [조항 요약 제목]

**Subject 원문 (Chapter X, p.Y)**:
> "...그대로 인용..."

**만족 단계**: → HOW §X-Y ([링크])
**검증 명령** (평가 시연용): `명령`
**이론 배경**: → WHY §Z ([링크])
```

### 3.2 목차 초안 (모든 `must/have to` 조항)

**Chapter IV — General**
- WHAT-01: VirtualBox (or UTM) 사용 강제
- WHAT-02: signature.txt 제출
- WHAT-03: 평가 시작 시 snapshot 없음

**Chapter V — Mandatory**
- WHAT-04: GUI 금지 (X.org/Wayland → 0 점)
- WHAT-05: Debian 최신 안정판 또는 Rocky 최신 안정판
- WHAT-06: AppArmor (Debian) 시작 시 작동
- WHAT-07: 암호화 파티션 최소 2 개 + LVM
- WHAT-08: SSH 포트 4242
- WHAT-09: SSH root 로그인 금지
- WHAT-10: UFW 설치, 4242 만 허용
- WHAT-11: 방화벽 VM 시작 시 활성
- WHAT-12: hostname = `<login>42`
- WHAT-13: 강한 비밀번호 정책
- WHAT-14: sudo 엄격 설정
- WHAT-15: root 외에 본인 login 사용자 존재
- WHAT-16: 해당 사용자 = user42 + sudo 그룹 멤버
- WHAT-17: 비번 만료 30 일
- WHAT-18: 비번 변경 최소 간격 2 일
- WHAT-19: 만료 7 일 전 경고
- WHAT-20: 비번 10 자 이상, 대·소문자·숫자 포함, 연속 동일 3 초과 금지
- WHAT-21: 비번에 사용자명 미포함
- WHAT-22: (root 제외) 이전 비번과 7 자 이상 달라야 함
- WHAT-23: root 도 위 정책 준수
- WHAT-24: 설정 후 전체 계정 비번 재설정
- WHAT-25: sudo 3 회 오인증 제한
- WHAT-26: sudo 오인증 커스텀 메시지
- WHAT-27: sudo 입력/출력 로그 → `/var/log/sudo/`
- WHAT-28: sudo TTY 모드
- WHAT-29: sudo 경로 제한 (secure_path)
- WHAT-30: monitoring.sh (bash) 서버 시작 시 + 10 분 주기
- WHAT-31: monitoring.sh 12 항목 출력

**Chapter VI — README**
- WHAT-32: README.md Git 루트
- WHAT-33: 첫 줄 이탤릭 (정해진 문구)
- WHAT-34: Description / Instructions / Resources 섹션
- WHAT-35: Project description + 4 가지 비교

**Chapter VII — Bonus (선택, 필수는 아니나 "must" 가 나옴)**
- WHAT-36: 파티션 구조 (lsblk 예시 기준)
- WHAT-37: WordPress (lighttpd + MariaDB + PHP)
- WHAT-38: 추가 서비스 1 개 (NGINX/Apache 금지)

**Chapter VIII — Submission**
- WHAT-39: README.md + signature.txt 제출
- WHAT-40: sha1 형식 서명
- WHAT-41: VM 자체 Git 포함 금지 → 0 점

→ **총 약 41 개 항목**. 평균 15 줄씩 잡으면 ~700 줄 문서.

### 3.3 작성 원칙
- Subject 원문은 **영문 그대로 인용** (번역은 생략 또는 괄호 보조)
- `must/have to/forbidden` 구절은 **굵게 강조**
- HOW 앵커는 `#c6a`, `#e2` 같은 markdown anchor 사용
- 각 조항 아래 "검증 명령" 한 줄 필수 (평가 시연 대비)

---

## 4. 문서 3 (WHY — 이론) — 작성 계획

### 4.1 3 단 구조

각 개념마다:

```
### WHY-NN. [개념 이름]

**중학생 1~2 문장**: (비유 포함)
**학부 3~5 문장**: (정확한 정의 + 동작 원리)
**더 깊이 파고 싶다면 (구글 검색)**: 
- `"정확한 검색 키워드 1"`
- `"정확한 검색 키워드 2"`

**연결**: HOW §X-Y 에서 사용, WHAT-NN 조항에 대응
```

### 4.2 목차 초안

**하드웨어·가상화**
- WHY-01: 하이퍼바이저 (Type 1 vs Type 2)
- WHY-02: vCPU 와 pCPU (왜 초과 할당이 더 느린가)
- WHY-03: 가상 디스크 (vdi/vmdk, Pre-Allocate)

**부팅 흐름**
- WHY-04: BIOS vs UEFI
- WHY-05: MBR 구조 (512 바이트)
- WHY-06: GRUB 다단계 부트로더
- WHY-07: 커널과 initramfs
- WHY-08: systemd 와 PID 1

**저장**
- WHY-09: 파티션 (primary/extended/logical)
- WHY-10: 파일시스템과 저널링 (ext4 vs xfs vs btrfs)
- WHY-11: LVM (PV/VG/LV)
- WHY-12: LUKS 와 dm-crypt (계층)

**네트워크**
- WHY-13: NAT 와 포트 포워딩
- WHY-14: SSH 와 공개키 암호 (간단)
- WHY-15: UFW/iptables 방화벽

**권한과 정책**
- WHY-16: root / 일반 사용자 / sudo
- WHY-17: 그룹과 getent
- WHY-18: PAM 과 pwquality
- WHY-19: AppArmor (path-based MAC) vs SELinux (label-based)

**모니터링**
- WHY-20: cron 과 wall
- WHY-21: monitoring 12 항목 (왜 이 조합)

**웹 스택 (보너스)**
- WHY-22: lighttpd 와 FastCGI
- WHY-23: MariaDB (MySQL fork)
- WHY-24: PHP 와 www-data

**무결성**
- WHY-25: 해시 함수 (SHA-1 의 1:1 매칭)
- WHY-26: VBox 스냅샷 (COW)

→ **총 약 26 개 개념**. 평균 20 줄씩 잡으면 ~520 줄 문서.

### 4.3 작성 원칙
- **중학생 버전은 비유 필수**. 기술 용어 최소화
- **학부 버전은 정확**. 다만 논문급 디테일 금지
- **구글 검색어는 영문 + 한글 혼합 추천** (검색 결과 풍부한 쪽)
- **참고 자료 출처**는 학부 교재 (Silberschatz, Tanenbaum, Stallings) + 공식 man page + Wikipedia

---

## 5. 참고 자료 활용

| 자료 | 활용처 |
|---|---|
| `_reference/en_subject_born2beroot.pdf` | 문서 2 의 **원천** (구절 인용) |
| `_reference/README_KR.md` | 문서 1 검증 (빠진 화면 없는지) |
| `_reference/MBR_완역_주석_0장.md` | 문서 3 §WHY-04~08 의 직접 근거 |
| `_reference/하드웨어자료조사.md` | 문서 3 §WHY-02 보강 |
| `_reference/virtualbox_howtosetcpu.md` | 문서 3 §WHY-02 의 1 차 출처 (본인 메모) |
| `how_to_set_hardware_detailed_report.md` | 문서 3 §WHY-01~03 의 추가 근거 (LLM 편집물이라 검증 후 발췌) |
| `_reference/subject_ko_Born2beRoot.md` | 한글 번역판 — subject 인용 시 참고 |
| `_reference/스크린샷 *.png` | 문서 1 의 실제 화면 검증 |
| README_KR.md 의 GIF 링크 (외부) | **옵션** — Phase 1 후반에 fetch 여부 결정 |

---

## 6. 작업 순서 (Phase)

### Phase 1: 문서 1 (HOW) 완성
- **1-A** (진행 중): 사용자 실제 진행 피드백 받으며 누락 화면 추가 (C6a 사례 같은 방식)
- **1-B**: Subject 검토로 발견된 누락 보강 (AppArmor, monitoring startup, /snap/bin 등)
- **1-C**: 보너스 파트 Subject 의 lsblk 기준으로 C10 권장 크기 재정렬
- **1-D**: §1.5 규칙대로 모든 단계 제목에 `<a id="how-xx"></a>` HTML 앵커 삽입 + 각 단계 제목 아래 [📋 WHAT-nn] [📖 WHY-nn] 역방향 쇼트컷 2 줄 추가

**완료 기준**: 사용자가 텍스트만으로 처음부터 끝까지 막히지 않고 부팅 → SSH → 서명까지 도달 + 보너스 옵션 진행 가능

### Phase 2: 문서 2 (WHAT) 작성
- **2-A**: Subject 원문에서 `must/have to/forbidden` 조항 모두 추출 (약 41 개)
- **2-B**: 각 조항을 WHAT-NN 으로 번호 매기고 HOW 앵커 연결
- **2-C**: 각 조항에 검증 명령 1 줄 추가 (평가 시연용)

**완료 기준**: Subject 의 모든 의무 조항이 WHAT-NN 번호로 커버되고, 각각의 HOW 앵커 링크가 작동

### Phase 3: 문서 3 (WHY) 작성
- **3-A**: 26 개 개념을 3 단 구조로 작성
- **3-B**: 참고 자료 (`MBR_완역_주석_0장.md` 등) 에서 발췌 + 압축
- **3-C**: HOW/WHAT 앵커 역방향 링크

**완료 기준**: 중학생 1 문장만 읽어도 감이 오고, 구글 검색어 한 줄로 본인이 심화 탐색 시작 가능

### Phase 4: 통합 검증
- **4-A**: 3 문서 모든 앵커가 양방향 작동 확인 (grep)
- **4-B**: Subject 조항 → WHAT → HOW → WHY 왕복 테스트
- **4-C**: 최종 "백지 재현" 리허설 시나리오 (문서 1 만으로 진행, 막히면 문서 3 참조)

---

## 7. 검증 기준 (완료 체크)

| 기준 | 측정 방법 |
|---|---|
| HOW 로 백지 재현 가능 | 사용자가 VM 삭제 후 새 VM 에서 문서 1 만 보고 완주 |
| Subject 100% 커버 | 문서 2 에 Subject 의 `must/have to` 조항이 빠짐없이 있는가 |
| 평가 단골 질문 대응 | 평가자가 무작위 5 개 질문 → 문서 2 번호로 즉답 가능 |
| WHY 가독성 | 중학생이 비유 읽고 "아~" 하는지 (본인 셀프 테스트) |
| 앵커 정합성 | 3 문서 간 깨진 링크 0 개 |

---

## 8. 지금 바로 다음 할 일 (Phase 1 착수)

1. ✅ 계획 문서 작성 (본 문서)
2. 🔜 **Phase 1-A**: 사용자 실제 진행 이어서, 추가로 누락된 화면 발견 시 즉시 HOW 에 삽입
3. Phase 1-B/C: Phase 1-A 와 병행 가능 (Subject 기반 누락 보강)
4. Phase 1-D: Phase 1 마무리 시점에 앵커 일괄 삽입

**사용자에게 요청**: 본 계획에 수정/추가 의견 또는 `진행` → Phase 1 착수. 현재 실제 진행 화면(C7 자동 파티션 표 등) 스크린샷 주시면 Phase 1-A 동시 처리.

---

## 부록: GIF 탐독 옵션

README_KR.md 가 참조하는 GIF 는 외부 Git 원본 (`NoelClay/42-born2beroot_guide` 또는 `thebrisly/42-guide-website`) 에 있습니다. 현재 파일시스템에 없음.

**옵션 A**: fetch 없이 사용자 실제 스크린샷으로만 검증 (현재 방식)
**옵션 B**: `gh` 또는 `curl` 로 GIF fetch → 프레임별 텍스트 묘사 생성 → HOW 의 화면 묘사 대조 검증

→ **옵션 A 권장** (사용자 실제 환경이 더 정확). 옵션 B 는 Phase 1-A 후 "여전히 빠진 화면이 있다" 싶을 때 보조.
