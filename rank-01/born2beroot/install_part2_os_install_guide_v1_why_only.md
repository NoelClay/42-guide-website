# 📠 Born2beRoot "왜" 가이드 — 학부 수준 완결판

본 문서는 두 가지 문서와 짝을 이루어 **백지에서 다시 VirtualBox 를 켜고 처음부터 끝까지 재현 가능한** 수준의 완결성을 목표로 합니다.

| 문서 | 역할 |
|---|---|
| [`README_KR.md`](./_reference/README_KR.md) | **HOW**: 어떤 명령을 어떤 순서로 입력하는지 (시간 순 절차서) |
| **본 문서** | **WHY**: 각 명령·선택의 근거 (학부 전공서적 수준의 이유) |
| [v5.2 메인 가이드](./install-your-virtual-machine_new_v5_2.md) | OS·하드웨어 선택의 사전 결정 (Part 0~1) |

깊이 약속: **컴퓨터공학 학부 전공서적(예: Silberschatz, Tanenbaum) 이상으로는 절대 들어가지 않습니다.** 박사·테크리더 급 디테일은 의도적으로 배제. 본 가이드 한 번 + README_KR.md 한 번이면 평가 통과 + 다음에 백지에서 다시 셋업 가능한 정도면 충분합니다.

원형 자료: `_reference/answer.md` (사용자가 직접 답한 선행 질문).
선수 자료: [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) (BIOS → MBR → GRUB → 커널 → initramfs 흐름).

---

## 본 문서가 README_KR.md 의 어디를 보충하는가

| README_KR.md 섹션 | 본 문서 섹션 | 핵심 질문 |
|---|---|---|
| §1.3 시작하기 | **A** | Skip Unattended 왜 켜나, .vdi 어디 두나 |
| §2.1 부팅 | **B** | hostname/도메인/3종 비밀번호의 역할 차이 |
| §2.2 파티셔닝 | **C, D** | LUKS+LVM 4 층 스택, /home·/var·/tmp 분리 근거, /boot 가 왜 평문 |
| §2.2 끝 ~ 설치 마무리 | **E** | 미러/프록시/popcon/GRUB/소프트웨어 선택 |
| §3 SSH | **F** | 4242 포트·PermitRootLogin no·NAT 포워딩 |
| §4 방화벽 | **G** | UFW 화이트리스트, 4242 만 허용 |
| §5 sudo와 그룹 | **H** | secure_path/requiretty/log, user42 그룹 |
| §6 비밀번호 정책 | **I** | 30/2/7 일수, pwquality 옵션별 근거 |
| §7 모니터링 | **J** | 12 항목 의미, cron 10 분, wall 역할, 중지 방법 |
| §8 WordPress | **K** | lighttpd/MariaDB/PHP/FastCGI/www-data |
| §9 추가 서비스 | **L** | Netdata 선택 근거, 19999/0.0.0.0 바인딩 |
| §10 서명/스냅샷 | **M** | sha1sum 무결성, 스냅샷 v5.2 정책 |

---

## A. VirtualBox VM 생성 마무리

### A1. "Skip Unattended Installation" 을 왜 체크하나?

- **Unattended Installation** = VirtualBox 가 ISO 를 인식해서 사용자명·비밀번호·키보드·디스크 파티션까지 **자동으로** 답해주고 OS 를 깔아 버리는 모드입니다 [[1]](https://www.virtualbox.org/manual/ch01.html#create-vm-wizard-1-2-unattended).
- Born2beRoot 의 본질은 **파티셔닝(LVM + LUKS), 사용자/그룹/sudo, SSH 포트, UFW, hostname** 같은 모든 결정을 **본인이 직접** 하는 것입니다.
- 자동 설치를 켜면 평가자가 "왜 이 파티션 구조인가?" 라고 물었을 때 **VirtualBox 가 알아서 했다** 라고 답하게 되며, 이 경우 평가는 통과할 수 없습니다.

→ 따라서 **반드시 체크**해서 자동 설치를 끕니다.

### A2. 42 경산/서울 환경에서 `.vdi` 저장 위치

- 42 클러스터 PC 는 `/home/<login>` 의 용량 쿼터가 있어서 20 GB `.vdi` 를 그대로 두면 위험합니다.
- 42 서울: `sgoinfre` 라는 큰 공유 저장소가 사용자별로 할당돼 PC 를 옮겨도 동일하게 접근 가능 → 일반적으로 여기 저장.
- 42 경산: 알타리안(Altarian) 가입 없이는 `sgoinfre` 사용이 제한 → 본인 USB 에 `.vdi` 를 올리고 그 USB 를 클러스터 PC 에 마운트해서 진행하는 방식이 현실적입니다 (사용자 본인 사례).

> **Tip.** USB 사용 시 USB 자체를 `ext4` 로 포맷하고 (윈도우/맥 호환은 포기) UUID 마운트로 두면 PC 를 옮겨도 경로가 동일해집니다.

---

## B. OS 부팅·사용자·호스트 설정

### B1. hostname 규칙: `<login>42`

서브젝트 [`subject_ko_Born2beRoot.md`](./_reference/subject_ko_Born2beRoot.md) 에 명시:

> *Your virtual machine's hostname must be your login ending with **42** (e.g., `wil42`).*

- **이유**: 42 평가 자동화·동료 평가에서 **누구의 VM 인지 즉시 식별**하기 위함. 동시에 평가용 명령(`hostnamectl set-hostname`)이 잘 작동하는지를 확인하는 항목이기도 합니다.

### B2. 도메인 이름은 왜 건너뛰나?

- 도메인 이름(domain name) = 호스트가 속한 **DNS 도메인** 이름 (예: `example.com`). FQDN 은 `<hostname>.<domain>` 형태가 됩니다.
- Born2beRoot 의 VM 은 **단독 서버**이며 외부 DNS 도메인에 등록되지 않으므로 도메인 이름이 사실상 **무의미**합니다. 비워 두면 Debian 은 `localdomain` 같은 기본값을 쓰지 않고 **빈 값**으로 둡니다.
- 영향: `/etc/hosts` 에 `127.0.1.1   <hostname>` 한 줄만 들어가고 끝. 일부 메일/프로그램이 FQDN 을 요구할 때 경고가 뜰 수 있지만 본 과제에서는 무관합니다 [[2]](https://wiki.debian.org/HowTo/ChangeHostname).

### B3. root vs 사용자 vs LUKS 비밀번호 — 셋의 역할

| 비밀번호 | 사용 시점 | 보호 대상 |
|---|---|---|
| **LUKS** | 부팅 직후, GRUB 다음 단계 | **암호화된 파티션 자체** (디스크 도난 방어) |
| **root** | `su -` 또는 `sudo -i` | **시스템 관리자 계정** (root 직접 로그인은 SSH 에서 차단) |
| **사용자 (`<login>`)** | 콘솔/SSH 로그인, `sudo` | **일반 사용자 계정** (sudo 그룹에 포함되어 root 권한 위임 가능) |

- **순서**: 부팅 → LUKS 암호로 디스크 해독 → 부팅 완료 → 사용자 비밀번호로 로그인 → 필요 시 root 비밀번호로 권한 상승.
- 셋은 **물리적으로 다른 계층**을 보호합니다. 같게 설정하면 1개가 새면 전부 새는 셈이 되므로 **반드시 다르게**.
- Born2beRoot 평가에서 자주 나오는 질문: "디스크가 도난당하면 어떻게 되나?" → 답: **LUKS 가 막아준다, root/사용자 비밀번호와는 무관**.

---

## C. 파티셔닝 핵심 — 암호화 + LVM 3 층 스택

여기가 본 과제의 **꽃**입니다. 학부 수준에서 정확히 이 한 그림만 머리에 박혀 있으면 충분합니다.

```
┌─────────────────────────────────────────┐
│  논리 (위)                              │
│   ┌─────────────────────────────────┐   │
│   │  ext4   (파일 시스템)           │   │  ← mkfs.ext4 가 여기 만든다
│   ├─────────────────────────────────┤   │
│   │  LV   (논리 볼륨: root, home …) │   │  ← lvcreate 가 여기 자른다
│   ├─────────────────────────────────┤   │
│   │  VG   (볼륨 그룹: LVMGroup)     │   │  ← vgcreate 가 PV 들을 묶는다
│   ├─────────────────────────────────┤   │
│   │  PV   (물리 볼륨)               │   │  ← pvcreate 가 LUKS 매퍼 위에 만든다
│   ├─────────────────────────────────┤   │
│   │  LUKS  /dev/mapper/sda5_crypt   │   │  ← cryptsetup 이 부팅 시 푼다
│   ├─────────────────────────────────┤   │
│   │  sda5  (논리 파티션, MBR)       │   │  ← Debian 설치기가 잡는다
│   └─────────────────────────────────┘   │
│  물리 (아래)                            │
└─────────────────────────────────────────┘
       ↑ /boot 만 sda1 에서 암호화 바깥
```

### C1. "Guided — use entire disk and set up encrypted LVM"

- **Guided** = Debian 설치기 자체의 **자동 파티션 마법사**. (반대는 "Manual": 한 칸씩 본인이 만든다.)
- **encrypted LVM** = "암호화된 LVM" → 위 그림 그대로 **디스크 → LUKS → LVM → ext4** 스택을 자동으로 깔아줍니다.
- 처음 한 번은 Guided 로 깔고, **이후 추가 LV (`/var`, `/var/log`, `/srv`, `/tmp`, `/home`)** 만 직접 잘라 넣는 것이 권장 워크플로입니다.

### C2. LVM 이 왜 필요한가? — 일반 파티션과 무엇이 다른가

| 항목 | 일반 MBR/GPT 파티션 | LVM |
|---|---|---|
| 크기 변경 | **거의 불가능** (재포맷) | `lvextend` / `lvreduce` 한 줄 |
| 파티션 추가 | MBR 4 개 제한, GPT 도 디스크 끝에 묶임 | VG 안에서 자유 |
| 여러 디스크 합치기 | 불가 | VG 에 PV 여러 개 추가 가능 |
| 스냅샷 | 불가 | `lvcreate --snapshot` |

- **PV (Physical Volume)**: LVM 이 인식하는 "벽돌". 여기서는 `/dev/mapper/sda5_crypt` 1 개.
- **VG (Volume Group)**: PV 들을 모은 "벽돌 더미". 여기서는 `LVMGroup` 1 개.
- **LV (Logical Volume)**: VG 안에서 자른 "방". 우리는 root, swap, home, var, tmp, srv, var-log 등 7~8 개를 만듭니다.

> **MBR/GPT 가 뭔가요?** 디스크 시작 부분에 있는 **파티션 표 형식**. MBR(고전, 2 TB 한계, 4 primary), GPT(현대, 거대, 128 partition). 자세한 내용은 [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) §3.

### C3. LUKS 는 정확히 어느 계층에서 작동하나?

```
파티션(sda5)  ←  LUKS 가 여기 헤더를 박는다
       ↓ 부팅 시 cryptsetup luksOpen
가상 블록 장치 /dev/mapper/sda5_crypt  ←  여기 위에 PV 가 만들어진다
       ↓
PV → VG → LV → ext4
```

즉 **파티션 위, LVM 아래** 입니다. "물리 파티션을 통째로 암호화 → 그 결과물(가상 블록 장치)을 LVM 의 PV 로 사용" 하는 구조 [[3]](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/FrequentlyAskedQuestions).

핵심 포인트:
- LUKS 는 **블록 단위 암호화**여서, 위에 무슨 파일 시스템이 오든 상관없습니다.
- LV 안의 데이터는 LUKS 가 풀려야 비로소 LVM 이 보고, LVM 이 매핑해야 ext4 가 마운트됩니다.

### C4. 멘탈 모델 — "각 층은 위·아래에 무엇을 주고받는가"

| 계층 | 아래 층에서 받는 것 | 위 층에 제공하는 것 |
|---|---|---|
| **물리 디스크** | (실물) | 섹터 |
| **MBR 파티션 (sda5)** | 디스크 섹터 | 연속된 섹터 묶음 (한 파티션) |
| **LUKS** | sda5 파티션 | 복호화된 가상 블록 장치 (`sda5_crypt`) |
| **PV** | sda5_crypt | "벽돌" (LVM 이 관리하는 단위) |
| **VG** | PV(s) | "벽돌 더미" |
| **LV** | VG | 가상 블록 장치 (`/dev/LVMGroup/root` 등) |
| **ext4** | LV | 파일과 디렉토리 (POSIX API) |

학부 수준에서 이 표 한 장이면 평가에서 90 % 의 질문은 막을 수 있습니다.

### C5. VG 크기를 왜 축소해야 추가 LV 공간이 확보되나?

- Guided 모드는 처음에 **VG 의 거의 전부를 root LV 에 할당**해 버립니다. 즉 VG 안에 빈 공간이 거의 없습니다.
- 새 LV (`/srv`, `/var/log` 등)를 만들려면 **VG 안에 빈 공간이 필요** → 그래서 가장 큰 LV (보통 root) 를 줄여야 합니다 [[4]](https://wiki.debian.org/LVM).
- 줄이는 명령은 `lvreduce` + `resize2fs`(파일 시스템도 같이 축소). 단 **마운트된 채로 줄이면 위험** → Live CD 부팅 또는 설치 단계에서 처리.

---

## D. 파티션/LV 분리 설계 — 왜 이 구성인가

### D1a. 보너스 파트가 요구하는 3 분리: `/home`, `/var`, `/tmp`

서브젝트 보너스: WordPress + lighttpd + MariaDB 를 추가로 띄움. → 각각 다른 디렉토리에 부담을 줍니다:
- WordPress 업로드 = `/var/www/...` → **`/var`** 비대화
- 사용자 파일 = **`/home`**
- 임시 파일·세션 = **`/tmp`**

### D1b. 일반 서버 관리 관점에서 이 3 개를 분리하는 이유

| 디렉토리 | 분리 이유 |
|---|---|
| **`/home`** | 사용자가 큰 파일을 채워도 시스템 파티션(`/`)을 침범하지 못함. OS 재설치 시 `/home` 만 남기고 다시 깔 수 있음 |
| **`/var`** | 로그·DB·캐시가 폭주해도 시스템이 죽지 않음. 다른 마운트 옵션(`noexec`, `nosuid`) 적용 가능 |
| **`/tmp`** | 누구나 쓸 수 있는 디렉토리 → `noexec`, `nosuid`, `nodev` 옵션으로 **랜섬웨어/익스플로잇이 `/tmp` 에서 바이너리를 실행하는 것**을 차단 [[5]](https://wiki.archlinux.org/title/Fstab#Filesystem-independent_mount_options) |

핵심은 **장애 격리(`/` 가 안 차게)** + **보안 옵션 적용** 입니다.

### D1c. 왜 `/usr`, `/boot`, `/opt` 는 분리하지 않나?

- **`/usr`**: 현대 리눅스(systemd 시대)는 `/usr` 와 `/` 를 분리하면 부팅 자체가 깨집니다 ([UsrMerge](https://www.freedesktop.org/wiki/Software/systemd/TheCaseForTheUsrMerge/)). 분리하지 않는 게 권장.
- **`/boot`**: 이미 별도 파티션 (`sda1`) 으로 빠져 있습니다. **암호화 바깥**이어야 GRUB 이 커널을 읽을 수 있기 때문. (E3 참고)
- **`/opt`**: 외부에서 받은 큰 패키지(예: VS Code, JetBrains) 가 들어가는 곳. Born2beRoot 에서는 안 씁니다 → 분리 불필요.

### D2a. `/srv` 는 무엇인가?

- FHS (Filesystem Hierarchy Standard) 정의: **"Data for services provided by this system"** [[6]](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03s17.html). 웹 서버 데이터, FTP 데이터 등 외부 서비스용.
- 본 LEMP + WordPress 셋업에서 실제로는 **잘 쓰이지 않습니다** (Debian 의 `lighttpd` 는 `/var/www/html` 사용). 그러나 **분리해 두면 나중에 외부 서비스 데이터를 격리해서 쿼터 걸기 좋다**는 이유로 가이드들이 만들어 두라고 권합니다.

### D2b. `/var/log` 를 별도 LV 로 뽑는 이점

- 모니터링·감사 로그가 폭주해도 `/var` 의 다른 용도(DB, 캐시) 와 **상호 침범하지 않음**.
- Born2beRoot 의 평가 항목 중 하나인 **모니터링 스크립트(`monitoring.sh`)** 결과가 cron 으로 매분 출력 → 로그가 빨리 큼. `/var/log` 분리가 도움 됩니다.
- `noexec`, `nodev` 등 보안 옵션 추가 가능.

### D2c. LV 로 분리 vs 그냥 디렉토리

| 항목 | LV 분리 | 디렉토리만 |
|---|---|---|
| 크기 한계 | 독립 (LV 크기) | `/` 와 공유 |
| 마운트 옵션 (`noexec` 등) | **가능** | 불가 |
| 백업 (스냅샷) | **LV 단위** 가능 | 전체 `/` |
| 로그 폭주 시 시스템 영향 | **격리** | `/` 가 꽉 차 시스템 다운 |

학부 수준에서 외워둘 한 줄: "**LV 분리는 격리(isolation)와 옵션 부여를 위한 것**".

### D3a. Journaling File System 이란?

- 파일을 쓰기 **전에** "이런 변경을 할 거다" 라는 메타데이터를 **저널**(journal)이라는 별도 영역에 먼저 기록 → 진짜 쓰기 → 저널에서 지우기.
- 만약 중간에 전원이 나가면 부팅 시 저널을 보고 **반쯤 끝난 작업을 복구**합니다 [[7]](https://en.wikipedia.org/wiki/Journaling_file_system).
- 없으면 `fsck` 가 디스크 전체를 스캔해야 해서 시간이 오래 걸리고, 일관성도 보장 안 됨.

### D3b. ext4 vs xfs vs btrfs — 왜 ext4 인가?

| FS | 장점 | 단점 |
|---|---|---|
| **ext4** | Debian 기본, 안정성 검증, 거의 모든 도구 지원 | 스냅샷 없음 |
| **xfs** | 큰 파일·고성능 I/O | 축소 불가 |
| **btrfs** | 스냅샷·체크섬·CoW | 복잡, 일부 환경에서 안정성 이슈 |

- Born2beRoot 는 학습 + 평가용 → **익숙함과 재현성**이 우선 → **ext4**.
- 또한 보너스 파트의 LV 축소(`lvreduce`) 와 호환되어야 하는데 **xfs 는 축소가 불가능** → 자동 탈락.

### D3c. 모든 파티션이 ext4 인가? `/boot` 는?

- `/`, `/home`, `/var`, `/tmp`, `/srv`, `/var/log` → **전부 ext4**.
- `/boot` (sda1, 약 500 MB) → 가이드에 따라 **ext2 또는 ext4**. ext2 는 저널이 없는 더 단순한 형태인데, `/boot` 는 거의 읽기만 하므로 저널이 필요 없어 ext2 도 자주 쓰입니다.
- `swap` → 파일 시스템이 아니라 **swap 영역** (`mkswap`).

---

## E. 설치 마무리 단계

### E1. Debian 아카이브 미러와 프록시

- **아카이브(archive)** = Debian 의 모든 패키지(`.deb`)를 모아둔 **공식 저장소**. 본체는 `ftp.debian.org`.
- **미러(mirror)** = 그 저장소를 지역별로 복제해 둔 **속도 최적화용 사본**. 한국이면 `ftp.kr.debian.org` 또는 `mirror.kakao.com`.
- **프록시(proxy)** = 학교/사내망에서 외부로 직접 못 나가고 중간 서버를 거쳐야 할 때 입력. 42 클러스터에서는 보통 **공란**. 직접 망에서 외부 가능하면 비워 두면 됩니다.

### E2. popularity-contest 를 거부하는 이유

- `popularity-contest` = 본인 시스템에 **어떤 패키지가 깔려 있는지**를 익명으로 Debian 통계 서버에 정기 전송하는 패키지 [[8]](https://popcon.debian.org/).
- 본 과제는 **최소 노출(공격 표면 축소)** 이 핵심 → 외부로 나가는 통신은 다 끕니다.
- 또한 평가에서 "running services 를 최소화했나?" 가 항목이라 사용 안 함이 정답입니다.

### E3. GRUB 과 LUKS — `/boot` 가 암호화 바깥인 이유

- **GRUB** (GRand Unified Bootloader) = MBR/EFI 다음 단계에서 **커널을 메모리에 올려 점프**시켜 주는 부트로더 [[9]](https://www.gnu.org/software/grub/manual/grub/grub.html).
- 문제: 커널과 initramfs(`/boot/vmlinuz-…`, `/boot/initrd.img-…`)가 **암호화된 디스크 안에** 있다면, GRUB 은 그 파일을 **읽을 수가 없습니다** (LUKS 를 푸는 코드는 커널 안에 있는데, 커널이 잠겨 있음).
- **해법**: `/boot` 만 별도 파티션(`sda1`)으로 떼서 **암호화하지 않고 평문**으로 둡니다. GRUB 은 여기서 커널을 읽고 메모리에 올림 → 커널이 부팅하면서 LUKS 모듈을 로드 → 사용자에게 LUKS 암호 입력 받음 → `sda5` 를 풀음 → 거기서 `/` 마운트 → 이후 정상 부팅.

```
GRUB → 평문 /boot 에서 커널 읽음 → 커널 시동
         ↓
       사용자에게 "LUKS passphrase:" 물음
         ↓
       /dev/sda5 풀어서 /dev/mapper/sda5_crypt 노출
         ↓
       LVM 인식 → root LV 마운트 → systemd 시작
```

학부 수준에서 외워둘 한 줄: "**`/boot` 가 평문이어야 부팅이 가능하다**".
(자세한 흐름은 [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) §5, §8, §9 참고.)

### E4. 소프트웨어 선택: SSH server + standard system utilities 만

- **GNOME / KDE / Xfce** 등 **데스크톱 환경 체크 해제** → 서브젝트가 **GUI 금지** ("Your server should not have a graphical environment").
- **SSH server** 체크 → 평가 시 호스트에서 `ssh -p 4242 <login>@localhost` 로 접속해야 하므로 **필수**.
- **standard system utilities** = `bash`, `coreutils`, `less`, `grep`, `nano`, `procps`, `sysvinit-utils`, `util-linux` 등 **CLI 운영에 꼭 필요한 기본 패키지 모음** [[10]](https://www.debian.org/releases/stable/amd64/ch06s03.en.html#pkgsel).
- **web server / print server / DNS server** 등은 → **나중에 필요한 것만 `apt install`** 로 깐다. (보너스의 lighttpd 는 여기서 안 깔고 나중에 직접.)

---

## F. SSH 설정

### F1. 왜 포트 22 가 아니라 4242 인가?

- **22 번**은 SSH 의 IANA 표준 포트 → 인터넷의 거의 모든 봇이 자동으로 22 만 스캔/무차별 대입(brute-force)합니다 [[11]](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml).
- 비표준 포트(4242)로 옮기면 **자동화된 봇 공격을 99% 줄일** 수 있습니다 (실제 공격 차단이 아닌 **공격 표면 축소** 기법, security through obscurity 의 합리적 활용).
- 또한 서브젝트가 **명시적으로 4242 를 강제** → 평가 자동화도 4242 로만 접속 시도.

### F2. 왜 `PermitRootLogin no` 인가?

- root 직접 SSH 허용 시: 공격자가 비밀번호 1 개만 뚫으면 즉시 시스템 전체 권한.
- `no` 로 막으면: 공격자는 **(1) 일반 사용자명 추측 → (2) 사용자 비번 → (3) sudo 비번** 의 3 단계를 뚫어야 함 = **인증 방어선 1 → 3 단계로 강화**.
- 이는 "Defense in Depth" 의 가장 단순한 실전 적용입니다 [[12]](https://man.openbsd.org/sshd_config#PermitRootLogin).

### F3. 왜 VirtualBox 포트 포워딩이 필요한가?

- VirtualBox 기본 네트워크 = **NAT 모드** → 게스트 VM 의 IP(10.0.2.15)는 **사설** → 호스트에서도 직접 접근 불가 [[13]](https://www.virtualbox.org/manual/ch06.html#network_nat).
- 호스트:게스트 = 일반 인터넷:NAT 뒤 PC 와 같은 관계 → **포트 포워딩(NAT 매핑)** 이 필요.
- `호스트 2222 → 게스트 4242` 매핑 설정 후 호스트에서 `ssh -p 2222 user@localhost` → VirtualBox 가 4242 로 전달 → 게스트 sshd 응답.

> **Tip.** 게스트에서 4242 외 다른 포트(80, 19999) 도 노출하려면 각각 별도 포트 포워딩 규칙이 필요합니다.

---

## G. 방화벽 (UFW)

### G1. 왜 `default deny incoming` (화이트리스트) 인가?

| 정책 | 동작 | 위험 |
|---|---|---|
| **deny incoming** (화이트리스트) | 명시적으로 허용한 포트만 통과 | 새 서비스 깔면 직접 열어줘야 (실수 방지) |
| allow incoming (블랙리스트) | 명시적으로 차단한 포트만 막음 | 모르는 사이 새 서비스가 외부에 노출됨 |

- 서버 보안의 기본 원칙은 **"기본 거부, 예외 허용"**.
- Born2beRoot 는 학습용이므로 더더욱 모든 포트를 닫고 SSH 4242 만 여는 게 정답입니다.

### G2. 왜 `default allow outgoing` 은 허용하나?

- 서버에서 **외부로 나가는 트래픽**은 패키지 업데이트(`apt update`), DNS 조회, NTP 시간 동기화 등 운영에 필수.
- 송신까지 막으면 시스템 운영 자체가 불가능 → 일반적인 서버 보안 베이스라인은 **"송신 허용, 수신 차단 + 화이트리스트"**.

### G3. 왜 4242 만 여는가? (보너스에서 80, 19999 추가)

- 평가에서 호스트는 SSH(4242) 만 접속하면 됨 → 그 외 포트가 열려 있으면 **불필요한 공격 표면**.
- 보너스에서 lighttpd(WordPress) 깔면 80 추가, Netdata 깔면 19999 추가 → **서비스 단위로 최소 노출**.

```bash
ufw status verbose   # 평가 시 자주 보는 명령
```

---

## H. sudo 와 그룹

### H1. 왜 sudo 가 필요한가? (root 직접 사용의 위험)

- root 로 직접 로그인/작업 = **모든 실수가 즉시 시스템 전체에 영향** (예: `rm -rf /` 가 그대로 실행).
- sudo 는 **(1) 권한 위임** + **(2) 명령별 인증** + **(3) 감사 로그** 를 제공 → 사고 추적 가능.

### H2. `Defaults` 옵션 7 종 — 왜 각각 필요한가?

| 옵션 | 무엇을 막는가? |
|---|---|
| `secure_path="…"` | 사용자 PATH 에 끼워 넣은 **악성 바이너리**(예: `/tmp/ls`) 가 sudo 로 실행되는 것 |
| `requiretty` | 백그라운드 프로세스(웹 서버 등)가 **사용자 모르게 sudo** 호출 — TTY 가 있어야만 허용 [[14]](https://www.sudo.ws/docs/man/sudoers.man/) |
| `badpass_message="WRONG PASSWORD"` | 평가에서 가시성 확인용 메시지 (서브젝트 요구) |
| `logfile="/var/log/sudo/sudo.log"` | sudo 호출 자체를 기록 — **누가 언제 무슨 명령** |
| `log_input` / `log_output` | 입력/출력 전체를 기록 — 사고 발생 시 정확히 무엇이 실행됐는지 재현 |
| `iolog_dir=/var/log/sudo` | 입출력 로그 보관소 (위 두 옵션 활성화 시 필요) |
| `passwd_tries=3` | 비밀번호 무한 시도 차단 (3 회 실패 시 종료) |

**한 줄 요약**: "권한 상승 경로의 모든 입출력을 기록하고, 잘못된 시도는 빨리 끊는다."

### H3. 왜 `user42` 와 `sudo` 두 그룹에 추가하는가?

- **`sudo` 그룹**: 멤버가 `sudo` 명령을 사용할 수 있게 해주는 표준 그룹 (Debian 기본 정책: `%sudo ALL=(ALL:ALL) ALL` in `/etc/sudoers`).
- **`user42` 그룹**: **서브젝트 요구**. 평가에서 `getent group user42` 로 멤버십 확인 → 본인이 정확히 시키는 대로 그룹 작업을 했는지 검증.
- 둘 다 필요한 이유: sudo 는 권한, user42 는 평가 항목.

```bash
getent group sudo user42   # 평가 단골 확인 명령
```

---

## I. 비밀번호 정책

### I1. 왜 PASS_MAX_DAYS=30 / MIN_DAYS=2 / WARN_AGE=7 인가?

| 항목 | 값 | 이유 |
|---|---|---|
| `PASS_MAX_DAYS=30` | 30 일마다 만료 | 비밀번호가 유출되어도 **30 일 안에 자동 무효화** |
| `PASS_MIN_DAYS=2` | 변경 후 최소 2 일 | 사용자가 새 비번 → 즉시 옛 비번 → 정책 회피 시도 차단 |
| `PASS_WARN_AGE=7` | 만료 7 일 전 경고 | UX (당일에 갑자기 잠기는 사고 방지) |

- 이 3 종은 **NIST SP 800-63B** 기반 일반 권장값. 서브젝트도 동일 수치를 강제.
- `chage` 는 **기존 사용자**에게도 같은 정책을 소급 적용하는 도구 (login.defs 는 신규 사용자만 영향).

### I2. pwquality 옵션 9 종 — 왜 이 조합인가?

| 옵션 | 의미 | 차단 대상 |
|---|---|---|
| `retry=3` | 3 회 실패 시 종료 | 무한 시도 |
| `minlen=10` | 최소 10 자 | 6~8 자 짧은 비번 |
| `difok=7` | 이전 비번과 7 자 이상 달라야 | "Pass1" → "Pass2" 같은 미세 변경 |
| `maxrepeat=3` | 같은 문자 3 회 초과 금지 | "aaaa1234" |
| `dcredit=-1` | 숫자 최소 1 자 (음수 = 강제) | "Password" |
| `ucredit=-1` | 대문자 최소 1 자 | "password1" |
| `lcredit=-1` | 소문자 최소 1 자 | "PASSWORD1" |
| `reject_username` | 사용자명 포함 금지 | "wil42pass" |
| `enforce_for_root` | root 도 동일 정책 | root 만 약한 비번 → 무의미 |

- `dcredit/ucredit/lcredit` 의 **음수 값**은 "그만큼 반드시 포함" 의미 (양수 = 보너스 점수, 헷갈리는 부분) [[15]](https://www.man7.org/linux/man-pages/man5/pwquality.conf.5.html).

---

## J. 모니터링 스크립트와 cron

### J1. 12 개 항목은 왜 이 조합인가?

서버 운영자가 매 순간 알고 싶은 4 가지 카테고리의 최소 항목입니다.

| 카테고리 | 항목 | 본질 |
|---|---|---|
| **시스템 정체** | Architecture, Physical CPU, vCPU, LVM use | 이 서버의 정체성 |
| **자원 사용량** | Memory, Disk, CPU load | 한계가 가까운가? |
| **시간** | Last boot | 마지막 재부팅 (장애 후 복구 시간 추적) |
| **활동 추적** | TCP, User log, Network, Sudo | 누가 무엇을 하고 있나 |

- 12 개는 **너무 적지도 많지도 않은 경량 헬스 체크**의 산업 표준에 가깝습니다.

### J2. 왜 cron 10 분 주기인가?

- 1 분: wall 메시지가 모든 사용자 터미널을 매분 도배 → 작업 방해.
- 1 시간: 변화 감지가 너무 늦음 (DB 가 30 분간 디스크 100% 차도 모름).
- **10 분**: 로그가 1 시간에 6 줄 → 가독성 + 즉시성의 균형.

### J3. wall 의 역할과 Debian 13 결함

- `wall` = 로그인한 **모든 터미널**에 메시지 브로드캐스트 → 관리자가 어디서 무엇을 보든 즉시 인지.
- **Debian 13 의 wall 결함**: util-linux 2.40+ 에서 `setgid tty` 권한 박탈 → SSH 세션(pts) 에 메시지가 안 감. → v5.2 메인 가이드 Part 0 §2 가 Debian 12 권장 근거.

### J4. 평가 단골: "수정 없이 cron 을 일시 중지하려면?"

- 정답: **`crontab -e` 로 들어가 해당 줄 앞에 `#` 주석** (또는 `crontab -r` 로 전체 제거 — 단 복원 어려움).
- 또는 `systemctl stop cron` (서비스 자체 중지) → 다른 cron job 까지 멈추므로 비추천.

---

## K. WordPress 스택 (LEMP)

### K1. 왜 lighttpd 인가? (NGINX/Apache 금지 이유)

- 서브젝트가 **NGINX, Apache 명시적 금지** → 학생이 흔히 알고 있는 두 거인을 막아 다른 도구를 학습하게 의도.
- lighttpd = **경량(메모리 30 MB 미만)**, 단일 바이너리, 설정이 단순 → 학습 곡선 낮음 [[16]](https://redmine.lighttpd.net/).
- 정식 영문 표기 LEMP 의 E 는 NGINX 의 발음 "엔진엑스" → 본 셋업은 엄밀히는 "LLMP" (lighttpd, Linux, MariaDB, PHP).

### K2. 왜 MariaDB 인가? (MySQL 과의 관계)

- MariaDB = MySQL 원 개발자(Monty Widenius)가 Oracle 인수 후 만든 **포크(fork)**.
- Debian 12 부터 `default-mysql-server` 가 MariaDB 를 가리킴 → **사실상 Debian 표준**.
- WordPress 는 두 DB 모두 호환.

### K3. 왜 FastCGI 가 필요한가?

- lighttpd 자체는 PHP 를 못 실행 → **PHP 인터프리터에 위임**해야 함.
- **CGI**: 요청마다 PHP 프로세스 fork → 매 요청 비용 큼.
- **FastCGI**: PHP 워커 프로세스를 **상주**시켜 두고 소켓으로 요청 전달 → 압도적으로 빠름 [[17]](https://fastcgi-archives.github.io/).
- `lighttpd-enable-mod fastcgi fastcgi-php` = 이 두 모듈을 활성화하는 Debian 도우미.

### K4. 왜 `chown -R www-data:www-data /var/www/html/` 인가?

- lighttpd 데몬은 **`www-data`** 사용자 권한으로 실행 (Debian 표준).
- WordPress 가 업로드/캐시 파일을 `/var/www/html/` 에 쓰는데, 디렉토리 소유자가 root 면 권한 거부 → 파일 업로드·플러그인 설치 실패.
- 소유권을 `www-data` 로 맞추면 정상 동작.

```bash
ls -la /var/www/html/  # www-data 소유 확인
```

---

## L. Netdata (추가 서비스)

### L1. 왜 추가 서비스가 필요한가?

- 서브젝트 보너스: "본인이 선택한 추가 서비스 1 개 운영 + **선택 이유 설명**" → 평가에서 "왜 이걸 골랐는가?" 가 반드시 질문됨.
- 단순 설치가 아니라 **선택의 근거**가 채점 대상.

### L2. 왜 Netdata 인가? (Prometheus/Grafana 와 비교)

| 항목 | Netdata | Prometheus + Grafana |
|---|---|---|
| 설치 | 1 패키지 | 2 패키지 + Exporter 별도 |
| 즉시 시각화 | **기본 내장** | Grafana 별도 설정 |
| 자원 사용 | 가벼움 (~ 200 MB RAM) | 중간 |
| 학습 곡선 | 매우 낮음 | 중상 |

- Born2beRoot 의 작은 VM 에서 **단일 패키지 + 즉시 동작**이라는 강점이 결정적.

### L3. 왜 19999 / 0.0.0.0 바인딩 변경?

- `19999` = Netdata 기본 포트 (수정 불필요).
- 기본 바인딩은 `127.0.0.1` (localhost) 만 listen → VirtualBox NAT 통한 **외부 접근 불가**.
- `/etc/netdata/netdata.conf` 의 `bind to = 0.0.0.0` 으로 변경 → **모든 네트워크 인터페이스에서 listen** → 호스트의 포트 포워딩으로 접근 가능.

> **보안 주의**: 0.0.0.0 바인딩은 **운영 환경에서는 위험**(인증 없이 노출). Born2beRoot VM 은 NAT + UFW 뒤에 있어 호스트만 접근 가능하므로 학습용으로 OK.

---

## M. 서명과 스냅샷

### M1. 왜 sha1sum 인가? — 무결성(integrity) 의 본질

- 평가자는 본인이 제출한 `.vdi` 와 **동일한 파일**을 평가하는지 확인해야 함 (사이에 수정 금지).
- **해시 함수**의 성질: 입력 1 비트만 바뀌어도 출력 해시가 완전히 달라짐 → "이 해시 = 이 파일" 의 1:1 매칭 [[18]](https://en.wikipedia.org/wiki/SHA-1).
- 제출 시 `signature.txt` 에 해시 적어두고 → 평가 시 다시 계산 → 일치하면 무결성 증명.
- **참고**: SHA-1 은 충돌 공격이 알려져 있어 최신 시스템은 SHA-256 권장이지만, 서브젝트가 명시적으로 sha1sum 사용을 지정.

### M2. 왜 .vdi 가 켜기만 해도 변경되는가?

- VM 부팅 = 디스크에 **로그·메타데이터·시간 기록 등** 자동 write 발생 → `.vdi` 내용 변경 → **해시 깨짐**.
- 따라서 "제출 → 평가 사이에는 절대 부팅 금지" 가 원칙.

### M3. 왜 v5.2 는 "각 평가는 스냅샷 없는 상태에서 시작" 인가?

- 스냅샷이 남아 있으면 평가자가 "이 학생이 평가 도중 실수해도 스냅샷으로 되돌리는 꼼수를 쓰는 것 아닌가?" 의심 가능.
- v5.2 정책: **평가 시작 시점에 만들고 → 평가 종료 후 즉시 삭제**. 평가 중 실수가 발생하면 그 스냅샷으로 복원하되, 다음 평가 전에는 **클린 상태**.
- 또한 **VM 자체를 Git 에 올리는 것 절대 금지** — 깃은 코드용, .vdi 는 GB 단위라 깃 저장소를 망가뜨림.

```bash
sha1sum debian.vdi > signature.txt   # 제출 직전 1 회만
```

---

## 자가 점검 (학부 수준 통과 기준)

평가자가 무작위로 골라 물었을 때 **각 항목당 30 초 이내**로 답할 수 있어야 합니다.

**Part 1 — VM 생성 ~ OS 설치 (§A~§E)**
1. ☐ Skip Unattended 를 켜는 이유 한 줄
2. ☐ hostname 규칙 (`<login>42`) + 그 근거(서브젝트)
3. ☐ root / 사용자 / LUKS 비밀번호 셋의 보호 대상 차이
4. ☐ "암호화된 LVM" 의 4 층 스택 (디스크 → LUKS → LVM → ext4)
5. ☐ LUKS 가 정확히 어느 계층에서 동작하는가 (파티션 위, LVM 아래)
6. ☐ PV / VG / LV 의 정의
7. ☐ 추가 LV 를 만들려면 왜 root LV 를 줄여야 하는가
8. ☐ `/home`, `/var`, `/tmp` 분리의 보안·운영상 이점
9. ☐ `/usr` 를 분리하지 않는 이유 (UsrMerge)
10. ☐ Journaling FS 의 한 줄 정의
11. ☐ ext4 를 선택한 이유 (안정성 + LV 축소 호환)
12. ☐ `/boot` 가 암호화 바깥인 이유 (GRUB 이 커널을 읽어야 함)
13. ☐ popularity-contest 거부 이유 (공격 표면 축소)
14. ☐ standard system utilities + SSH 만 체크하는 이유 (GUI 금지)

**Part 2 — 보안·서비스 설정 (§F~§M)**
15. ☐ 4242 포트로 옮기는 이유 (봇 공격 표면 축소 + 서브젝트)
16. ☐ `PermitRootLogin no` 가 인증 단계를 어떻게 강화하는가 (1 → 3 단계)
17. ☐ NAT 모드와 포트 포워딩이 왜 필요한가
18. ☐ UFW `default deny incoming` (화이트리스트) vs allow (블랙리스트) 차이
19. ☐ sudo 의 `secure_path`, `requiretty`, `log_input/output` 각각 막는 위협
20. ☐ `user42` 그룹과 `sudo` 그룹의 역할 차이
21. ☐ PASS_MAX_DAYS=30 / MIN_DAYS=2 / WARN_AGE=7 각 의미
22. ☐ pwquality 의 `dcredit=-1` 음수 표기 의미 (강제 vs 보너스)
23. ☐ 모니터링 12 항목 4 카테고리 (정체/자원/시간/활동)
24. ☐ cron 10 분 주기 선택 이유 (1분/1시간 양극단 비교)
25. ☐ "수정 없이 cron 일시 중지" 방법 (`crontab -e` 주석)
26. ☐ NGINX/Apache 금지 → lighttpd 선택 이유
27. ☐ FastCGI 가 CGI 보다 빠른 이유 (워커 상주)
28. ☐ `chown www-data` 가 필요한 이유 (lighttpd 실행 사용자)
29. ☐ Netdata vs Prometheus+Grafana 비교 후 Netdata 선택 이유
30. ☐ Netdata 의 0.0.0.0 바인딩 의미와 보안 함의
31. ☐ sha1sum 으로 무결성을 증명하는 원리 (해시의 1:1 매칭)
32. ☐ 왜 .vdi 가 부팅만 해도 변경되는가 → 스냅샷 정책의 근거

전부 ✓ 되면 평가 통과 가능합니다.

---

## TODO(human) — 본인 환경에 맞춘 파티션 표 작성

**컨텍스트**: 위 D 섹션은 일반론입니다. 실제 본인 VM(예: 20 GB `.vdi`) 에서는 **각 LV 에 정확히 몇 MB 를 줄지** 본인이 결정해야 평가 때 즉답할 수 있습니다.

**Your Task**: 아래 표의 빈칸을 본인 환경에 맞춰 채워 보세요.
근거(왜 이 크기인지)를 1~2 줄씩 적으면 평가 방어가 쉬워집니다.

| 마운트 | 권장 크기 | **본인이 정한 크기** | **왜 이 크기?** |
|---|---|---|---|
| `/boot` (sda1) | 500 MB | TODO | TODO |
| `swap` | RAM 의 1~2 배 | TODO | TODO |
| `/` | 5 GB | TODO | TODO |
| `/home` | 가장 큼 | TODO | TODO |
| `/var` | 2 GB | TODO | TODO |
| `/var/log` | 1 GB | TODO | TODO |
| `/srv` | 1 GB | TODO | TODO |
| `/tmp` | 500 MB | TODO | TODO |

**가이드**: 합이 디스크 크기를 넘으면 안 됩니다. WordPress 업로드 위주면 `/var` 키우고, 사용자 데이터 위주면 `/home` 키우세요. `/tmp` 는 너무 키우면 디스크 낭비, 너무 작으면 빌드/패키징 실패. swap 은 4 GB RAM 에 4~8 GB 면 보너스의 메모리 부족 상황까지 커버됩니다.

---

## 출처

[[1]](https://www.virtualbox.org/manual/ch01.html#create-vm-wizard-1-2-unattended) VirtualBox Manual — Unattended Installation
[[2]](https://wiki.debian.org/HowTo/ChangeHostname) Debian Wiki — Change Hostname (FQDN, domain)
[[3]](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/FrequentlyAskedQuestions) cryptsetup FAQ — LUKS layer placement
[[4]](https://wiki.debian.org/LVM) Debian Wiki — LVM (resize, extend)
[[5]](https://wiki.archlinux.org/title/Fstab#Filesystem-independent_mount_options) Arch Wiki — fstab mount options (noexec, nosuid, nodev)
[[6]](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch03s17.html) FHS 3.0 — `/srv`
[[7]](https://en.wikipedia.org/wiki/Journaling_file_system) Wikipedia — Journaling file system
[[8]](https://popcon.debian.org/) Debian — Popularity Contest 통계 페이지
[[9]](https://www.gnu.org/software/grub/manual/grub/grub.html) GNU GRUB Manual
[[10]](https://www.debian.org/releases/stable/amd64/ch06s03.en.html#pkgsel) Debian Installation Guide — Software selection (`pkgsel`)
[[11]](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) IANA — Service Names and Port Numbers
[[12]](https://man.openbsd.org/sshd_config#PermitRootLogin) OpenBSD `sshd_config(5)` — PermitRootLogin
[[13]](https://www.virtualbox.org/manual/ch06.html#network_nat) VirtualBox Manual — NAT and Port Forwarding
[[14]](https://www.sudo.ws/docs/man/sudoers.man/) sudoers manual — Defaults options (`requiretty`, `secure_path`)
[[15]](https://www.man7.org/linux/man-pages/man5/pwquality.conf.5.html) `pwquality.conf(5)` — credit options (음수 = 강제)
[[16]](https://redmine.lighttpd.net/) lighttpd 공식 (lightweight web server)
[[17]](https://fastcgi-archives.github.io/) FastCGI Specification (FastCGI vs CGI)
[[18]](https://en.wikipedia.org/wiki/SHA-1) Wikipedia — SHA-1 (해시 함수의 무결성)

선수 자료 (계층 구조 자세히): [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md)
사용자 본인 답변(원본): `_reference/answer.md`
시간 순 절차서 (HOW): [`README_KR.md`](./_reference/README_KR.md)
