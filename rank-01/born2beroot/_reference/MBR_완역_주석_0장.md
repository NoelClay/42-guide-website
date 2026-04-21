# 제0장. 부팅 — 학부 표준 정리

> **목적**: Born2beRoot 통과 + 학부 OS 강의 통과에 필요한 **최소 필수 부팅 지식**. 표준 교재 (Silberschatz, *Operating System Concepts* §2.9.2 "System Boot") 기준 **4 단계 + 보충 단락** 으로 정리. 분량은 의도적으로 학부 강의 1~2 회 수준 (~5 페이지). 그 이상 깊이 (현장 디버깅·커널 개발자 수준) 는 [`MBR_완역_주석_0장_오바싸는버전.md`](MBR_완역_주석_0장_오바싸는버전.md) 참조.

---

## §1. 선수 표기법 — 10 분 안에 끝내기

### 1-1. bit, byte

- **bit** = 0 또는 1 (전기 신호 ON/OFF)
- **byte** = 8 bits = 256 가지 표현 가능
- 1 byte 표현 범위 = 0 ~ 255

### 1-2. 16진수 (hexadecimal)

2진수 8자리 (`11111111`) 가 너무 길어서 만든 짧은 표기. **2진수 4자리 = 16진수 1자리**.

| 10진수 | 16진수 | 2진수 |
|---|---|---|
| 0~9 | 0~9 | 0000~1001 |
| 10~15 | A~F | 1010~1111 |

`0x` 접두어 = "이건 16진수다" 표시. 예: `0xAA = 10×16 + 10 = 170`.

**1 byte = 16진수 2자리** (00 ~ FF).

⚠️ 헷갈리지 말 것:
- **16-bit = 2 bytes** (8 × 2)
- 16 bytes = 128 bits — 다른 크기
- `0x5자리 = 20-bit`, `0x8자리 = 32-bit`

### 1-3. little-endian

x86 CPU 는 여러 byte 짜리 숫자를 메모리에 **작은 자릿수부터** 저장.

- 숫자값: `0xAA55`
- 메모리 표시: `55 AA` (뒤집힘)

MBR 서명이 디스크에 `55 AA` 로 보이지만 숫자값으로는 `0xAA55` 인 이유.

---

## §2. 디스크 구조 — sector 와 LBA

### 2-1. sector

디스크의 최소 읽기 단위. **논리 섹터 = 512 bytes** (현대 디스크가 OS 에 보고하는 표준 크기).

물리 섹터는 4 KB 일 수도 있지만 디스크 컨트롤러가 OS 에는 512 bytes 로 환산해서 보고. OS 입장에서는 항상 512 bytes 섹터로 계산하면 됨.

### 2-2. LBA (Logical Block Addressing)

섹터에 단순 번호 0, 1, 2, ... 매기는 방식.

- LBA 0 = 디스크 첫 섹터 (= MBR 자리)
- LBA N × 512 bytes = 디스크에서 그 byte 위치
- LBA 2048 × 512 = **1 MB** 지점 (현대 첫 파티션 시작 위치)

### 2-3. MBR 의 2 TB 한계 — 수학적 유도

MBR 파티션 엔트리의 LBA 필드 = **32-bit** (1983년 설계).

$$
\text{최대 디스크} = 2^{32} \times 512 \text{ bytes} = 2^{41} \text{ bytes} = 2 \text{ TiB}
$$

2 TB 이상 디스크는 **GPT** (64-bit LBA) 를 사용. Born2beRoot 는 ~21 GB 이므로 MBR 로 충분.

⚠️ 주의: **2 TB 한계는 디스크 주소 이야기**. 메모리 주소 (segment:offset, 1 MB) 와 무관.

---

## §3. MBR 의 정체 — 디스크의 첫 512 bytes

```
offset 0x000 ┌──────────────────────────┐
             │  Bootstrap code (446 B)  │ ← 부팅 시작 코드
offset 0x1BE ├──────────────────────────┤
             │  Partition table (64 B)  │ ← 파티션 4개 정보
             │   = 16 B × 4 entries     │
offset 0x1FE ├──────────────────────────┤
             │  Boot signature (2 B)    │ ← 0x55 0xAA
offset 0x1FF └──────────────────────────┘
              총 512 bytes
```

### MBR 파티션 번호 규칙

MBR 은 최대 **4 개 primary 파티션**. 더 많이 쓰려면 1 개를 **extended (컨테이너)** 로 지정:

- `sda1` = primary #1
- `sda2` = primary #2 (extended 컨테이너)
- `sda5` = logical #1 (extended 안에서, **항상 5번부터 시작**)
- `sda3, sda4` 는 안 쓰면 비어 있음

Born2beRoot 의 `sda1` (`/boot`) + `sda2` (extended 껍데기) + `sda5` (LUKS 컨테이너) 가 정확히 이 패턴.

---

## §4. 메모리 주소 기초 — segment:offset

### 4-1. 왜 두 조각인가

Intel 8086 (1978) 의 한계:
- 레지스터 = **16-bit** → 64 KB 까지만 표현 가능
- 주소 버스 = **20-bit** → 1 MB 까지 메모리 접근 가능

해결책: **두 16-bit 값을 조합**:

$$
\text{물리 주소} = \text{segment} \times 16 + \text{offset}
$$

(× 16 = 4-bit 왼쪽 shift. 16 + 16 - 4 = 20-bit 주소 생성.)

### 4-2. 표기법

`segment:offset` — **콜론 앞이 segment, 뒤가 offset**.

| 표기 | segment × 16 + offset | 물리 주소 |
|---|---|---|
| `0x0000:0x7C00` | 0 + 0x7C00 | **0x07C00** |
| `0x07C0:0x0000` | 0x7C00 + 0 | **0x07C00** (같은 위치) |

여러 (segment, offset) 조합이 같은 물리 주소를 표현 가능. 표준 관행은 `segment=0` 으로 두고 offset 에 전체 주소.

### 4-3. 0x7C00 의 정체

BIOS 가 MBR (sector 0) 을 읽어 메모리 `0x07C00 ~ 0x07DFF` (512 bytes) 에 로드 후 그곳에서 실행 시작.

수학적 유도: IBM PC 5150 + 디스크 옵션의 최소 RAM = 32 KB = `0x8000`. 그 끝부분 1 KB 를 MBR + 스택용으로 예약 → MBR 시작 = `0x8000 - 0x400 = 0x7C00`.

또는 "32 번째 KB 의 첫 byte" = `(32-1) × 0x400 = 0x7C00`.

이 값은 1981년 결정 후 40 년 넘게 안 바뀜 (하위 호환성).

---

## §5. 부팅의 4 단계 (Silberschatz 표준)

> *"On most systems, the boot process proceeds as follows:*
> *1. A small piece of code known as the **bootstrap program** or **boot loader** locates the kernel.*
> *2. The kernel is loaded into memory and started.*
> *3. The kernel initializes hardware.*
> *4. The root file system is mounted."*
> — Silberschatz, *Operating System Concepts*, §2.9.2

| 단계 | 임무 |
|---|---|
| **1** | bootstrap program (= boot loader) 가 커널의 위치를 찾음 |
| **2** | 커널을 메모리에 로드하고 시작 |
| **3** | 커널이 하드웨어 (CPU, 메모리, 장치) 를 초기화 |
| **4** | root file system 마운트 |

**부팅 = 커널을 메모리에 로드하여 시작하는 과정.** 한 문장으로 끝.

---

## §6. 다단계 부팅 (multistage boot)

전원 ON 부터 OS 까지는 **여러 단계의 연쇄 (chain loading)** 가 필요. 한 단계가 모든 일을 못 함.

```
[BIOS]                       ← 펌웨어. 디스크 첫 섹터 (512 B) 만 읽음.
   ↓
[MBR boot code (446 B)]      ← 너무 작아 OS 못 로드. 다음 단계로 점프.
   ↓
[GRUB]                        ← 본격 부트로더. 파일시스템 읽기, 메뉴 표시.
   ↓
[Linux kernel]                ← 하드웨어 초기화, initramfs 마운트.
   ↓
[initramfs]                   ← 임시 환경. cryptsetup 으로 LUKS 해독, LVM 활성화.
   ↓
[real root file system]       ← 진짜 / 마운트.
   ↓
[systemd]                     ← 첫 사용자 프로세스. 서비스 기동, 로그인.
```

**왜 이렇게 단계가 많은가** — 각 단계는 **이전 단계가 못하는 일** 을 수행. 호환성 보존 때문에 새 기능은 새 단계로 추가됨. 40 년 진화한 결과.

---

## §7. BIOS vs UEFI

| | BIOS | UEFI |
|---|---|---|
| 구조 | 다단계 (multistage) | 단일 boot manager |
| 64-bit 지원 | 약함 | 강함 |
| 큰 디스크 | 2 TB 한계 (MBR) | 9.4 ZB (GPT) |
| 속도 | 느림 (단계 많음) | 빠름 |
| 등장 시기 | 1981 | 2005 (Intel/Microsoft 표준화) |

> *"UEFI has several advantages over BIOS, including better support for 64-bit systems and larger disks. Perhaps the greatest advantage is that UEFI is a single, complete boot manager and therefore is faster than the multistage BIOS boot process."*
> — Silberschatz §2.9.2

**Born2beRoot = BIOS 부팅** (VirtualBox 기본).

---

## §8. GRUB — Linux 의 부트로더

> *"GRUB is an open-source bootstrap program for Linux and UNIX systems. Boot parameters for the system are set in a GRUB configuration file, which is loaded at startup. GRUB is flexible and allows changes to be made at boot time, including modifying kernel parameters and even selecting among different kernels that can be booted."*
> — Silberschatz §2.9.2

특징:
- **오픈소스** (GNU project)
- 시작 시 **구성 파일** `/boot/grub/grub.cfg` 로드
- 부팅 시점에 **커널 파라미터 변경** 또는 **여러 커널 중 선택** 가능

**커널 파라미터 예시** (`/proc/cmdline` 에서):
```
BOOT_IMAGE=/boot/vmlinuz-4.4.0-59-generic
root=UUID=5f2e2232-4e47-4fe8-ae94-45ea749a5c92
```

- `BOOT_IMAGE` = 로드될 커널 이미지 파일 경로
- `root` = root file system 의 UUID (Universally Unique IDentifier)

**참고**: GRUB 이 내부적으로 stage 1 / 1.5 / 2 로 나뉘는데, 이는 §6 의 "각 단계의 물리적 제약" 때문 (MBR 440 B → MBR-sda1 사이 빈 공간 → /boot/grub/ 안의 파일). 학부 수준에서는 "GRUB 이 부트로더" 로만 알아도 충분.

---

## §9. initramfs — 임시 RAM 파일시스템

> *"To save space as well as decrease boot time, the Linux kernel image is a compressed file that is extracted after it is loaded into memory. During the boot process, the boot loader typically creates a temporary RAM file system, known as **initramfs**. This file system contains necessary drivers and kernel modules that must be installed to support the **real** root file system... Once the kernel has started and the necessary drivers are installed, the kernel switches the root file system from the temporary RAM location to the appropriate root file system location. Finally, Linux creates the **systemd** process, the initial process in the system."*
> — Silberschatz §2.9.2

### 왜 필요한가

**커널 혼자서는 복잡한 root file system 을 마운트할 수 없다.** Born2beRoot 의 root 까지 가는 길:

```
sda → sda5 (LUKS 암호화) → sda5_crypt (해독됨) → LVM VG → root LV → ext4 → /
```

각 단계마다 **사용자 공간 프로그램** (`cryptsetup`, `lvm`, `mount`) 이 필요. 그런데 이 프로그램들이 들어있는 파일시스템이 바로 우리가 마운트하려는 대상. **닭-달걀 문제**.

### 해결 — initramfs 의 동작 순서

```
1. boot loader 가 메모리에 vmlinuz (커널) + initrd.img (initramfs) 로드
2. 커널 시작 → initramfs 를 임시 / 로 마운트
3. initramfs 안의 cryptsetup 이 사용자에게 LUKS 암호 요청 → 해독
4. lvm 이 VG 활성화 → LV 들이 /dev/mapper/ 에 노출됨
5. mount 가 진짜 / (root LV) 를 마운트
6. switch_root 가 임시 / 에서 진짜 / 로 전환
7. systemd 가 시작 (첫 사용자 프로세스)
```

**한 문장 요약**: initramfs = **"진짜 root 마운트에 필요한 도구를 담은 임시 환경"**.

---

## §10. Born2beRoot 의 부팅 — Silberschatz 4 단계 적용

| Silberschatz 단계 | Born2beRoot 실제 |
|---|---|
| **1. boot loader 가 커널 위치 찾기** | BIOS → MBR → GRUB. GRUB 가 sda1(`/boot`) 의 ext2 안에서 `vmlinuz-*` 식별 |
| **2. 커널 메모리 로드·시작** | GRUB 가 `vmlinuz-*` + `initrd.img-*` 메모리에 로드, 커널에 제어권 |
| **3. 커널 하드웨어 초기화** | 디바이스 드라이버, MMU, dm-crypt·dm-mod 모듈 준비 |
| **4. root file system 마운트** | initramfs → cryptsetup (LUKS 해독) → lvm (VG 활성화) → mount → switch_root → systemd 시작 |

Born2beRoot 가 학부 표준보다 **단계 4 가 길어 보이는 이유**: LUKS + LVM 으로 root 까지 4 계층이 쌓여 있음. 추상적으로는 정확히 Silberschatz 4 단계.

---

## §11. 학부 수준 자가점검

다음 5 항목 답할 수 있으면 **학부 OS 강의 통과 + Born2beRoot P2P 평가 통과 가능**.

- [ ] **booting 의 정의** 한 문장으로: "커널을 메모리에 로드하여 시작하는 과정"
- [ ] **부팅 4 단계** 외워서: boot loader 찾기 → 커널 로드 → 하드웨어 초기화 → root 마운트
- [ ] **BIOS vs UEFI** 핵심 차이 한 가지: BIOS = 다단계, UEFI = 단일 boot manager
- [ ] **GRUB** 이 무엇이고 왜 필요한지 한 문장: 커널을 찾고 로드하는 부트로더, 커널 파라미터 수정·선택 가능
- [ ] **initramfs** 가 왜 필요하고 어떻게 동작하는지 한 문단: 진짜 root 마운트 (특히 LUKS+LVM 같은 복잡한 경우) 에 필요한 도구를 담은 임시 RAM 파일시스템. 진짜 root 마운트 후 systemd 로 전환

---

## 부록 A — 자주 헷갈리는 포인트 정리

학생 자가답변 시 실제로 나온 오류들. 한 번 정리:

| 오해 | 정정 |
|---|---|
| `0x55 AA` 는 16 byte | **2 byte (= 16-bit)**. 16-bit ≠ 16 bytes |
| 2 TB 한계가 메모리 64 KB 와 관련 | **무관**. LBA 32-bit × 512 bytes = 2 TiB. 메모리는 별개 이야기 |
| `0x5자리 = 32-bit` | `0x5자리 = 20-bit`. 32-bit = `0x8자리` |
| `JMP 0x0000:0x7C00` 후 CS = 0x7C00 | **CS = 0x0000**. 콜론 앞이 CS, 뒤가 IP |
| CS 가 매 명령마다 변함 | CS 는 **far JMP/CALL/RET/INT** 에서만 변경. 일반 코드는 IP 만 자동 증가 |
| 0x84000 같은 주소가 쓰레기 | conventional memory (0x00500 ~ 0x9FFFF) 안. 정상 주소 |
| "고정 grub" 이라는 용어가 있다 | 그런 이름은 없음. 정확히는 **MBR-sda1 사이의 core.img** |
| "grub 파일시스템" 이라는 용어가 있다 | 없음. **/boot 파티션의 ext2 안의 GRUB 모듈 파일들** |
| "선택의 영역" 이 별도 단계 | 별도 단계 아님. **GRUB stage 2 (normal.mod) 의 한 기능** |
| 커널이 LUKS 를 직접 해독 | **initramfs 의 cryptsetup** 이 해독. 커널은 dm-crypt 모듈로 암호화 연산만 담당 |
| "마운트" 가 한 동작 | **lvm 활성화 → mount → switch_root** 의 3 단계 |
| `IP` 가 "현재 실행 명령" 위치 | 엄밀히는 "**다음에 fetch 할** 명령의 위치" (느슨하게는 "현재 흐름") |

---

## 부록 B — 더 깊이 가고 싶다면

학부 표준 (이 문서) 으로 충분한 사람은 여기서 멈추면 됨. 더 알고 싶다면:

| 자료 | 적정 수준 |
|---|---|
| [`MBR_완역_주석_0장_오바싸는버전.md`](MBR_완역_주석_0장_오바싸는버전.md) | 시스템 프로그래밍·커널 디버깅 진입 수준 (이 문서의 4~5 배 깊이) |
| Silberschatz, *Operating System Concepts*, §2.9 | 학부 OS 표준 교재 (이 문서의 출처) |
| Arch Wiki — Boot process | 가장 정확·갱신 빈번. 실용 참조 |
| Wikipedia — Master Boot Record | MBR 자체의 역사·구조 1 차 자료 |

---

**다음**: [제1장. MBR 이 뭐고 왜 있는가 — Wikipedia 원문 Introduction 완역·주석](MBR_완역_주석_1장.md)
