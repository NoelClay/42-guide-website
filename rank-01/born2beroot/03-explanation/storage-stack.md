---
description: "디스크 → 파티션 → LUKS → LVM → 파일시스템 4 층 스택."
---

# 💡 Explanation — 저장 스택

> **문서 유형**: Explanation. Born2beRoot 저장 구조의 계층 관계.

## 4 층 구조

```
물리 디스크 (sda, 20 GB)
    ├─ sda1 (ext2, 평문, /boot, 500 MB)
    └─ sda5 (LUKS 컨테이너, 19.5 GB)
         ↓ LUKS 해독
       sda5_crypt (암호화 해제된 가상 블록 장치)
         ↓ LVM PV
       <hostname>-vg (Volume Group)
         ↓ LVM LV
       ├─ root  ext4  /
       ├─ home  ext4  /home
       ├─ var   ext4  /var
       ├─ var-log ext4 /var/log
       ├─ srv   ext4  /srv
       ├─ tmp   ext4  /tmp
       └─ swap_1 swap area
```

각 층이 아래 층을 **블록 장치** 로 간주. 추상화 분리 덕분에 한 층만 교체/확장 가능.

---

## 파티션 (primary / extended / logical)

MBR 파티션 테이블은 4 엔트리 고정. 5 개 이상 필요하면:
- 엔트리 1 개를 **extended** 타입(0x05) 으로 → 컨테이너 역할
- 그 안에 **logical** 파티션들을 **EBR (Extended Boot Record) 체인** 으로 넣음
- Linux 명명: primary=1~4, logical=5~

Born2beRoot 구성:
- `sda1` primary, `/boot`
- `sda2` extended (컨테이너만, 크기 1K)
- `sda5` logical, LUKS 대상

### 30 초 답변
"MBR 은 최대 4 개의 primary 파티션만 지원하므로, 5 개 이상 필요할 때 하나를 extended 파티션으로 만들어 그 안에 logical 파티션을 추가합니다. 우리의 sda2 가 extended, sda5 가 그 안의 logical 입니다."

---

## 파일시스템과 저널링

파일시스템(FS) = 블록 → 파일/디렉토리 추상화.

- **ext4** = Linux 기본. 저널링, inode 기반, 16 TiB 파일, **축소 가능** (Born2beRoot 요구 사항).
- **xfs** = 고성능 대용량. **축소 불가** → Born2beRoot 탈락.
- **btrfs** = Copy-on-Write, 스냅샷, 체크섬 — 현대적이지만 복잡.

**저널링** = 메타데이터 변경을 저널 영역에 선기록 → 전원 중단 시 저널 재생으로 일관성 복구.

### 30 초 답변
"ext4 는 저널링 파일시스템으로, 메타데이터 변경을 저널에 먼저 기록해 전원 끊김 같은 충돌 후에도 일관성을 보장합니다. 또한 온라인 확장·축소가 가능해 LVM 과 조합할 때 유연합니다."

---

## LVM (PV / VG / LV)

**Logical Volume Manager**. 3 계층 추상화:

| 층 | 역할 | Born2beRoot |
|---|---|---|
| **PV** (Physical Volume) | LVM 의 벽돌 | `/dev/sda5_crypt` 하나 |
| **VG** (Volume Group) | 벽돌 더미 | `<hostname>-vg` |
| **LV** (Logical Volume) | 가상 파티션 | root, home, var, var-log, srv, tmp, swap_1 |

장점:
- `lvextend` / `lvreduce` — **실행 중** 크기 변경
- `lvcreate --snapshot` — Copy-on-Write 스냅샷
- 여러 PV 를 하나의 VG 로 묶어 단일 대용량 LV 생성 가능

**Debian installer 의 함정**: LVM 메뉴에 "리사이즈" 옵션이 없음 → 크기 바꾸려면 **삭제 후 재생성** 패턴.

### 30 초 답변
"LVM 은 PV → VG → LV 의 3 계층 추상화로, 여러 디스크를 묶어 유연한 가상 파티션을 만듭니다. 장점은 실행 중 크기 변경 (`lvextend`) 과 스냅샷입니다. 전통 파티션 테이블의 경직성 (`fdisk` 로 크기 변경 불가) 을 극복합니다."

### 더 깊이
- `"LVM Physical Extent size"`
- `"lvcreate lvextend lvreduce examples"`
- `"device-mapper architecture Linux"`

---

## <a id="luks"></a>LUKS 와 dm-crypt

**LUKS** (Linux Unified Key Setup) = 블록 장치 암호화 표준.

파티션 시작에 **LUKS 헤더** 가 있고, 그 뒤가 실제 암호화 데이터:
- **Magic** (`LUKS\xba\xbe`)
- **Version**, **cipher spec** (예: aes-xts-plain64)
- **8 개의 키슬롯** — 각 슬롯에 PBKDF2 로 유도한 마스터 키 암호문

**dm-crypt** = 커널의 device-mapper 모듈, 블록 단위 암/복호화.
**cryptsetup** = 유저 공간 도구.

계층: `디스크 → 파티션 → LUKS → 가상 블록장치(sda5_crypt) → LVM PV → VG → LV → ext4`.
즉 **LUKS 는 파티션 위, LVM 아래**.

### LUKS 랜덤 디스크 채우기의 이유
암호화 전에 전체 디스크를 랜덤 데이터로 채우면, "암호화 영역" 과 "빈 영역" 을 엔트로피 차이로 구분할 수 없음 → 데이터 패턴 노출 방지. 20~30 분이 걸리는 이유.

### 30 초 답변
"LUKS 는 파티션을 통째로 암호화하는 표준으로, 헤더에 8 개의 키슬롯을 두어 여러 패스프레이즈로 해독 가능합니다. 부팅 시 initramfs 단계에서 `cryptsetup` 이 패스프레이즈로 마스터 키를 복원해 `sda5_crypt` 가상 장치를 만들고, 그 위에 LVM·파일시스템이 올라갑니다."

### 더 깊이
- `"LUKS header format keyslot"`
- `"dm-crypt kernel module explained"`
- `"cryptsetup luksFormat"`

---

## 관련 문서

- [튜토리얼 Part C (파티셔닝)](../00-tutorial/install-your-first-vm.md#part-c-파티셔닝-핵심)
- [LV 확장](../01-how-to/extend-lv-capacity.md)
- [부팅 흐름 (initramfs 가 LUKS 해독)](boot-sequence.md)
- [Subject v5.2 SC-07 (최소 2 암호화 파티션)](../02-reference/subject-v5.2-clauses.md#sc-07-최소-2-개-암호화-파티션--lvm)
