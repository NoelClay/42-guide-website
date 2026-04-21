# 📠 Born2beRoot v5.2 — HOW 설치 단계별 가이드 (2026-04-18)

**3 문서 체계 중 ① HOW (진행자의 시선)** · 키 입력 단위 텍스트 가이드

| 문서 | 역할 | 파일 |
|---|---|---|
| ① **HOW** (본 문서) | VBox 메인창 → Debian 설치 → 부팅 후 설정 → 서명까지 모든 키 입력/클릭 | `Born2beRoot_v5_2_HOW_2026-04-18.md` |
| ② **WHAT** | Subject v5.2 의 `must/have to` 조항 → HOW 단계 매핑 + 검증 명령 | `Born2beRoot_v5_2_WHAT_2026-04-18.md` |
| ③ **WHY** | 각 개념의 (중학생 / 학부 / 구글 검색어) 3 단 이론 | `Born2beRoot_v5_2_WHY_2026-04-18.md` |

각 단계 제목 옆의 `<a id="how-xx"></a>` 앵커 → WHAT/WHY 에서 클릭 시 이 위치로 점프. 본 문서 안에서도 부록 표의 `C12 다시` 같은 크로스 레퍼런스가 앵커로 동작.

| 환경 | 값 |
|---|---|
| Hypervisor | VirtualBox 7.1.16 r172425 (Qt6.5.3) |
| Guest OS | Debian 12.13 (Bookworm) amd64 netinst.iso |
| Firmware | BIOS (UEFI 미사용 — GRUB MBR 부팅) |
| 디스크 | 20 GB, VDI, Pre-Allocate Full Size |
| 메모리 | 4 GB, vCPU 2~3 |
| 네트워크 | NAT (포트 포워딩으로 SSH/HTTP/Netdata 노출) |

| 짝 자료 (참고용) | 역할 |
|---|---|
| [`README_KR.md`](./_reference/README_KR.md) | chlimous/NoelClay 번역본 — 시간 순 절차서 (그림 + 명령) |
| [v5.2 메인 가이드](./install-your-virtual-machine_new_v5_2.md) | OS·하드웨어 결정 사전 정리 (Part 0~1) |
| [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) | 부팅 흐름 깊이 자료 |
| [Subject (영문)](./_reference/en_subject_born2beroot.pdf) | 평가 기준 원문 |

깊이는 **컴퓨터공학 학부 수준** 으로 통제. 본 문서 + WHAT + WHY 면 평가 통과 + 다음에 백지 재현 가능.

---

## 섹션 ↔ WHAT (Subject) ↔ WHY (이론) 매핑 (클릭 시 점프)

| HOW 섹션 | Subject 요구 (WHAT) | 학부 이론 (WHY) |
|---|---|---|
| [Part A](#how-part-a) VM 생성 (A1~A7) | [📋 WHAT-01](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-01) VBox | [📖 WHY-01](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-01) 하이퍼바이저 · [📖 WHY-02](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-02) vCPU·pCPU · [📖 WHY-03](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-03) VDI · [📖 WHY-04](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-04) BIOS/UEFI |
| [B6](#how-b6) hostname | [📋 WHAT-12](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-12) | — |
| [B8~B9](#how-b8) root/사용자 | [📋 WHAT-15](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-15) | [📖 WHY-16](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16) root·sudo · [📖 WHY-17](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-17) 그룹 |
| [Part C](#how-part-c) 파티셔닝 (C1~C13) | [📋 WHAT-07](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-07) 암호화+LVM · [📋 WHAT-36](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-36) 보너스 구조 | [📖 WHY-05](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-05) MBR · [📖 WHY-09](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-09) 파티션 · [📖 WHY-10](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-10) FS/저널링 · [📖 WHY-11](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-11) LVM · [📖 WHY-12](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-12) LUKS |
| [D3](#how-d3) 소프트웨어 선택 | [📋 WHAT-04](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-04) GUI 금지 · [📋 WHAT-05](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-05) Debian | — |
| [D4](#how-d4) GRUB | — | [📖 WHY-06](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-06) GRUB |
| [D6~D7](#how-d6) 첫 부팅/로그인 | — | [📖 WHY-07](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-07) 커널/initramfs · [📖 WHY-08](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-08) systemd |
| [E2](#how-e2) SSH 4242 | [📋 WHAT-08](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-08) 포트 · [📋 WHAT-09](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-09) root 금지 | [📖 WHY-13](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-13) NAT · [📖 WHY-14](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-14) SSH |
| [E2.5](#how-e2-5) AppArmor | [📋 WHAT-06](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-06) | [📖 WHY-19](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-19) AppArmor·SELinux |
| [E3](#how-e3) UFW | [📋 WHAT-10](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-10) · [📋 WHAT-11](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-11) | [📖 WHY-15](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-15) UFW/iptables |
| [E4](#how-e4) sudo/그룹 | [📋 WHAT-14](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-14) · [📋 WHAT-16](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-16) · [📋 WHAT-25~29](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-25) | [📖 WHY-16](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-16) root·sudo · [📖 WHY-17](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-17) 그룹 |
| [E5](#how-e5) 비번 정책 | [📋 WHAT-13](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-13) · [📋 WHAT-17~24](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-17) | [📖 WHY-18](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-18) PAM·pwquality |
| [E6](#how-e6) monitoring/cron | [📋 WHAT-30](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-30) · [📋 WHAT-31](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-31) | [📖 WHY-20](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-20) cron·wall · [📖 WHY-21](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-21) 12 항목 |
| [E7](#how-e7) WordPress | [📋 WHAT-37](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-37) | [📖 WHY-22](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-22) lighttpd·FastCGI · [📖 WHY-23](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-23) MariaDB · [📖 WHY-24](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-24) www-data |
| [E8](#how-e8) Netdata | [📋 WHAT-38](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-38) | — |
| [E9](#how-e9) 서명/스냅샷 | [📋 WHAT-02](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-02) · [📋 WHAT-03](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-03) · [📋 WHAT-39~41](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-39) | [📖 WHY-25](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-25) SHA-1 · [📖 WHY-26](./Born2beRoot_v5_2_WHY_2026-04-18.md#why-26) 스냅샷 |

**사용법**:
- 진행 중 "왜 이 값?" 궁금하면 해당 행의 📖 WHY 클릭 → 학부 수준 설명으로 점프
- 평가자가 Subject 조항을 물으면 해당 행의 📋 WHAT 클릭 → Subject 원문 + 검증 명령으로 점프
- 모든 링크는 같은 폴더 내 파일 + 앵커 형식이라 GitBook·GitHub·VS Code Preview 에서 클릭 즉시 점프

---

## 표기 규칙

- `[Button]` = GUI 버튼 클릭
- `↑↓` = 화살표 키
- `Enter` = 엔터키
- `Tab` = 다음 항목으로 포커스 이동
- `Space` = 체크박스/라디오 토글
- `<Yes>` `<No>` `<Continue>` = ncurses 화면의 하단 버튼
- `[입력란]` = 텍스트 입력
- → = "다음 화면으로 넘어감"

---

# <a id="how-part-a"></a>Part A — VirtualBox 7.1 에서 VM 생성

## <a id="how-a1"></a>A1. 새 VM 마법사 열기

**보이는 것**: VirtualBox 메인 창 (좌측 VM 목록, 우측 정보 패널).

**할 일**:
1. 메뉴바 `Machine` → `New` (또는 단축키 `Ctrl+N`)

→ "Create Virtual Machine" 마법사가 열림. 좌측 사이드바에 5 단계가 보임:
- Name and Operating System (현재)
- Unattended Install
- Hardware
- Hard Disk
- Summary

## <a id="how-a2"></a>A2. 페이지 1 — Name and Operating System

**보이는 것** (위에서 아래로):
- `Name:` [입력란]
- `Folder:` [경로 + 폴더 아이콘]
- `ISO Image:` [드롭다운, 기본 `<not selected>`]
- `Edition:` (자동)
- `Type:` (자동)
- `Subtype:` (자동)
- `Version:` (자동)
- ☐ `Skip Unattended Installation`

**할 일**:
1. `Name` 칸: `born2beroot` (또는 `<login>` 본인 이름) 입력
2. `Folder` 칸: 그대로 두거나, 42 경산이면 USB 마운트 경로 (예: `/media/<login>/USB/VMs`) 로 변경
3. `ISO Image` 드롭다운 → `Other...` → 파일 선택 창에서 `debian-12.13.0-amd64-netinst.iso` 선택 → `[Open]`
4. `Edition`/`Type`/`Subtype`/`Version` 이 자동으로 `Debian 12 Bookworm 64-bit` 잡힌 것을 확인
5. **`☐ Skip Unattended Installation` 체크박스를 클릭해 `☑` 로 만들기** ← 필수
6. `[Next]` 클릭

**WHY**: Skip 안 켜면 VBox 가 사용자명·비번·파티션을 자동으로 답해버려 평가 시 "왜 이 설정?" 질문에 답할 수 없음.

→ Page 2 (Unattended Install) 는 Skip 체크 때문에 자동 건너뜀, Page 3 (Hardware) 로 직행.

## <a id="how-a3"></a>A3. 페이지 3 — Hardware

**보이는 것**:
- `Base Memory:` [슬라이더 + MB 입력란]
- `Processors:` [슬라이더 + 입력란]
- ☐ `Enable EFI (special OSes only)`

**할 일**:
1. `Base Memory` → `4096` MB 입력 (또는 슬라이더로 4096)
2. `Processors` → `3` 입력 (또는 v5.2 가이드대로 `2`. 본인 호스트 CPU 와 맞춰서)
3. **`☐ Enable EFI` 는 체크하지 않은 상태 유지** ← BIOS 모드로 부팅해야 MBR + GRUB 흐름이 본 과제와 일치
4. `[Next]` 클릭

**WHY**:
- 4 GB = Debian 최소 + 보너스(MariaDB/PHP/Netdata) 여유. v5.2 메인 가이드 Part 1 §1 의 산출.
- 3 vCPU = PHP 워커 부하 + OS 여유. **호스트 물리 코어 수를 절대 넘지 않게**.
- EFI 끔 = MBR 부팅 (sda1=/boot, sda2=extended, sda5=암호화). UEFI 면 EFI System Partition 이 따로 필요해 가이드와 어긋남.

→ Page 4 (Hard Disk) 로 이동.

## <a id="how-a4"></a>A4. 페이지 4 — Virtual Hard Disk

**보이는 것**:
- ● `Create a Virtual Hard Disk Now`
- ○ `Use an Existing Virtual Hard Disk File`
- ○ `Do Not Add a Virtual Hard Disk`
- `Disk Size:` [슬라이더 + GB 입력란]
- ☐ `Pre-Allocate Full Size`
- `Hard Disk File Type:` [드롭다운] (VDI 기본)
- `Storage on Physical Hard Disk:` Dynamically allocated (기본)

**할 일**:
1. `● Create a Virtual Hard Disk Now` 선택 (기본값)
2. `Disk Size` → `20.00 GB` 입력
3. **`☐ Pre-Allocate Full Size` 클릭해서 `☑`** ← 필수
4. `Hard Disk File Type` → `VDI (VirtualBox Disk Image)` 그대로
5. `[Next]` 클릭

**WHY**:
- 20 GB = OS + 보너스 + 안전 계수 2 배 (v5.2 메인 가이드 Part 1 §2).
- Pre-Allocate = 호스트 디스크에서 20 GB 를 즉시 점유 → I/O 단편화 방지 + 평가 중 디스크 부족 위험 제거.
- VDI = VBox 네이티브, 호환성 최선. VMDK 도 가능하지만 본 과제 표준은 VDI.

→ Page 5 (Summary) 로 이동.

## <a id="how-a5"></a>A5. 페이지 5 — Summary

**보이는 것**: 위 4 단계의 설정 요약.

**할 일**:
1. 설정 확인 (Memory 4096, Processors 3, Disk 20 GB, ISO 경로 정확)
2. `[Finish]` 클릭

→ 메인 창에 새 VM `born2beroot` 가 추가됨.

## <a id="how-a6"></a>A6. (옵션) VM 시작 전 추가 확인

VBox 메인 창에서 새 VM 우클릭 → `Settings` → 확인할 항목만:

- `System` → `Motherboard` → `Boot Order` 가 `Optical, Hard Disk` 순서인지 확인 (ISO 부팅 위해)
- `System` → `Acceleration` → `Hardware Virtualization` 두 체크박스 모두 ☑ 확인
- `Network` → `Adapter 1` → `Attached to: NAT` 확인 (기본)
- `Display` → `Video Memory` → 16 MB (기본 그대로)

`[OK]` 로 빠져나옴.

## <a id="how-a7"></a>A7. VM 시작

1. 메인 창에서 `born2beroot` 선택
2. 도구바 `[Start]` 클릭 (또는 `Ctrl+T`, 또는 더블클릭)

→ Debian installer 부트 메뉴가 새 창에 표시됨.

---

# <a id="how-part-b"></a>Part B — Debian installer: 진입 ~ 사용자 설정

## <a id="how-b1"></a>B1. 부트 메뉴

**보이는 것** (검은 화면 + 파란 메뉴 박스):
```
Debian GNU/Linux installer menu (BIOS mode)

  Graphical install
  Install                       ← 이걸 선택
  Advanced options >
  Accessible dark contrast installer menu >
  Help
  Install with speech synthesis
```

**할 일**:
1. `↑↓` 로 `Install` 선택 → `Enter`

**WHY**: 텍스트 모드는 GUI 보다 가벼움 + 메모리 적게 사용 + 키 조작이 일관됨. Graphical install 도 결과는 같지만 본 가이드는 텍스트 모드 기준으로 키 시퀀스 통일.

→ "Select a language" 화면.

## <a id="how-b2"></a>B2. 언어 선택

**보이는 것**: 언어 목록.

**할 일**:
1. `↑↓` 로 `English - English` 선택 → `Enter`

**WHY**: 한국어도 가능하지만 **에러 메시지를 검색할 때 영어 메시지가 압도적으로 자료가 많음**. 문제 해결 속도가 다름.

→ "Select your location" 화면.

## <a id="how-b3"></a>B3. 지역 선택

**보이는 것**:
```
Country, territory or area:

  United States
  United Kingdom
  Canada
  ...
  other                          ← 이걸 선택
```

**할 일**:
1. `↑↓` 로 맨 아래 `other` → `Enter`
2. 다음 화면 `Asia` → `Enter`
3. 다음 화면 `Korea, Republic of` → `Enter`

→ "Configure locales" 화면.

## <a id="how-b4"></a>B4. 로케일 설정

**보이는 것**:
```
Country to base default locale settings on:

  United States - en_US.UTF-8    ← 권장
  ...
```

**할 일**:
1. `United States - en_US.UTF-8` → `Enter` (또는 `Korea, Republic of - ko_KR.UTF-8` 도 가능)

**WHY**: 위와 동일. 시스템 메시지 영어 권장.

→ "Configure the keyboard" 화면.

## <a id="how-b5"></a>B5. 키보드 레이아웃

**보이는 것**: 키보드 레이아웃 목록.

**할 일**:
1. 본인 키보드에 맞춰 선택 → `Enter`
   - 한글 키보드: `Korean` 검색해 선택
   - 영문 키보드: `American English`

→ 자동으로 ISO/패키지 로딩 (수 초~십수 초).
→ "Configure the network" 자동 진행 (DHCP).

## <a id="how-b6"></a>B6. 호스트네임

**보이는 것**:
```
Hostname:
[입력란]
```

**할 일**:
1. **본인 login + `42`** 입력 (예: `dongjki42`) → `Tab` → `<Continue>` → `Enter`

**WHY**: 서브젝트가 `<login>42` 로 강제. 평가 자동화에서 이 형식 검증.

→ "Configure the network" - Domain name 화면.

## <a id="how-b7"></a>B7. 도메인 이름

**보이는 것**:
```
Domain name:
[입력란]
```

**할 일**:
1. **비워둔 채로** `Tab` → `<Continue>` → `Enter`

**WHY**: 본 VM 은 외부 DNS 도메인에 등록되지 않은 단독 서버. 비우면 `/etc/hosts` 에 깔끔하게 호스트네임 한 줄만 들어감.

→ "Set up users and passwords" - Root password.

## <a id="how-b8"></a>B8. Root 비밀번호

**보이는 것**:
```
Root password:
[입력란 - 입력 시 ●●● 또는 빈 공간]

Show Password in Clear  ☐
```

**할 일**:
1. 강한 비밀번호 입력 (10 자 이상, 대소문자+숫자+특수문자) → `Tab` → `<Continue>` → `Enter`
2. 같은 비밀번호 재입력 → `Tab` → `<Continue>` → `Enter`

**WHY**: root 는 시스템의 최종 권한. 사용자/LUKS 비밀번호와 **반드시 다르게** 설정 (한쪽 유출 시 전부 유출 방지). 추후 pwquality 정책에 부합하는 강도로 설정해 두면 평가 시 변경 안 해도 됨.

→ Full name for new user 화면.

## <a id="how-b9"></a>B9. 일반 사용자 생성

**보이는 것**: `Full name for the new user:`

**할 일**:
1. 본인 이름 입력 (예: `Donggi Kim`) → `Tab` → `<Continue>` → `Enter`
2. 다음 화면 `Username for your account:` → `<login>` 입력 (예: `dongjki`, **42 안 붙임**) → `Tab` → `<Continue>` → `Enter`
3. User password → 입력 → `Tab` → `<Continue>` → `Enter`
4. 재입력 → `Tab` → `<Continue>` → `Enter`

**WHY**: 사용자명은 `<login>` (호스트네임은 `<login>42`). 일상 작업은 이 사용자로 SSH 접속, 필요 시 `sudo` 또는 `su -`.

→ "Configure the clock" 자동 (Asia/Seoul 자동 감지).

## <a id="how-b10"></a>B10. 시간대

**보이는 것**:
```
Select your time zone:
  Seoul                          ← 자동 강조
  ...
```

**할 일**:
1. `Seoul` → `Enter`

→ **Partition disks** 화면 (Part C 의 시작).

---

# <a id="how-part-c"></a>Part C — 파티셔닝 (가장 중요·자세하게)

여기서 막히는 사람이 가장 많습니다. **모든 키 입력을 빼먹지 말고** 따라가세요.

## <a id="how-c1"></a>C1. 파티션 방법 선택

**보이는 것**:
```
Partitioning method:

  Guided - use entire disk
  Guided - use entire disk and set up LVM
  Guided - use entire disk and set up encrypted LVM   ← 이걸 선택
  Manual
```

**할 일**:
1. `↑↓` 로 `Guided - use entire disk and set up encrypted LVM` → `Enter`

**WHY**: encrypted LVM = 디스크 → LUKS → LVM → ext4 의 4 층 스택을 자동 생성. Manual 로 처음부터 만들 수도 있지만 Guided 가 시간 1/3.

→ 디스크 선택 화면.

## <a id="how-c2"></a>C2. 디스크 선택

**보이는 것**:
```
Select disk to partition:

  SCSI3 (0,0,0) (sda) - 21.5 GB ATA VBOX HARDDISK
```

**할 일**:
1. `Enter`

**WHY**: VBox 가 만든 가상 디스크가 `sda` 로 잡힘. 21.5 GB 는 20 GB 의 base-10 표기 차이 (정상).

→ 데이터 삭제 경고.

## <a id="how-c3"></a>C3. 데이터 삭제 확인

**보이는 것**:
```
You have selected an entire device to partition. ...
all data on the disk you select will be erased ...

  <Go Back>          <No>          <Yes>          ← Yes 선택
```

**할 일**:
1. `Tab` 또는 `→` 로 `<Yes>` 로 포커스 이동 → `Enter`

→ 파티셔닝 스킴 화면.

## <a id="how-c4"></a>C4. 파티셔닝 스킴

**보이는 것**:
```
Partitioning scheme:

  All files in one partition (recommended for new users)
  Separate /home partition
  Separate /home, /var and /tmp partitions             ← 이걸 선택
```

**할 일**:
1. `↑↓` 로 `Separate /home, /var and /tmp partitions` → `Enter`

**WHY**: 보너스의 WordPress 가 `/var/www` 비대화 → `/var` 분리 필요. 사용자 데이터(/home), 임시 파일(/tmp) 도 시스템 격리. 추후 `/srv`, `/var/log` 는 LV 로 직접 추가.

→ 파티션 표 자동 생성 진행. 수 초.

## <a id="how-c5"></a>C5. 디스크 채우기 (랜덤 데이터)

**보이는 것**:
```
The selected device contains the following partitions, which are about to be removed:
... 
Erasing data on /dev/sda: ...  
```

**할 일**: **자동 진행**. **시간이 오래 걸림** (10~30 분). VM 의 가상 디스크 전체에 랜덤 데이터를 덮어써 LUKS 암호화의 안전성을 높이는 단계.

**WHY**: LUKS 가 빈 영역과 암호화 영역의 **엔트로피 차이**로 데이터 패턴이 노출되는 걸 막기 위함. 기다리는 게 정답.

> **Tip.** 정말 급하면 `Cancel` → 다시 시작 시 "Erase data?" 에 `<No>` 가능. 단 학습/평가 환경에선 권장 안 함.

→ Encryption passphrase 화면.

## <a id="how-c6"></a>C6. LUKS 암호 입력

**보이는 것**:
```
Encryption passphrase:
[입력란]
```

**할 일**:
1. 강한 비밀번호 입력 (12 자 이상 권장) → `Tab` → `<Continue>` → `Enter`
2. 재입력 → `Tab` → `<Continue>` → `Enter`

**WHY**: 이 암호가 디스크 자체를 보호. **부팅 때마다 매번 입력** 해야 함 → 너무 어렵게 만들면 본인이 못 풀음. root/사용자 비밀번호와는 완전히 별개.

→ Volume Group 할당량 지정 화면 (C6a).

## <a id="how-c6a"></a>C6a. Volume Group 할당량 지정

**보이는 것** (빨간 `[!] Partition disks` 제목의 박스):
```
You may use the whole volume group for guided partitioning, or part
of it. If you use only part of it, or if you add more disks later,
then you will be able to grow logical volumes later using the LVM
tools, so using a smaller part of the volume group at installation
time may offer more flexibility.

The minimum size of the selected partitioning recipe is 8.3 GB (or
39%); please note that the packages you choose to install may require
more space than this. The maximum available size is 20.9 GB.

Hint: "max" can be used as a shortcut to specify the maximum size,
or enter a percentage (e.g. "20%") to use that percentage of the
maximum size.

Amount of volume group to use for guided partitioning:

[20.9 GB]              ← 파란 하이라이트로 기본값이 들어가 있음

  <Go Back>                                       <Continue>
```

**할 일**:
1. **기본값 `20.9 GB` 그대로 두고** (또는 지우고 `max` 타이핑해도 동일) `Tab` → `<Continue>` → `Enter`

**입력 가능 형식**:
- `max` (전체, 단축어) — **본 과제 권장**
- `20.9 GB` (절대값, 기본값)
- `100%` (상대값, max 와 동일)
- `8.3 GB` (최소값, 이 아래는 설치 실패)

**WHY (왜 전체 / `max` 인가)**:
- 이미 sda5 LUKS 컨테이너가 디스크 거의 전부(21 GB)를 차지함. **VG 는 그 LUKS 위**에 만들어짐.
- VG 를 작게 (예: 10 GB) 잡으면 → 나머지 10 GB 는 **암호화는 돼 있는데 LVM 도 모르고 파일시스템도 못 쓰는 죽은 공간** 이 됨. 순수 낭비.
- 안내문의 "나중에 추가 디스크 넣고 확장 여유" = 물리 디스크를 더 꽂는 경우의 이야기. 본 VM 은 단일 디스크라 무의미.
- 추가 LV (`srv`, `var-log`) 가 필요해도 → **VG 안에서 큰 LV 를 줄이거나 삭제해서 빈 공간 확보** ([C9](#how-c9)~[C10](#how-c10)) 가 표준. VG 자체를 작게 잡는 게 아님.

**막히기 쉬운 지점**:
- 입력란 기본 하이라이트 상태에서 **아무 키나 타이핑하면 기존 `20.9 GB` 가 즉시 지워지고** 새 값이 들어감. 실수로 숫자 키 눌렀다면 → 백스페이스로 지우고 `max` 다시 입력하거나 `<Go Back>` 으로 돌아가 재진입.
- `8.3 GB` 미만 입력 시 에러 메시지 후 재입력. (선택한 파티셔닝 레시피 `Separate /home, /var, /tmp` 의 합계 최소치가 8.3 GB.)

→ 자동 파티션 표 생성 (수 초). 잠시 후 메인 파티션 화면 ([C7](#how-c7)).

## <a id="how-c7"></a>C7. 자동 생성된 파티션 표 확인

**보이는 것** (이게 메인 파티션 화면, 앞으로 자주 돌아옴):
```
This is an overview of your currently configured partitions and mount points.

  Configure software RAID
  Configure the Logical Volume Manager
  Configure encrypted volumes
  Configure iSCSI volumes

  Encrypted volume (sda5_crypt) - 21.0 GB Linux device-mapper (crypt)
        #1                            21.0 GB     f ext4

  LVM VG <hostname>-vg, LV home - X.X GB Linux device-mapper (linear)
        #1                            X.X GB     f ext4         /home
  LVM VG <hostname>-vg, LV root - X.X GB Linux device-mapper (linear)
        #1                            X.X GB     f ext4         /
  LVM VG <hostname>-vg, LV swap_1 - X.X GB Linux device-mapper (linear)
        #1                            X.X GB     f swap         swap
  LVM VG <hostname>-vg, LV tmp - X.X GB Linux device-mapper (linear)
        #1                            X.X GB     f ext4         /tmp
  LVM VG <hostname>-vg, LV var - X.X GB Linux device-mapper (linear)
        #1                            X.X GB     f ext4         /var

  SCSI3 (0,0,0) (sda) - 21.5 GB ATA VBOX HARDDISK
        #1 primary  500.0 MB   B f ext2          /boot
        #5 logical  21.0 GB         crypto       (sda5_crypt)

  Undo changes to partitions
  Finish partitioning and write changes to disk
```

**확인 포인트**:
- `sda1` = `/boot` (500 MB, 평문, ext2) ← LUKS 바깥
- `sda5` = LUKS 컨테이너 → 풀면 `sda5_crypt` → 그 안에 LVM
- LVM 안에 `home`, `root`, `swap_1`, `tmp`, `var` 5 개 LV 자동 생성
- **VG 가 거의 다 찼음** → `srv`, `var-log` 추가하려면 빈 공간이 없음 → [C8](#how-c8) 단계 필수

**할 일**: 일단 아무것도 하지 말고 다음 단계로.

## <a id="how-c8"></a>C8. LVM 메뉴 진입 — 큰 LV 삭제로 빈 공간 확보

**할 일**:
1. `↑↓` 로 위쪽 `Configure the Logical Volume Manager` → `Enter`
2. 변경 사항을 디스크에 기록할지 묻는 화면:
   ```
   Before the Logical Volume Manager can be configured, ...
   Write the changes to disks and configure LVM?
       <No>          <Yes>      ← Yes
   ```
   `Tab` → `<Yes>` → `Enter`

→ LVM 메뉴 화면.

**보이는 것**:
```
LVM configuration action:

  Display configuration details
  Create volume group
  Delete volume group
  Extend volume group
  Reduce volume group
  Create logical volume
  Delete logical volume
  Finish
```

**WHY (중요)**: **이 메뉴에는 LV 크기 변경 옵션이 없습니다.** LV 크기를 바꾸려면 **삭제 후 재생성**이 표준. 이걸 모르면 영원히 막힘.

## <a id="how-c9"></a>C9. 큰 LV 삭제 (home, var, tmp 모두 삭제)

20 GB 디스크 기준, Guided 가 만든 LV 들 중 `home` 이 보통 디스크의 절반(약 10 GB) 차지. 보너스 위해 `srv` `var-log` 를 추가하려면 빈 공간 확보 필요.

**전략**: home, var, tmp 모두 삭제 후 적절한 크기로 재생성. (root 와 swap_1 은 그대로 둬도 됨.)

**할 일**:
1. `Delete logical volume` → `Enter`
2. VG 선택 화면 → 본인 VG (보통 `<hostname>-vg`) → `Enter`
3. LV 목록 → `home` 선택 → `Enter`
4. 확인 화면 `<Yes>` → `Enter`

→ 다시 LVM 메뉴로 돌아옴.

5. 위 1~4 반복:
   - `Delete logical volume` → VG → `var` → `<Yes>`
   - `Delete logical volume` → VG → `tmp` → `<Yes>`

이제 VG 에 빈 공간이 충분해짐. (필요시 `Display configuration details` 로 Free PE 확인.)

## <a id="how-c10"></a>C10. LV 7 개 새로 생성 (Subject Bonus 예시 기준)

Subject v5.2 Chapter VII (Bonus) 의 lsblk 예시:

```
# lsblk
NAME                     MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda                        8:0    0  30.8G  0 disk
 ├─sda1                    8:1    0   500M  0 part  /boot
 ├─sda2                    8:2    0     1K  0 part
 └─sda5                    8:5    0  30.3G  0 part
    └─sda5_crypt         254:0    0  30.3G  0 crypt
       ├─LVMGroup-root   254:1    0    10G  0 lvm   /
       ├─LVMGroup-swap   254:2    0   2.3G  0 lvm   [SWAP]
       ├─LVMGroup-home   254:3    0     5G  0 lvm   /home
       ├─LVMGroup-var    254:4    0     3G  0 lvm   /var
       ├─LVMGroup-srv    254:5    0     3G  0 lvm   /srv
       ├─LVMGroup-tmp    254:6    0     3G  0 lvm   /tmp
       └─LVMGroup-var--log 254:7  0     4G  0 lvm   /var/log
```

이 예시는 30 GB 디스크 기준. **본 가이드는 20 GB 디스크** 이므로 비율 조정:

| LV 이름 | Subject 예시 (30 GB) | 20 GB 권장 | 용도 | 마운트 |
|---|---|---|---|---|
| `root` | 10 GB | 7 GB | 시스템 | `/` |
| `swap_1` | 2.3 GB | 1.5 GB | 스왑 | swap |
| `home` | 5 GB | 3 GB | 사용자 데이터 | `/home` |
| `var` | 3 GB | 2 GB | 가변 데이터 | `/var` |
| `srv` | 3 GB | 1 GB | 서비스 데이터 | `/srv` |
| `tmp` | 3 GB | 1 GB | 임시 파일 | `/tmp` |
| `var-log` | 4 GB | 2 GB | 로그 분리 | `/var/log` |
| **합계** | 30.3 GB | **17.5 GB** | (여유 3 GB) | |

> **Note**: 20 GB 디스크에 Subject 예시 그대로 넣으면 30 GB 초과라 안 들어감. 위 20 GB 권장 사용.

**할 일** (각 LV 마다 반복):

### home LV 생성
1. `Create logical volume` → `Enter`
2. VG 선택 → `Enter`
3. `Logical volume name:` → `home` 입력 → `Tab` → `<Continue>` → `Enter`
4. `Logical volume size:` → `3 GB` 입력 → `Tab` → `<Continue>` → `Enter`

→ LVM 메뉴로 돌아옴.

### var LV 생성
1. `Create logical volume` → VG → `Enter`
2. Name: `var` → `<Continue>`
3. Size: `2 GB` → `<Continue>`

### var-log LV 생성
1. Name: `var-log` / Size: `2 GB`

### srv LV 생성
1. Name: `srv` / Size: `1 GB`

### tmp LV 생성
1. Name: `tmp` / Size: `1 GB`

> **에러 "Not enough free space"**: VG 빈 공간이 부족. `Display configuration details` 로 남은 공간 확인 후, 너무 큰 LV (예: root) 도 삭제 후 작게 다시 만들기.

## <a id="how-c11"></a>C11. LVM 메뉴 종료

1. `Finish` → `Enter`

→ 메인 파티션 화면 ([C7](#how-c7) 의 화면) 으로 돌아옴. 새 LV 들이 표시되지만 **마운트 포인트가 없음** (아직 사용 안 함 상태).

## <a id="how-c12"></a>C12. 각 LV 에 ext4 + 마운트 포인트 설정

**핵심**: 새로 만든 LV 는 마운트 포인트와 파일시스템을 직접 지정해야 사용 가능.

### home LV 설정

**보이는 것** (메인 파티션 화면에서 `home` LV 항목):
```
LVM VG <hostname>-vg, LV home - 3.0 GB Linux device-mapper (linear)
      #1                          3.0 GB     do not use      
```

**할 일**:
1. `↑↓` 로 위 `#1` 줄 (`do not use`) 선택 → `Enter`

→ 파티션 설정 화면.

**보이는 것**:
```
Partition settings:

  Use as:                        do not use
  
  Copy data from another partition
  Erase data on this partition
  Delete the partition
  Done setting up the partition
```

2. `Use as:` 선택 → `Enter`
3. 파일시스템 목록에서 `Ext4 journaling file system` → `Enter`

→ 화면이 바뀌어 마운트 옵션 등이 추가로 보임:
```
Partition settings:

  Use as:                        Ext4 journaling file system
  
  Mount point:                   none
  Mount options:                 defaults
  Label:                         none
  Reserved blocks:               5%
  Typical usage:                 standard
  
  Erase data on this partition
  Done setting up the partition
```

4. `Mount point:` 선택 → `Enter`
5. 마운트 포인트 목록:
   ```
     /     - the root file system
     /boot - static files of the boot loader
     /home - user home directories
     /tmp  - temporary files
     /usr  - static data
     /var  - variable data
     /srv  - data for services provided by this system
     /opt  - add-on application software packages
     /usr/local - local hierarchy
     Enter manually
     Do not mount it
   ```
6. `/home` → `Enter`
7. `Done setting up the partition` → `Enter`

→ 메인 파티션 화면으로 돌아옴.

### var LV 설정
1. `var` LV 의 `#1` 줄 선택 → `Enter`
2. `Use as:` → `Ext4 journaling file system`
3. `Mount point:` → `/var`
4. `Done setting up the partition`

### var-log LV 설정 (마운트 포인트 직접 입력)
1. `var-log` LV 의 `#1` 줄 선택 → `Enter`
2. `Use as:` → `Ext4 journaling file system`
3. `Mount point:` → 목록에 `/var/log` 가 **없음** → `Enter manually` → `Enter`
4. 입력란에 `/var/log` 입력 → `Tab` → `<Continue>` → `Enter`
5. `Done setting up the partition`

### srv LV 설정
1. `srv` LV 의 `#1` 줄 선택 → `Enter`
2. `Use as:` → `Ext4 journaling file system`
3. `Mount point:` → `/srv`
4. `Done setting up the partition`

### tmp LV 설정
1. `tmp` LV 의 `#1` 줄 선택 → `Enter`
2. `Use as:` → `Ext4 journaling file system`
3. `Mount point:` → `/tmp`
4. `Done setting up the partition`

### root LV (이미 마운트되어 있을 가능성)
- [C9](#how-c9) 에서 root 를 삭제하지 않았다면 `/` 로 이미 마운트됨 → 추가 작업 불필요.
- 삭제하고 재생성했다면 위와 동일하게 `Use as: ext4 → Mount: /` 설정.

### swap_1 LV
- 자동으로 `swap area` 로 잡혀 있음. 마운트 포인트 없음 (정상).

## <a id="how-c13"></a>C13. 파티셔닝 종료

**메인 파티션 화면 확인**: 모든 LV 가 다음과 같이 보여야 함:
```
LVM VG xxx, LV home    - 3.0 GB ... ext4         /home
LVM VG xxx, LV root    - 7.0 GB ... ext4         /
LVM VG xxx, LV srv     - 1.0 GB ... ext4         /srv
LVM VG xxx, LV swap_1  - 1.5 GB ... swap         swap
LVM VG xxx, LV tmp     - 1.0 GB ... ext4         /tmp
LVM VG xxx, LV var     - 2.0 GB ... ext4         /var
LVM VG xxx, LV var-log - 2.0 GB ... ext4         /var/log
```

> **체크**: 모든 줄에 `f ext4` 또는 `f swap` 표시 + 마운트 포인트가 있는지. 없으면 그 LV 다시 설정.

**할 일**:
1. `↓` 로 맨 아래 `Finish partitioning and write changes to disk` → `Enter`

→ 마지막 확인 화면:
```
If you continue, the changes listed below will be written to the disks. ...

The partition tables of the following devices are changed:
  LVM VG ..., LV home
  LVM VG ..., LV root
  ...
  SCSI3 (0,0,0) (sda)

Write the changes to disks?
    <No>          <Yes>     ← Yes
```

2. `Tab` → `<Yes>` → `Enter`

→ 디스크에 파일시스템 생성 (자동, 수 분).

---

# <a id="how-part-d"></a>Part D — 패키지 관리자 ~ 소프트웨어 ~ GRUB ~ 첫 부팅

## <a id="how-d1"></a>D1. Debian 아카이브 미러 설정

**Configure the package manager** 화면이 차례로 진행됨.

### D1-1. 추가 설치 미디어 스캔
**보이는 것**:
```
Scan extra installation media?
    <No>          <Yes>
```
**할 일**: `Tab` → `<No>` → `Enter`

### D1-2. 미러 국가
```
Debian archive mirror country:
  ...
  Korea, Republic of           ← 본인 위치
```
**할 일**: `↑↓` → `Korea, Republic of` → `Enter`

### D1-3. 미러 호스트
```
Debian archive mirror:
  deb.debian.org               ← 권장 (CDN, 자동 가까운 미러)
  ftp.kr.debian.org
  mirror.kakao.com
  ...
```
**할 일**: `deb.debian.org` → `Enter` (또는 `mirror.kakao.com` 도 빠름)

### D1-4. 프록시
**보이는 것**:
```
HTTP proxy information (blank for none):
[입력란]
```
**할 일**: 비워둔 채로 → `Tab` → `<Continue>` → `Enter`

**WHY**: 학교/사내망에서 외부로 직접 못 나갈 때만 프록시 입력. 일반 가정/42 클러스터는 NAT 로 직접 통신 → 비움.

→ 패키지 다운로드 자동 진행.

## <a id="how-d2"></a>D2. popularity-contest

**보이는 것**:
```
Participate in the package usage survey?
    <No>          <Yes>
```
**할 일**: `Tab` → `<No>` → `Enter`

**WHY**: 본 시스템에 깔린 패키지 정보를 외부 통계 서버로 보내는 기능. **공격 표면 축소** + 필요 없는 외부 통신 차단.

## <a id="how-d3"></a>D3. 소프트웨어 선택

**보이는 것**:
```
Choose software to install:

  [ ] Debian desktop environment
  [ ]   ... GNOME
  [ ]   ... Xfce
  [ ]   ... GNOME Flashback
  [ ]   ... KDE Plasma
  [ ]   ... Cinnamon
  [ ]   ... MATE
  [ ]   ... LXDE
  [ ]   ... LXQt
  [ ] web server
  [ ] print server
  [*] SSH server                 ← 체크
  [*] standard system utilities  ← 체크 (기본)
```

**할 일**:
1. `↑↓` 로 항목 이동 + `Space` 로 토글
2. **모든 데스크탑/web/print 항목은 `[ ]` (체크 해제)**
3. **`SSH server` 만 `[*]` 로** (Space 로 체크)
4. **`standard system utilities` 는 `[*]` 유지** (기본 체크 됨)
5. `Tab` → `<Continue>` → `Enter`

**WHY**:
- 데스크탑 환경 = 그래픽 환경. 서브젝트가 **명시적으로 금지** ("X.org/Wayland 등 그래픽 서버 설치 시 0 점").
- web/print server = 보너스 시 직접 `apt install lighttpd` 로 설치할 것이라 여기서 안 깜.
- SSH server = 평가에서 4242 포트로 접속해야 하므로 필수.
- standard system utilities = bash, less, grep, nano 등 운영 필수 도구.

→ 패키지 다운로드/설치 (자동, 수 분).

## <a id="how-d4"></a>D4. GRUB 부트로더 설치

### D4-1. GRUB 설치 여부
**보이는 것**:
```
Install the GRUB boot loader to your primary drive?
    <No>          <Yes>          ← Yes
```
**할 일**: `Tab` → `<Yes>` → `Enter`

### D4-2. 디바이스 선택
```
Device for boot loader installation:
  Enter device manually
  /dev/sda (ata-VBOX_HARDDISK_VBxxxxxx)   ← 이걸 선택
```
**할 일**: `↓` → `/dev/sda ...` → `Enter`

**WHY**: GRUB 의 boot.img 가 `/dev/sda` 의 MBR (첫 446 바이트) 에 박힘. 이게 BIOS 다음으로 실행돼 `/boot/grub/` 의 커널을 메모리에 올림. `/boot` 가 평문(LUKS 바깥)이어야 GRUB 이 읽을 수 있음.

→ GRUB 설치 자동 진행.

## <a id="how-d5"></a>D5. 설치 완료 → 재부팅

**보이는 것**:
```
Installation complete

Installation is complete, so it is time to boot into your new system. ...
        <Continue>
```

**할 일**:
1. `Enter`

→ VM 자동 재부팅.

## <a id="how-d6"></a>D6. 첫 부팅 — LUKS 암호 입력

**보이는 것** (검은 화면):
```
Please unlock disk sda5_crypt:
[입력란]
```

**할 일**:
1. [C6](#how-c6) 에서 설정한 LUKS 암호 입력 → `Enter`

**WHY**: GRUB → 커널 로드 → 커널이 LUKS 모듈 로드 → 사용자에게 암호 요청 → sda5 풀어 LVM 인식 → root LV 마운트.

→ GRUB 메뉴 (5 초 카운트다운 후 자동) → 부팅 메시지 → 로그인 프롬프트.

## <a id="how-d7"></a>D7. 첫 로그인

**보이는 것**:
```
Debian GNU/Linux 12 <hostname> tty1

<hostname> login: 
```

**할 일**:
1. 사용자명 (예: `dongjki`) → `Enter`
2. 비밀번호 → `Enter`

**WHY**: root 직접 로그인은 평가 끝까지 거의 안 함. 일반 사용자로 들어가서 필요 시 `su -` 또는 `sudo`.

→ 셸 프롬프트 표시. **OS 설치 끝**.

---

# <a id="how-part-e"></a>Part E — 부팅 후 시스템 설정

여기부터는 셸 명령 위주. 명령은 [README_KR.md](./_reference/README_KR.md) §3~§10 의 것을 그대로 사용하되, **각 명령의 WHY** 와 **막히기 쉬운 지점** 을 인라인으로 보충합니다.

## <a id="how-e1"></a>E1. root 로 전환

```bash
su -
# (root 비밀번호 입력)
```

이후 모든 시스템 설정은 root 에서. 사용자 비번은 평가 시연 때만.

## <a id="how-e2"></a>E2. SSH 설정 (포트 4242)

```bash
systemctl status ssh                      # 설치됐는지 + 포트 확인
nano /etc/ssh/sshd_config                 # 또는 vi
```

**`/etc/ssh/sshd_config` 안에서 수정 (Ctrl+W 로 검색)**:
```
#Port 22                       →   Port 4242        # 주석 # 제거 + 4242
#PermitRootLogin prohibit-pw   →   PermitRootLogin no
```

저장 (`Ctrl+O` → `Enter` → `Ctrl+X`).

```bash
systemctl restart ssh
ss -tlnp | grep 4242                      # 4242 LISTEN 확인
```

**WHY**: 22 = 봇 무차별 대입의 90 % 표적 → 4242 로 옮겨 표면 축소. `PermitRootLogin no` = 공격자가 일반 사용자 → sudo 의 2 단계를 더 뚫게 강제.

**VBox 포트 포워딩 (호스트에서 SSH 접속용)**:
1. VBox 메인 창 → VM 선택 → `Settings` → `Network` → `Adapter 1` → `Advanced` → `Port Forwarding`
2. `+` 버튼 → 새 규칙:
   - Name: `SSH`
   - Protocol: `TCP`
   - Host IP: (비움)
   - Host Port: `2222`
   - Guest IP: (비움)
   - Guest Port: `4242`
3. `[OK]` → `[OK]`

**호스트 터미널에서 테스트**:
```bash
ssh <login>@localhost -p 2222
```

**WHY (포트 포워딩)**: VBox NAT 모드의 게스트 IP(10.0.2.15) 는 사설 → 호스트에서 직접 접근 불가. NAT 매핑 필요.

## <a id="how-e2-5"></a>E2.5. AppArmor 확인 (Subject 필수)

Subject Chapter V: *"AppArmor for Debian must be running at startup"*

```bash
/usr/sbin/aa-status                       # apparmor module is loaded 확인
systemctl is-enabled apparmor             # enabled 확인
systemctl status apparmor                 # active (exited) 확인
```

**기대 출력 (정상)**:
```
apparmor module is loaded.
N profiles are loaded.
N profiles are in enforce mode.
...
```

**만약 비활성**:
```bash
systemctl enable apparmor
systemctl start apparmor
```

**WHY**: AppArmor = Debian 기본 MAC(Mandatory Access Control). 프로세스별 경로 기반 권한 제한 → 취약점 악용 시에도 피해 범위 제한. Subject 는 시작 시 작동을 **명시적으로 요구** → `systemctl is-enabled` 가 `enabled` 여야 평가 통과.

**평가 단골**: "AppArmor vs SELinux 차이?" → AppArmor 는 경로 기반(간단, Debian 기본), SELinux 는 라벨 기반(정교, Rocky/RHEL 기본).

## <a id="how-e3"></a>E3. UFW 방화벽

```bash
apt install ufw
ufw default deny incoming
ufw default allow outgoing
ufw allow 4242
ufw enable                                # y 입력
ufw status verbose                        # 상태 확인
```

**Subject 검증 명령**:
```bash
/usr/sbin/ufw status                      # active 확인
ss -tunlp                                 # 4242 만 LISTEN 확인
```

**WHY**: 화이트리스트 정책 (기본 거부, 명시 허용). 4242 외 모든 수신 차단. 보너스 시 `ufw allow 80` (lighttpd), `ufw allow 19999` (Netdata) 추가.

## <a id="how-e4"></a>E4. sudo 와 그룹

```bash
apt install sudo
usermod -a -G sudo <login>                # sudo 그룹에 추가
groupadd user42
usermod -a -G user42 <login>              # user42 그룹에 추가 (서브젝트 요구)
getent group sudo user42                  # 멤버십 확인
```

**sudo 정책 설정**:
```bash
mkdir -p /var/log/sudo
visudo
```

**`/etc/sudoers` 끝에 추가** (Defaults 블록, Subject 예시 그대로):
```
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults  requiretty
Defaults  badpass_message="WRONG PASSWORD"
Defaults  logfile="/var/log/sudo/sudo.log"
Defaults  log_input
Defaults  log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  passwd_tries=3
```

저장 (Ctrl+X → Y → Enter, 또는 `:wq`).

**WHY 요약**: secure_path = PATH 변조 방지 / requiretty = 데몬 sudo 차단 / log_input/output = 감사 로그 / passwd_tries=3 = 무한 시도 차단 / `/snap/bin` 포함 = Subject Chapter V 경로 제한 예시의 마지막 항목.

> **Note**: Subject 예시는 `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin` 인데 Debian 에 snap 이 기본 설치 아님. 그래도 예시 그대로 적어두면 평가 시 Subject 일치 확인 가능.

## <a id="how-e5"></a>E5. 비밀번호 정책

**`/etc/login.defs` 수정**:
```bash
nano /etc/login.defs
```

다음 줄 찾아 수정:
```
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
PASS_WARN_AGE   7
```

**기존 사용자에게 소급 적용**:
```bash
chage -M 30 -m 2 -W 7 <login>
chage -M 30 -m 2 -W 7 root
chage -l <login>                          # 확인
```

**비밀번호 강도 정책 (pwquality)**:
```bash
apt install libpam-pwquality
nano /etc/pam.d/common-password
```

다음 줄 찾기:
```
password   requisite   pam_pwquality.so retry=3
```

다음으로 변경:
```
password   requisite   pam_pwquality.so retry=3 minlen=10 difok=7 maxrepeat=3 dcredit=-1 ucredit=-1 lcredit=-1 reject_username enforce_for_root
```

**Subject 필수 — 설정 후 전체 계정 비번 재설정**:

Subject Chapter V: *"After setting up your configuration files, you will have to change all the passwords of the accounts present on the virtual machine, including the root account."*

```bash
passwd <login>                            # 사용자 비번 변경 (새 정책 적용 확인)
passwd root                               # root 비번 변경
```

**WHY**:
- 30/2/7 = 유출 시간 제한 + 즉시 회피 차단 + UX
- minlen=10/difok=7 = 길이/변경 강제
- dcredit=-1 의 음수 = "최소 1 자 강제" (양수면 보너스 점수 의미)
- **`difok=7` 는 root 에 적용 안 됨**: Subject 에 명시 — *"The following rule does not apply to the root password: the password must contain at least 7 characters that were not part of the previous password."* 즉 `enforce_for_root` 를 켜도 difok 만은 root 예외 (pwquality 내부 로직).
- **비번 재설정 필수 이유**: 설정 전에 만든 비번은 새 정책을 통과한 게 아님. 재설정해야 pwquality 검증을 통과한 비번으로 갱신 → 평가 시 정책 적용 증명.

## <a id="how-e6"></a>E6. 모니터링 스크립트와 cron

Subject Chapter V: *"At server startup, the script will display the information listed below on all terminals, and every 10 minutes"*

```bash
apt install bc sysstat
nano /root/monitoring.sh                  # 또는 /etc/cron.d/monitoring.sh 
chmod +x /root/monitoring.sh
```

스크립트 내용은 README_KR.md §7 또는 본인 작성 (12 항목 출력). 12 항목 = 시스템 정체(Architecture, CPU, vCPU, LVM) + 자원(Memory, Disk, CPU load) + 시간(Last boot) + 활동(TCP, User log, Network, Sudo).

**(1) 10 분 주기 cron 등록**:
```bash
crontab -e
```

마지막 줄에 추가:
```
*/10 * * * * bash /root/monitoring.sh | wall
```

**(2) "서버 시작 시 실행" — 두 가지 방법 중 택 1**:

**방법 A**: `@reboot` cron (가장 간단):
```
@reboot bash /root/monitoring.sh | wall
```

→ 재부팅 후 cron 데몬이 뜨면 1 회 실행.

**방법 B**: systemd oneshot service (정석):

```bash
nano /etc/systemd/system/monitoring-startup.service
```

```ini
[Unit]
Description=Monitoring script at startup
After=network.target

[Service]
Type=oneshot
ExecStart=/bin/bash -c '/root/monitoring.sh | wall'

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl enable monitoring-startup.service
```

**WHY (10 분 주기)**: 1 분 = wall 로 매분 도배 → 작업 방해. 1 시간 = 변화 감지 늦음. 10 분이 균형.
**WHY (시작 시 실행)**: Subject 가 `"At server startup"` 을 명시적으로 요구. 평가 시 VM 재부팅 후 wall 메시지 1 회 표시 여부 체크 가능성.

**평가 단골: "수정 없이 일시 중지?"** → `crontab -e` 후 해당 줄 앞에 `#` 주석 (systemd service 는 `systemctl stop monitoring-startup`).

## <a id="how-e7"></a>E7. (보너스) WordPress 스택

명령 시퀀스는 README_KR.md §8 그대로. 중요 WHY 만:

- `apt install lighttpd mariadb-server php php-pdo php-mysql php-zip php-gd php-mbstring php-curl php-xml php-pear php-bcmath php-opcache php-json php-cgi`
  - lighttpd = NGINX/Apache 금지로 인한 경량 대체
  - mariadb = MySQL 의 free fork, Debian 표준
- `lighttpd-enable-mod fastcgi fastcgi-php` → FastCGI = PHP 워커 상주 → CGI 보다 압도적으로 빠름
- `ufw allow http` → 80 포트 개방
- VBox 포트 포워딩 추가: 호스트 8080 → 게스트 80
- DB 셋업 (mariadb 프롬프트): `CREATE DATABASE`, `CREATE USER`, `GRANT ALL`, `FLUSH PRIVILEGES`
- WordPress 다운로드 후 `chown -R www-data:www-data /var/www/html/` ← lighttpd 가 www-data 권한으로 실행되므로 필수

## <a id="how-e8"></a>E8. (보너스) Netdata

```bash
apt install netdata
ufw allow 19999
nano /etc/netdata/netdata.conf
```

`[web]` 섹션에서:
```
bind to = 0.0.0.0
```

```bash
systemctl restart netdata
systemctl is-enabled netdata              # enabled 확인
```

VBox 포트 포워딩: 호스트 19998 → 게스트 19999.

호스트 브라우저: `http://localhost:19998`

**WHY 0.0.0.0**: 기본은 localhost(127.0.0.1) 만 listen → 외부 접근 불가. 0.0.0.0 = 모든 인터페이스 listen → NAT 통한 호스트 접근 가능. (운영에선 위험하나 NAT+UFW 뒤라 학습 OK.)

## <a id="how-e9"></a>E9. 서명과 스냅샷

**VM 종료**:
```bash
systemctl poweroff
```

**호스트에서**:
```bash
cd "~/VirtualBox VMs/born2beroot"
sha1sum *.vdi > signature.txt
cat signature.txt                         # 예: 6e657c4619944be17df3c31faa030c25e43e40af
```

**VBox 스냅샷**:
1. VBox 메인 창 → VM 우클릭 → `Snapshot` → `[Take]`
2. Name: `for-evaluation-2026-04-18` (날짜 명시)
3. `[OK]`

**v5.2 정책 요약** (Subject Chapter IV & VIII):
- 각 평가는 **스냅샷이 없는 상태**에서 시작
- 평가 시작 시 만들고 → 평가 후 즉시 삭제
- VM 자체를 Git 에 올리는 것 **절대 금지** → 0 점
- README.md 와 signature.txt 둘 다 Git 루트에 제출

**WHY**: VM 켜기만 해도 .vdi 가 변경 → sha1 깨짐. 스냅샷으로 원본 보존. SHA-1 의 1:1 매칭 성질로 무결성 증명 (제출 시점 == 평가 시점).

---

# <a id="how-part-f"></a>Part F — 자가 점검 (30 개)

평가자가 무작위로 골라 물었을 때 **각 항목당 30 초 이내** 답변 가능해야 합니다.

**설치 단계 ([Part A](#how-part-a)~[Part D](#how-part-d))**
1. ☐ Skip Unattended 를 켜는 이유
2. ☐ EFI 끄고 BIOS 모드로 가는 이유 (MBR 부팅)
3. ☐ Pre-Allocate Full Size 의 I/O 이점
4. ☐ hostname 규칙 `<login>42` 의 근거 (서브젝트)
5. ☐ Domain name 비우는 이유
6. ☐ root / 사용자 / LUKS 비밀번호 셋의 보호 대상 차이
7. ☐ Guided encrypted LVM 의 4 층 스택 (디스크→LUKS→LVM→ext4)
8. ☐ VG 할당량 max 로 두는 이유 ([C6a](#how-c6a))
9. ☐ "Erase data" 단계가 시간이 오래 걸리는 이유 (랜덤 데이터 → 엔트로피)
10. ☐ LVM 메뉴에 "리사이즈" 가 없는 이유 → Delete + Create 패턴
11. ☐ /home, /var, /tmp 분리의 보안·운영 이점
12. ☐ /var/log 별도 LV 의 이점
13. ☐ /usr 를 분리하지 않는 이유 (UsrMerge)
14. ☐ ext4 선택 근거 (안정성 + LV 축소 호환)
15. ☐ /boot 가 LUKS 바깥(평문)이어야 하는 이유 (GRUB 이 커널을 읽어야)
16. ☐ popularity-contest 거부 이유
17. ☐ standard system utilities + SSH 만 체크하는 이유

**부팅 후 ([Part E](#how-part-e))**
18. ☐ SSH 4242 포트 + PermitRootLogin no 의 보안 효과
19. ☐ NAT 모드 + 포트 포워딩 필요 이유
20. ☐ **AppArmor 시작 시 작동 확인 명령** ([E2.5](#how-e2-5))
21. ☐ UFW deny incoming 화이트리스트 정책의 의미
22. ☐ sudo 의 secure_path / requiretty / log_input/output 각각의 위협
23. ☐ user42 와 sudo 두 그룹의 역할 차이
24. ☐ PASS_MAX_DAYS=30 / MIN_DAYS=2 / WARN_AGE=7 의 의미
25. ☐ pwquality dcredit=-1 음수의 의미, difok 가 root 에 예외인 이유
26. ☐ 모니터링 12 항목의 4 카테고리, 10 분 주기 + 시작 시 실행 구현 ([E6](#how-e6))
27. ☐ "수정 없이 cron 일시 중지" 방법
28. ☐ NGINX/Apache 금지 → lighttpd, FastCGI 가 빠른 이유
29. ☐ Netdata 의 0.0.0.0 바인딩 의미
30. ☐ sha1sum + 스냅샷 v5.2 정책 (각 평가 = 스냅샷 없는 상태에서 시작)

전부 ✓ 되면 평가 통과 가능.

---

# 부록 — 막힐 때 빠른 진단

| 증상 | 원인 | 해결 |
|---|---|---|
| 부팅 시 GRUB 이 안 보임 | EFI 모드로 설치됨 | VM 설정 → System → Motherboard → "Enable EFI" 끄고 재설치 |
| LUKS 암호 입력 안 받음 | 키보드 레이아웃 다름 | 영문 비번 권장 (한글 키보드면 ASCII만) |
| VG 크기 입력 화면 막힘 | C6a 를 빼먹음 | [C6a](#how-c6a) 복귀, 기본값 그대로 Continue |
| 메인 파티션 화면에서 새 LV 가 "do not use" | ext4 + 마운트 포인트 미설정 | [C12](#how-c12) 다시 |
| LVM 메뉴 "Not enough free space" | VG 빈 공간 0 | [C9](#how-c9) 처럼 큰 LV (root/home) 도 삭제 후 작게 재생성 |
| /var/log 마운트 포인트가 목록에 없음 | 정상 (FHS 표준 외) | "Enter manually" → /var/log 직접 입력 |
| 미러 다운로드 실패 | 네트워크 NAT 문제 | VM 끄고 VBox 네트워크 어댑터 NAT 확인, 재시작 |
| 첫 부팅 후 SSH 접속 안 됨 | 포트 포워딩 누락 또는 sshd_config 오타 | [E2](#how-e2) 의 포트 포워딩 추가 + `systemctl status ssh` 확인 |
| `ufw enable` 후 SSH 끊김 | 4242 미허용 상태에서 enable | `ufw allow 4242` 먼저 실행했어야 함. VM 콘솔에서 `ufw allow 4242` 후 재접속 |
| AppArmor 비활성 | 설치 누락 (netinst 기본엔 있음) | `apt install apparmor apparmor-utils && systemctl enable --now apparmor` |
| sudo 후 "no tty present" | requiretty 인데 비대화식 호출 | 정상. tty 에서 직접 실행 |
| 새 비번이 "BAD PASSWORD" 거부 | pwquality 정책 위반 | minlen=10, 대/소/숫자 각 1 이상, difok=7, 사용자명 미포함 확인 |
| monitoring 시작 시 안 뜸 | @reboot 또는 systemd service 미등록 | [E6](#how-e6) (2) 의 방법 A 또는 B 재확인 |
| .vdi 해시가 평가마다 바뀜 | VM 부팅 시 자동 변경 | 스냅샷으로 원본 보존 ([E9](#how-e9)) |

---

# 출처

- [VirtualBox Manual Ch. 1](https://www.virtualbox.org/manual/ch01.html) (Skip Unattended, VM 생성 마법사)
- [VirtualBox Manual Ch. 6](https://www.virtualbox.org/manual/ch06.html#network_nat) (NAT, 포트 포워딩)
- [Debian Installation Guide (Bookworm)](https://www.debian.org/releases/bookworm/amd64/) (전체 installer 흐름)
- [Debian Wiki — LVM](https://wiki.debian.org/LVM) (LV 삭제/생성 패턴)
- [Debian Wiki — AppArmor](https://wiki.debian.org/AppArmor) (aa-status, enable/start)
- [cryptsetup FAQ](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/FrequentlyAskedQuestions) (LUKS 계층)
- [FHS 3.0](https://refspecs.linuxfoundation.org/FHS_3.0/) (/srv, /var/log)
- [OpenBSD sshd_config(5)](https://man.openbsd.org/sshd_config) (PermitRootLogin)
- [sudoers manual](https://www.sudo.ws/docs/man/sudoers.man/) (Defaults 옵션)
- [pwquality.conf(5)](https://www.man7.org/linux/man-pages/man5/pwquality.conf.5.html) (credit 음수)
- [GNU GRUB Manual](https://www.gnu.org/software/grub/manual/grub/grub.html) (MBR 부팅)
- [systemd.service(5)](https://www.freedesktop.org/software/systemd/man/systemd.service.html) (oneshot, Type)
- 짝 자료: [`README_KR.md`](./_reference/README_KR.md) (시간 순 절차서)
- 깊이 자료: [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) (BIOS→GRUB→커널 흐름)
- 원문: [`en_subject_born2beroot.pdf`](./_reference/en_subject_born2beroot.pdf) (Subject v5.2)
