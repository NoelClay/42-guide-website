---
description: "전원 ON → Debian 로그인 프롬프트까지: BIOS/UEFI · MBR · GRUB · 커널 · initramfs · systemd."
---

# 💡 Explanation — 부팅 흐름

> **문서 유형**: Explanation. 전원에서 login 까지의 5 단계 체인.

## 5 단계 체인 개관

```
[1] 펌웨어(BIOS/UEFI) → [2] MBR 부트코드 → [3] GRUB → [4] 커널 + initramfs → [5] systemd
```

각 단계는 **다음 단계의 코드를 찾아 메모리에 올리고 제어권을 넘긴다** 는 패턴의 반복.

---

## <a id="bios-uefi"></a>BIOS vs UEFI

**BIOS** (Basic Input/Output System, 1981 IBM PC 표준):
- 16-bit 실모드 코드
- 첫 512 바이트(MBR) 에서 부트코드 로드
- 2 TB 디스크 한계, primary 파티션 최대 4

**UEFI** (Unified Extensible Firmware Interface, 현대 표준):
- 32/64-bit 프로텍티드 모드
- FAT 파일시스템(EFI System Partition) 직접 읽어 `.efi` 로드
- GPT 파티션 테이블로 2 TB 초과 지원, Secure Boot

Born2beRoot 는 **BIOS 모드 강제** — Subject 의 `sda1/sda2/sda5` 파티션 예시가 MBR 전제이기 때문.

### 30 초 답변
"BIOS 는 80 년대 펌웨어 표준으로, 디스크 첫 섹터(MBR)에서 부트 코드를 읽어 실행합니다. UEFI 는 현대 대체로, EFI 시스템 파티션에서 직접 `.efi` 파일을 실행합니다. 우리는 BIOS 를 쓰는데, Subject 의 파티션 예시 (`sda5` 안의 LUKS) 가 MBR 기반이기 때문입니다."

---

## MBR (Master Boot Record)

디스크의 첫 섹터 (LBA 0), **정확히 512 바이트**.

구성:
- **446 바이트** — 부트 코드 (GRUB stage 1 이 여기 들어감)
- **64 바이트** — 파티션 테이블 (4 엔트리 × 16 바이트)
- **2 바이트** — 시그니처 `0x55AA`

한 엔트리: `부팅플래그(1) + CHS시작(3) + 타입(1) + CHS끝(3) + LBA시작(4) + 섹터수(4)`.

- LBA 4 바이트 → 32-bit → **2 TB 한계**
- Primary 최대 4 개 → Extended 타입(0x05) 으로 Logical 확장 (Born2beRoot 의 sda2 가 extended, sda5 가 logical)

### 더 깊이
- `"MBR partition table structure"`
- `"MBR 2TB limit explained"`
- `"EBR Extended Boot Record chain"`

---

## GRUB (GRand Unified Bootloader)

MBR 446 바이트로는 복잡한 부팅 로직이 들어갈 수 없어, GRUB 은 **다단계** 구조:

| 단계 | 위치 | 역할 |
|---|---|---|
| Stage 1 (`boot.img`, 440 B) | MBR 부트 코드 영역 | Stage 1.5 위치만 앎 |
| Stage 1.5 (`core.img`) | MBR 뒤 gap 또는 `/boot` | 파일시스템·LUKS·LVM 모듈 내장, `/boot` 마운트 가능 |
| Stage 2 | `/boot/grub/` | 설정 파일·메뉴·커널 로드 |

Born2beRoot 는 `/boot` 를 **평문 파티션(sda1)** 에 둠 → GRUB 이 복잡한 LUKS 해독 없이 커널을 찾을 수 있게 단순화.

### 30 초 답변
"GRUB 은 MBR 의 작은 공간 제약 때문에 여러 단계로 나뉩니다. Stage 1 은 MBR 에 들어가고, Stage 2 는 `/boot` 파티션의 파일을 읽어 커널과 initramfs 를 메모리에 올립니다. 우리는 `/boot` 를 암호화하지 않은 파티션에 둬서 이 단계를 단순화합니다."

---

## 커널과 initramfs

**커널** (`/boot/vmlinuz-*`): Linux 의 실제 몸통. bzImage 포맷.

**initramfs** (`/boot/initrd.img-*`): gzip 압축 cpio 아카이브. RAM 에 임시 파일시스템으로 풀려 마운트됨. 커널이 실행되자마자 이 initramfs 의 `/init` 을 실행.

여기서 **실제 root 파일시스템을 마운트하기 전 필수 작업** 수행:
- LUKS 해독 (`cryptsetup luksOpen sda5 sda5_crypt`)
- LVM 인식 (`lvm vgchange -ay`)
- 디스크 드라이버 로드

준비 끝나면 `switch_root` 로 실제 root 로 전환.

Born2beRoot 의 LUKS + LVM 스택이 **initramfs 없이는 부팅 불가** — 암호 화면이 뜨는 이유.

---

## systemd

부팅 완료 후 **PID 1** 로 상승. 유닛 파일(`.service`, `.target`, `.timer`, `.mount`) 로 시스템 상태를 기술하고 의존성 기반 병렬 부팅.

Born2beRoot 에서 만지는 systemd 명령:

```bash
systemctl enable apparmor          # 부팅 시 자동 시작
systemctl is-active ssh            # 현재 상태
systemctl restart lighttpd         # 재시작
journalctl -u cron                 # 서비스 로그
```

### 30 초 답변
"systemd 는 현대 Linux 의 init 시스템으로 PID 1 입니다. 유닛 파일로 서비스·타겟·타이머를 선언해 의존성 기반 병렬 부팅을 합니다. 과거 SysV init 의 직렬 실행보다 빠르고, `journalctl` 로 구조화된 로그를 제공합니다."

### 더 깊이
- `"systemd unit types service target"`
- `"systemd vs SysV init parallel boot"`
- `"journalctl structured logging"`

---

## 관련 문서

- [튜토리얼 Part D (GRUB)](../00-tutorial/install-your-first-vm.md#d3-grub-부트로더-설치)
- [GRUB 복구](../01-how-to/recover-from-lockout.md#grub-recovery)
- [저장 스택 (LUKS 가 initramfs 와 연결)](storage-stack.md)
