# 📖 Born2beRoot v5.2 — WHY 이론 (2026-04-18)

**3 문서 체계 중 ③ WHY (이론가의 시선)** · 각 개념을 (중학생 비유 → 학부 정의 → 더 깊이 구글 검색어) 3 단 구조로

| 문서 | 역할 | 파일 |
|---|---|---|
| ① HOW | 키 입력 단위 단계별 가이드 | `Born2beRoot_v5_2_HOW_2026-04-18.md` |
| ② WHAT | Subject `must/have to` 조항 매핑 | `Born2beRoot_v5_2_WHAT_2026-04-18.md` |
| ③ **WHY** (본 문서) | 각 개념 3 단 이론 | `Born2beRoot_v5_2_WHY_2026-04-18.md` |

**본 문서 사용법**:
- **개념이 막힐 때**: HOW 의 단계 옆 `[📖 WHY-nn]` 클릭 → 해당 개념의 중학생 버전부터 읽기
- **심화 탐색**: 3 단 끝의 **구글 검색어** 를 바로 브라우저에 복붙 → 원문 자료로 이동
- **평가 서술 대비**: 각 개념의 학부 버전 3~5 문장을 본인 말로 한 번 다시 써보면 평가 서술에 즉시 활용 가능

**깊이 약속**: 컴퓨터공학 학부 전공필수(Silberschatz, Tanenbaum) 이내. 박사/논문급 디테일은 구글 검색어로 넘김.

---

## 목차

**하드웨어·가상화** — [WHY-01](#why-01) 하이퍼바이저 · [WHY-02](#why-02) vCPU·pCPU · [WHY-03](#why-03) 가상 디스크

**부팅 흐름** — [WHY-04](#why-04) BIOS/UEFI · [WHY-05](#why-05) MBR · [WHY-06](#why-06) GRUB · [WHY-07](#why-07) 커널·initramfs · [WHY-08](#why-08) systemd

**저장** — [WHY-09](#why-09) 파티션 · [WHY-10](#why-10) 파일시스템·저널링 · [WHY-11](#why-11) LVM · [WHY-12](#why-12) LUKS·dm-crypt

**네트워크** — [WHY-13](#why-13) NAT·포트 포워딩 · [WHY-14](#why-14) SSH · [WHY-15](#why-15) UFW·iptables

**권한·정책** — [WHY-16](#why-16) root·sudo · [WHY-17](#why-17) 그룹 · [WHY-18](#why-18) PAM·pwquality · [WHY-19](#why-19) AppArmor·SELinux

**모니터링** — [WHY-20](#why-20) cron·wall · [WHY-21](#why-21) monitoring 12 항목

**웹 스택 (보너스)** — [WHY-22](#why-22) lighttpd·FastCGI · [WHY-23](#why-23) MariaDB · [WHY-24](#why-24) PHP·www-data

**무결성** — [WHY-25](#why-25) 해시 함수 SHA-1 · [WHY-26](#why-26) 스냅샷 COW

---

# 하드웨어·가상화

## <a id="why-01"></a>WHY-01. 하이퍼바이저 (Type 1 vs Type 2)

**중학생 버전**: 컴퓨터 안에 다른 컴퓨터를 만드는 마술사입니다. "Type 1" 은 하드웨어 위에 바로 사는 **집주인**, "Type 2" 는 다른 OS 위에 얹혀 사는 **세입자**. VirtualBox 는 세입자 쪽입니다.

**학부 버전**: 하이퍼바이저(hypervisor)는 가상화 계층을 제공하는 소프트웨어입니다. **Type 1 (Bare-metal)** 은 하드웨어 위에서 직접 동작 (VMware ESXi, Microsoft Hyper-V, KVM), 오버헤드 최소 + 성능 최선 → 데이터센터용. **Type 2 (Hosted)** 는 호스트 OS 위에 추가 계층으로 동작 (VirtualBox, VMware Workstation), 설치·관리 편리하지만 오버헤드 존재 → 개발자 PC 용. 본 과제는 Type 2 VirtualBox.

**더 깊이 (구글 검색)**:
- `"Type 1 vs Type 2 hypervisor differences"`
- `"KVM vs VirtualBox performance comparison"`
- `"VT-x AMD-V hardware virtualization"`

**연결**: [HOW Part A](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-part-a) · [WHAT-01](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-01)

---

## <a id="why-02"></a>WHY-02. vCPU 와 pCPU (과할당이 오히려 느린 이유)

**중학생 버전**: VM 에게 "너는 CPU 3 개를 가진 척 해" 라고 속이는 거예요. 근데 실제 CPU 가 3 개 동시에 비어 있어야만 그 "3 개" 가 동시에 돌아갑니다. 욕심내서 많이 주면 오히려 VM 이 멍해져요.

**학부 버전**: vCPU = 게스트 OS 가 보는 논리 CPU, pCPU = 호스트의 물리 CPU 스레드. 하이퍼바이저가 vCPU → pCPU 스케줄링. SMP VM 은 여러 vCPU 가 **동시에 pCPU 를 얻어야** (co-scheduling) 동작 — 따라서 vCPU 개수가 가용 pCPU 보다 많으면 "모든 vCPU 동시 가용" 시점까지 대기 → 유휴 시간 급증. 결과적으로 과할당은 성능 저하. 본 과제는 호스트 물리 코어를 절대 초과하지 않음.

**더 깊이 (구글 검색)**:
- `"CPU co-scheduling virtual machine"`
- `"vCPU oversubscription penalty"`
- `"VMware Ready Time %RDY"`

**연결**: [HOW A3](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-a3) · 보조 자료 [`virtualbox_howtosetcpu.md`](./_reference/virtualbox_howtosetcpu.md)

---

## <a id="why-03"></a>WHY-03. 가상 디스크 (VDI, Pre-Allocate)

**중학생 버전**: 호스트의 파일 하나(.vdi)가 VM의 하드디스크 전체 역할을 해요. "Pre-Allocate" 는 처음부터 20 GB 자리를 딱 잡아두는 것. 나중에 I/O 가 빨라져요.

**학부 버전**: `.vdi` = VirtualBox Disk Image, 내부는 블록 단위 매핑 구조. **Dynamic allocation** 은 실제 쓰기 시에만 호스트 공간 확장 (처음 가볍지만 단편화·성능 저하). **Pre-Allocate (Fixed-size)** 는 생성 시 전체 크기 예약 → 블록이 호스트 디스크에 연속 배치 → I/O 일관성 + 단편화 최소. 본 과제는 20 GB Pre-Allocate 권장.

**더 깊이 (구글 검색)**:
- `"VDI vs VMDK format comparison"`
- `"thin vs thick provisioning storage"`
- `"VirtualBox fixed size dynamically allocated"`

**연결**: [HOW A4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-a4)

---

# 부팅 흐름

## <a id="why-04"></a>WHY-04. BIOS vs UEFI

**중학생 버전**: 컴퓨터 전원을 켰을 때 가장 먼저 깨어나는 **문지기**. 옛날식(BIOS)은 디스크 맨 앞 512B(MBR)만 보고 OS 찾기. 새식(UEFI)은 파일시스템을 직접 읽어서 .efi 파일 실행.

**학부 버전**: **BIOS** = Basic Input/Output System, 1981 IBM PC 펌웨어 표준. 16-bit 실모드 코드, MBR(첫 512B) 에서 부트코드 로드. 2 TB 디스크 한계 + 4 primary 파티션 한계. **UEFI** = Unified Extensible Firmware Interface, 현대 표준. 32/64-bit 프로텍티드 모드, FAT 파일시스템 직접 읽어 `.efi` 로드. GPT 파티션 테이블로 > 2 TB, Secure Boot 지원. 본 과제는 MBR + GRUB 흐름이 Subject 의 `sda1/sda2/sda5` 파티션 예시와 일치하므로 **BIOS 모드 (UEFI 끔)** 사용.

**더 깊이 (구글 검색)**:
- `"UEFI vs BIOS differences explained"`
- `"Secure Boot how it works"`
- `"EFI System Partition ESP"`

**연결**: [HOW A3](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-a3) · [MBR_완역_주석_0장 §7](./_reference/MBR_완역_주석_0장.md)

---

## <a id="why-05"></a>WHY-05. MBR 구조 (512 바이트)

**중학생 버전**: 하드디스크 맨 앞 512 바이트에 있는 "이 디스크가 뭔지 + 어디서 부팅하는지" 적힌 메모장이에요.

**학부 버전**: **Master Boot Record** = 디스크 첫 섹터(LBA 0). 정확히 512 바이트. 구성: **446 B 부트 코드** + **64 B 파티션 테이블** (4 엔트리 × 16 B) + **2 B 시그니처** (0x55AA, little-endian). 한 엔트리: 부팅 플래그 1B + CHS 시작 3B + 타입 1B + CHS 끝 3B + LBA 시작 4B + 섹터 수 4B. LBA 4B → 2 TB 한계. Primary 최대 4 개. Extended 타입(0x05) 1 개로 Logical 여러 개 담는 EBR 체인.

**더 깊이 (구글 검색)**:
- `"MBR partition table structure"`
- `"MBR 2TB limit reason"`
- `"Extended Boot Record EBR chain"`

**연결**: [HOW C7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c7) · [MBR_완역_주석_0장 §3](./_reference/MBR_완역_주석_0장.md)

---

## <a id="why-06"></a>WHY-06. GRUB 다단계 부트로더

**중학생 버전**: BIOS 가 "여기부터 부팅해" 하면 GRUB 이 OS 선택 메뉴 보여주고 커널을 메모리에 올려줘요. MBR 446 바이트로는 부족해서 여러 단계로 나뉩니다.

**학부 버전**: **GRUB** (GRand Unified Bootloader). **Stage 1 (boot.img, 440 B)** 은 MBR 에 들어가고 Stage 1.5 의 위치만 안다. **Stage 1.5 (core.img)** 는 MBR 바로 뒤 파티션 전 공간(gap) 또는 /boot 파티션에 배치, 파일시스템·LUKS·LVM 모듈 포함. **Stage 2** = `/boot/grub/` 의 모듈·설정 파일. 최종적으로 커널(`/boot/vmlinuz-*`)과 initramfs(`/boot/initrd.img-*`) 를 메모리에 올려 점프. LUKS 암호화 디스크면 GRUB 의 `cryptomount` 모듈이 /boot 전 단계에서 해독 가능, 단 본 과제는 `/boot` 를 평문 파티션(sda1)에 둬 단순화.

**더 깊이 (구글 검색)**:
- `"GRUB boot.img core.img stages"`
- `"GRUB cryptomount encrypted boot"`
- `"MBR boot sequence step by step"`

**연결**: [HOW D4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-d4) · [MBR_완역_주석_0장 §8](./_reference/MBR_완역_주석_0장.md)

---

## <a id="why-07"></a>WHY-07. 커널과 initramfs

**중학생 버전**: 커널 = OS 의 진짜 몸통. initramfs = 커널이 완전히 서기 전에 잠깐 도와주는 **임시 도우미** (LUKS 해독, LVM 인식 등).

**학부 버전**: Linux kernel 은 bzImage 형태, `/boot/vmlinuz-*`. **initramfs** (`/boot/initrd.img-*`) = gzip 압축된 cpio 아카이브, RAM 에 임시 파일시스템으로 풀림. 커널 부팅 직후 이걸 root 로 마운트하고 `/init` 실행. 여기서 **실제 root FS 를 마운트하기 전 필수 작업** 수행 — LUKS 해독(cryptsetup), LVM 인식(lvm vgchange), 네트워크 드라이버 로드 등. 준비 끝나면 `switch_root` 로 실제 root 로 전환.

**더 깊이 (구글 검색)**:
- `"initramfs vs initrd difference"`
- `"switch_root pivot_root Linux boot"`
- `"Debian initramfs-tools hooks"`

**연결**: [HOW D6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-d6) · [MBR_완역_주석_0장 §9](./_reference/MBR_완역_주석_0장.md)

---

## <a id="why-08"></a>WHY-08. systemd 와 PID 1

**중학생 버전**: 부팅 완료 후 가장 먼저 깨어나는 **반장**. 필요한 서비스(SSH, cron, UFW) 를 순서대로 깨워줘요.

**학부 버전**: **systemd** = 현대 Linux 배포판의 init 시스템, **PID 1**. 유닛 파일 (`.service`, `.target`, `.timer`, `.mount`, `.socket`) 로 시스템 상태 기술. 의존성 기반 병렬 부팅 → 옛날 SysV init 보다 빠름. 로깅(journald, `journalctl`), 서비스 관리(`systemctl`), 타이머(cron 대체 가능). 본 과제의 `systemctl status apparmor`, `systemctl enable monitoring-startup` 등 명령은 모두 systemd API.

**더 깊이 (구글 검색)**:
- `"systemd unit types service target"`
- `"systemd vs SysV init parallel boot"`
- `"journalctl systemd logging"`

**연결**: [HOW E2.5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2-5) · [HOW E6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e6)

---

# 저장

## <a id="why-09"></a>WHY-09. 파티션 (primary / extended / logical)

**중학생 버전**: 하드디스크를 여러 칸으로 나누는 것. **primary** 는 큰 방 (최대 4 개), **extended** 는 큰 방 안에 작은 방들(**logical**) 여러 개 넣기.

**학부 버전**: MBR 의 파티션 테이블은 4 엔트리 고정 → 최대 4 **primary**. 5 개 이상 필요하면 엔트리 1 개를 **extended** 파티션(타입 0x05)으로 쓰고, 그 안에 **logical** 파티션들을 **EBR (Extended Boot Record) 체인** 으로 담음. Linux 에서 partition number: primary 1~4, logical 5~. 본 과제 Born2beRoot: `sda1` (primary, /boot), `sda2` (extended, 컨테이너만), `sda5` (logical, LUKS 암호화 대상).

**더 깊이 (구글 검색)**:
- `"primary extended logical partition"`
- `"EBR Extended Boot Record chain"`
- `"GPT vs MBR partition limits"`

**연결**: [HOW C7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c7) · [WHAT-07](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-07)

---

## <a id="why-10"></a>WHY-10. 파일시스템과 저널링

**중학생 버전**: 하드디스크 위에 "어떤 파일이 어디 있는지" 규칙을 정하는 것. **저널링** = "반쯤 쓰다 전원 꺼져도 복구 가능" 기능.

**학부 버전**: 파일시스템(FS) = 블록 → 파일/디렉토리 추상화. **ext4** = Linux 기본, journaling, inode 기반, 16 TiB 파일 / 1 EiB FS, `lvreduce` 호환(축소 가능). **xfs** = 대용량 최적 고성능, 그러나 **축소 불가** (본 과제 탈락). **btrfs** = CoW, 스냅샷, 체크섬, 복잡. **저널링** 은 쓰기 전에 메타데이터 변경을 저널 영역에 선기록 → 전원 중단 시 저널 재생으로 일관성 복구. 본 과제는 ext4 + 저널링.

**더 깊이 (구글 검색)**:
- `"ext4 vs xfs vs btrfs comparison"`
- `"journaling filesystem how it works"`
- `"ext4 inode structure"`

**연결**: [HOW C12](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c12)

---

## <a id="why-11"></a>WHY-11. LVM (PV / VG / LV)

**중학생 버전**: 하드디스크를 **레고 블록**처럼 쌓고 자를 수 있게 해주는 마법. PV = 블록 하나, VG = 블록 더미, LV = 그 안에서 자른 한 덩어리.

**학부 버전**: **Logical Volume Manager**. 3 계층 추상화:
- **PV (Physical Volume)**: `pvcreate` 로 블록 장치를 LVM 의 "벽돌" 로 등록. 본 과제 = `/dev/sda5_crypt` 1 개.
- **VG (Volume Group)**: `vgcreate` 로 PV 들을 묶어 "벽돌 더미". 본 과제 = `<hostname>-vg` 또는 `LVMGroup`.
- **LV (Logical Volume)**: `lvcreate` 로 VG 안에서 자른 가상 파티션. root/home/var/srv/tmp/var-log/swap 등.

운영 시 `lvextend`/`lvreduce` 로 동적 크기 변경, `lvcreate --snapshot` 으로 CoW 스냅샷. **Debian installer 의 LVM 메뉴에는 리사이즈 옵션이 없음** → 설치 단계에서는 Delete + Create 패턴.

**더 깊이 (구글 검색)**:
- `"LVM Physical Extent size"`
- `"lvcreate lvextend lvreduce examples"`
- `"device-mapper architecture Linux"`

**연결**: [HOW C8~C11](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c8) · [WHAT-07](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-07)

---

## <a id="why-12"></a>WHY-12. LUKS 와 dm-crypt (계층)

**중학생 버전**: 하드디스크 전체에 자물쇠 걸기. 부팅 때 비번 안 넣으면 그 안은 아무도 못 읽어요.

**학부 버전**: **LUKS** (Linux Unified Key Setup) = 블록 장치 암호화 표준. 파티션 시작에 LUKS 헤더 (magic + version + cipher spec + 8 개 키슬롯). **dm-crypt** = 커널의 device-mapper 모듈, 블록 단위 암/복호화. **cryptsetup** = 유저 공간 도구, `cryptsetup luksFormat/luksOpen`. 계층 순서: `디스크 → 파티션(sda5) → LUKS → 가상 블록장치(sda5_crypt) → LVM PV → VG → LV → ext4`. 즉 LUKS 는 **파티션 위, LVM 아래**.

**더 깊이 (구글 검색)**:
- `"LUKS header format keyslot"`
- `"dm-crypt kernel module explained"`
- `"cryptsetup luksFormat example"`

**연결**: [HOW C6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-c6) · [WHAT-07](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-07)

---

# 네트워크

## <a id="why-13"></a>WHY-13. NAT 와 포트 포워딩

**중학생 버전**: VM 은 "아파트 내부 호수" 같은 주소를 가져요. 밖에서 그 호수로 바로 못 와요. **경비실(NAT)** 이 "호스트의 이 포트로 오는 건 VM 의 저 포트로 보내줘" 매핑을 해줘야 연결 가능.

**학부 버전**: **NAT** (Network Address Translation) = 사설 ↔ 공용 IP 변환. VirtualBox **NAT 모드**: 게스트는 사설 IP 10.0.2.15 를 받음. 호스트 내부에 NAT 엔진(가상 라우터) 이 DHCP/DNS 흉내를 내고, 게스트의 outbound 트래픽은 호스트 네트워크로 내보냄. 외부에서 게스트로의 inbound 는 **포트 포워딩** 규칙 없이 불가 — VBox 의 포트 포워딩은 호스트 포트 → 게스트 포트를 매핑. 예: `호스트 2222 → 게스트 4242` 로 SSH 접근.

**더 깊이 (구글 검색)**:
- `"VirtualBox NAT vs Bridged Network"`
- `"iptables DNAT destination NAT"`
- `"port forwarding TCP SSH"`

**연결**: [HOW E2](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2)

---

## <a id="why-14"></a>WHY-14. SSH 와 공개키 암호 (간단)

**중학생 버전**: 원격 컴퓨터에 안전하게 접속하는 **암호화된 문**. 비번 대신 "공개 열쇠 + 비밀 열쇠" 한 쌍으로 더 안전하게 쓸 수도 있어요.

**학부 버전**: **SSH** (Secure Shell) = 원격 셸 프로토콜, 기본 TCP **22**. 연결 시 (1) 비대칭 키 교환 (Diffie-Hellman) 으로 대칭 세션 키 합의 → (2) 대칭 암호 (AES, ChaCha20) 로 트래픽 암호화 → (3) 사용자 인증 (비번 / 공개키 / Kerberos 등). 서버 = `sshd` 데몬, `/etc/ssh/sshd_config` 로 제어. Born2beRoot 의 핵심 설정: `Port 4242` (비표준으로 봇 공격 표면 축소), `PermitRootLogin no` (root 직접 접속 금지).

**더 깊이 (구글 검색)**:
- `"SSH key exchange Diffie-Hellman"`
- `"SSH public key authentication ~/.ssh/authorized_keys"`
- `"sshd_config best practices"`

**연결**: [HOW E2](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2) · [WHAT-08](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-08) · [WHAT-09](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-09)

---

## <a id="why-15"></a>WHY-15. UFW / iptables 방화벽

**중학생 버전**: 컴퓨터의 **출입 통제 장치**. 허용한 문만 열어두고 나머지는 다 막음. UFW 는 iptables 를 쉽게 쓰게 해주는 도우미.

**학부 버전**: **iptables** = Linux 커널의 netfilter 프레임워크 프론트엔드. 체인 (INPUT / OUTPUT / FORWARD) × 테이블 (filter / nat / mangle / raw) 에 규칙 등록. **UFW** (Uncomplicated Firewall) = iptables 추상 도우미. `ufw default deny incoming` → INPUT 체인 기본 DROP, `ufw allow 4242` → TCP 4242 ACCEPT 규칙 삽입. 화이트리스트 정책 (**기본 거부 + 명시 허용**) 이 서버 보안 표준. firewalld = Rocky/RHEL 의 유사 도구 (zone 기반). nftables = iptables 후속.

**더 깊이 (구글 검색)**:
- `"iptables chain table explained"`
- `"UFW vs firewalld comparison"`
- `"nftables vs iptables"`

**연결**: [HOW E3](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e3) · [WHAT-10](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-10)

---

# 권한·정책

## <a id="why-16"></a>WHY-16. root / sudo

**중학생 버전**: **root** = 학교장, 모든 권한. **sudo** = 학생 회장, 필요할 때만 학교장 권한을 빌려 씀 + 빌린 내역 기록.

**학부 버전**: Linux 권한 모델 = DAC (Discretionary Access Control). **root** (UID 0) 는 거의 모든 체크를 우회. 직접 root 로 상시 작업 시 모든 실수가 즉시 시스템 영향. **sudo** = `/etc/sudoers` 에 허용된 명령을 사용자 비밀번호 인증 후 root 로 실행 + 모든 호출/입력/출력을 로그. Defaults 옵션: `secure_path` (PATH 변조 방지), `requiretty` (데몬 sudo 차단), `log_input/log_output` (감사), `passwd_tries` (오인증 제한), `badpass_message` (사용자 정의 오류). 본 과제는 Subject 가 이 모든 옵션을 **명시적으로 요구**.

**더 깊이 (구글 검색)**:
- `"sudo vs su differences"`
- `"sudoers Defaults options reference"`
- `"Linux DAC vs MAC access control"`

**연결**: [HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) · [WHAT-14](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-14) 외 sudo 조항들

---

## <a id="why-17"></a>WHY-17. 그룹과 getent

**중학생 버전**: 여러 사용자를 묶어 "너희는 같은 권한" 이라 정하는 것. `sudo` 그룹 멤버면 `sudo` 명령을 쓸 수 있음.

**학부 버전**: Linux **그룹** = 사용자 묶음, GID 로 식별. `/etc/group` 에 정의 (형식: `groupname:x:GID:member1,member2,...`). 한 사용자는 **primary group** (`/etc/passwd` 의 gid) 1 개 + **supplementary groups** (login 시 자동 부여) 여러 개. 파일 권한 `rwxrwxrwx` 의 middle triple 이 그룹 멤버십 기반. `getent group sudo` = NSS(Name Service Switch)를 통해 그룹 조회, `/etc/group` + LDAP 등 여러 소스 통합. `usermod -aG sudo,user42 <login>` 로 그룹 추가.

**더 깊이 (구글 검색)**:
- `"/etc/group format primary supplementary"`
- `"getent nsswitch.conf"`
- `"Linux file permissions rwx"`

**연결**: [HOW E4](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e4) · [WHAT-16](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-16)

---

## <a id="why-18"></a>WHY-18. PAM 과 pwquality

**중학생 버전**: 로그인·비번 변경할 때 "비번이 강한지" 검사하는 **모듈들의 연속 체인**. PAM 은 검사 규칙을 모듈 형태로 끼우는 프레임워크.

**학부 버전**: **PAM** (Pluggable Authentication Modules) = 인증 관련 작업을 모듈로 분리한 프레임워크. `/etc/pam.d/<service>` 에 스택 정의 (auth / account / password / session 4 타입). Control flag: `requisite` (실패 즉시 종료), `required` (실패해도 끝까지 진행), `sufficient` (성공 시 즉시 통과). **pam_pwquality.so** = 비밀번호 품질 검사 모듈. 옵션: `minlen`, `dcredit/ucredit/lcredit` (음수 = 강제, 양수 = 가산점), `difok` (이전 비번과 다른 글자 수), `maxrepeat`, `reject_username`, `enforce_for_root` (root 에도 정책 적용 — 단 `difok` 는 pwquality 내부 로직상 root 예외). `/etc/login.defs` 는 별개 영역 (PASS_MAX_DAYS 등 만료 정책).

**더 깊이 (구글 검색)**:
- `"PAM stack requisite required sufficient"`
- `"pam_pwquality.so options reference"`
- `"login.defs vs PAM password policy"`

**연결**: [HOW E5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e5) · [WHAT-13](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-13) 외 비번 정책 조항들

---

## <a id="why-19"></a>WHY-19. AppArmor vs SELinux

**중학생 버전**: "어떤 프로그램이 어떤 파일·기능을 쓸 수 있다" 를 엄격히 제한하는 **경찰**. AppArmor 는 **경로 기반**(파일명 보고 판단), SELinux 는 **라벨 기반**(파일에 붙은 스티커 보고 판단). SELinux 가 더 정교하지만 설정 어려움.

**학부 버전**: 둘 다 **MAC** (Mandatory Access Control) — DAC(사용자 설정)와 달리 시스템이 강제하는 접근 제어. **AppArmor** (Ubuntu/Debian 기본) = path-based profiles, `/etc/apparmor.d/` 에 파일 경로 패턴 + 허용 작업 (`r`, `w`, `ix` 등). 예: `/usr/sbin/sshd` 가 `/etc/ssh/**` 만 읽기 가능. **SELinux** (RHEL/Rocky/Fedora 기본) = label-based, 모든 객체(파일·프로세스·소켓)에 security context `user:role:type:level` 라벨. policy 가 type 간 관계 규정 (TE: Type Enforcement). `enforcing` / `permissive` / `disabled` 모드. Subject 는 Debian 이면 AppArmor 시작 시 작동 필수.

**더 깊이 (구글 검색)**:
- `"AppArmor vs SELinux comparison"`
- `"MAC vs DAC Linux security"`
- `"SELinux type enforcement policy"`

**연결**: [HOW E2.5](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e2-5) · [WHAT-06](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-06) · [WHAT-35](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-35)

---

# 모니터링

## <a id="why-20"></a>WHY-20. cron 과 wall

**중학생 버전**: **cron** 은 알람 시계 (정해진 시간에 자동 실행). **wall** 은 확성기 (로그인한 모든 사람에게 메시지 뿌리기).

**학부 버전**: **cron** = 주기 작업 스케줄러 데몬. `crontab -e` 로 사용자별 작업 등록, `/etc/cron.d/` 는 시스템용 (패키지가 설치 시 둠), `/etc/cron.{hourly,daily,weekly}` 는 시간대별 폴더. crontab 형식: `분 시 일 월 요일 명령`. **`@reboot`** = 부팅 직후 1 회 실행. **`*/10 * * * *`** = 10 분마다. **wall** (write all) = `/var/run/utmp` 의 활성 터미널 사용자들에게 브로드캐스트 메시지. 본 과제 `bash monitoring.sh | wall` = 스크립트 출력을 모든 터미널에 뿌림. Debian 13 에서 wall 의 setgid tty 박탈 → SSH 세션(pts) 에 메시지 미도달 (v5.2 메인 가이드 Part 0 §2 참조).

**더 깊이 (구글 검색)**:
- `"crontab syntax @reboot"`
- `"wall command Linux utmp"`
- `"CVE-2024-28085 wall Debian"`

**연결**: [HOW E6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e6) · [WHAT-30](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-30)

---

## <a id="why-21"></a>WHY-21. monitoring 12 항목 (왜 이 조합)

**중학생 버전**: 서버의 **건강 검진 12 가지**. 서버가 지금 얼마나 바쁜지 + 얼마나 찼는지 + 누가 쓰는지 등을 한 번에 알려줘요.

**학부 버전**: Subject 의 12 항목은 서버 운영자가 실시간 알아야 할 최소 지표 4 카테고리:
- **정체성** (Architecture, Physical CPU, vCPU, LVM active) — `uname -a`, `lscpu`, `lsblk`
- **자원** (Memory 사용·%, Disk 사용·%, CPU load %) — `free`, `df`, `top`
- **시간** (Last boot) — `who -b` 또는 `uptime -s`
- **활동** (TCP 연결 수, 로그인 사용자 수, IPv4 + MAC, sudo 명령 수) — `ss`, `who`, `ip a`, `journalctl _COMM=sudo` 또는 `/var/log/auth.log` 파싱

12 개 조합이 "너무 많지도 적지도 않은" 산업 표준 수준의 **경량 헬스 체크**. cron 10 분 주기 = 1 분 너무 잦음, 1 시간 너무 느림의 중간.

**더 깊이 (구글 검색)**:
- `"bash sysadmin monitoring script example"`
- `"uname lscpu free df uptime cheatsheet"`
- `"/var/log/auth.log sudo count"`

**연결**: [HOW E6](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e6) · [WHAT-31](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-31)

---

# 웹 스택 (보너스)

## <a id="why-22"></a>WHY-22. lighttpd 와 FastCGI

**중학생 버전**: **lighttpd** = 가볍고 빠른 웹 서버 (NGINX/Apache 금지 조항 때문에 씀). **FastCGI** = 웹 서버가 PHP 같은 외부 언어를 **빠르게 호출하는 통로**.

**학부 버전**: **lighttpd** = 단일 프로세스 이벤트 기반 경량 HTTP 서버, 메모리 ~30 MB. 설정 `/etc/lighttpd/lighttpd.conf`. **CGI** (Common Gateway Interface) = 요청마다 새 프로세스 fork + 환경변수 + stdin 전달 → 매 요청 오버헤드 큼. **FastCGI** = 워커 프로세스를 **상주** 시키고 Unix socket/TCP 로 요청 전달 → 프로세스 재사용 → 10~100 배 성능. Debian 에서 `lighttpd-enable-mod fastcgi fastcgi-php` 가 `/etc/lighttpd/conf-enabled/` 에 심볼릭 링크 생성. PHP-FPM 은 FastCGI Process Manager, 독립 데몬.

**더 깊이 (구글 검색)**:
- `"FastCGI vs CGI performance benchmark"`
- `"PHP-FPM lighttpd setup"`
- `"lighttpd.conf fastcgi module"`

**연결**: [HOW E7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e7) · [WHAT-37](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-37)

---

## <a id="why-23"></a>WHY-23. MariaDB (MySQL fork)

**중학생 버전**: MySQL 과 거의 똑같은 DB. Oracle 이 MySQL 을 산 뒤 원 개발자가 걱정돼서 똑같이 다시 만든 **형제 프로그램**.

**학부 버전**: **MariaDB** = MySQL 의 drop-in replacement fork, 원 개발자 Michael "Monty" Widenius 가 2009 Oracle 의 MySQL 인수 후 창설. 대부분 쿼리/프로토콜/커넥터 호환. Storage engine 은 InnoDB (MySQL 과 공용) + MariaDB 전용 Aria, TokuDB, ColumnStore 등. Debian 12 부터 `default-mysql-server` / `default-mysql-client` 메타 패키지가 **MariaDB 를 가리킴** → Debian 표준. WordPress, Drupal 등 CMS 호환. 관리는 `mariadb` 또는 `mysql` 클라이언트, `/etc/mysql/` 설정.

**더 깊이 (구글 검색)**:
- `"MariaDB vs MySQL differences"`
- `"MariaDB storage engines"`
- `"Debian default-mysql-server MariaDB"`

**연결**: [HOW E7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e7) · [WHAT-37](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-37)

---

## <a id="why-24"></a>WHY-24. PHP 와 www-data

**중학생 버전**: **PHP** = 동적 웹페이지(로그인, 댓글 등) 만들 때 쓰는 언어. **www-data** = 웹 서버 전용 낮은 권한 계정 (root 아님).

**학부 버전**: **PHP** = 서버 측 스크립트 언어, `.php` 파일을 인터프리터가 실행해 HTML 출력. Debian 에서 lighttpd/Apache 는 **`www-data`** 계정으로 실행 (/etc/passwd, UID 33) — 낮은 권한으로 실행해 프로세스 취약점 악용 시 피해 제한. `/var/www/html/` 의 소유자·그룹이 `www-data` 가 아니면 PHP 가 파일을 쓰지 못해 업로드/캐시 실패. `chown -R www-data:www-data /var/www/html/` 는 WordPress 셋업의 필수 마무리 단계.

**더 깊이 (구글 검색)**:
- `"www-data user Linux purpose"`
- `"PHP file permissions chown"`
- `"PHP-FPM pool user group"`

**연결**: [HOW E7](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e7) · [WHAT-37](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-37)

---

# 무결성

## <a id="why-25"></a>WHY-25. 해시 함수 (SHA-1)

**중학생 버전**: 파일 전체를 **고유한 지문** 으로 줄여주는 수학 함수. 파일의 1 비트만 바뀌어도 지문이 완전히 달라져요. "내가 낸 파일과 평가할 파일이 같다" 증명용.

**학부 버전**: **암호학적 해시 함수** = 임의 길이 입력 → 고정 길이 출력, 다음 3 가지 성질:
- **preimage resistance**: 출력 → 입력 역산 불가
- **second-preimage resistance**: 특정 입력의 동일 해시 다른 입력 찾기 불가
- **collision resistance**: 임의 두 입력이 같은 해시 갖게 하기 불가

**SHA-1** = 160-bit 출력 (40 자 16 진수). 2017 Google **SHAttered** 공격으로 충돌 데모 → 암호학적 신뢰 하락. Git, TLS 서명 등 보안 중요 용도에선 SHA-256 이상 권장. 그러나 **단순 무결성 확인** (내가 낸 파일 == 평가 파일) 용도로는 여전히 충분. Subject 가 `sha1sum` 을 지정.

**더 깊이 (구글 검색)**:
- `"SHA-1 SHAttered collision attack"`
- `"cryptographic hash function properties"`
- `"sha1sum vs sha256sum"`

**연결**: [HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9) · [WHAT-40](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-40)

---

## <a id="why-26"></a>WHY-26. VBox 스냅샷 (Copy-on-Write)

**중학생 버전**: VM 의 **게임 저장 슬롯**. 저장된 시점으로 되돌릴 수 있어요. 파일 전체 복사 대신 "바뀐 부분만" 기록해서 가벼워요.

**학부 버전**: VirtualBox **snapshot** = copy-on-write 기반 시점 보존. 스냅샷 찍은 순간의 `.vdi` 를 **immutable base** 로 고정 + 이후 모든 변경은 **differencing .vdi** 에 기록 (`xxx-Snapshot-yyy.vdi`). 스냅샷 삭제 = differencing 파일을 base 로 병합(merge). 스냅샷 복원 = differencing 버리고 base 상태로 되돌림. Git branch / COW 파일시스템 (btrfs) 과 유사 개념. **Subject v5.2 정책**: 각 평가 시작 시 스냅샷 **없음** 상태 강제 → `.vdi` 해시 일치성 확인 위해. 평가 중 스냅샷 만들어 위기 시 복원 가능하지만 평가 끝나면 즉시 삭제.

**더 깊이 (구글 검색)**:
- `"VirtualBox snapshot differencing disk"`
- `"copy-on-write filesystem btrfs"`
- `"VDI merge snapshot"`

**연결**: [HOW E9](./Born2beRoot_v5_2_HOW_2026-04-18.md#how-e9) · [WHAT-03](./Born2beRoot_v5_2_WHAT_2026-04-18.md#what-03)

---

# 참고 자료 (원천)

**교재 수준** (학부 전공 필수):
- Silberschatz et al., *Operating System Concepts* (§2.9 Booting, §10 Mass-Storage, §13 File-System)
- Tanenbaum & Bos, *Modern Operating Systems* (§6 Deadlocks·§4 Memory·§10 Linux)
- Stallings, *Operating Systems: Internals and Design Principles*

**공식 문서** (정확성 보증):
- Debian Administrator's Handbook (debian-handbook.info)
- Linux man-pages (`man sudoers`, `man pam_pwquality`, `man systemd.service`)
- VirtualBox User Manual (virtualbox.org/manual/)
- GNU GRUB Manual (gnu.org/software/grub/manual/)

**본 프로젝트 내부 자료**:
- [`MBR_완역_주석_0장.md`](./_reference/MBR_완역_주석_0장.md) — BIOS→MBR→GRUB→커널→initramfs 흐름 상세
- [`virtualbox_howtosetcpu.md`](./_reference/virtualbox_howtosetcpu.md) — vCPU/pCPU 본인 정리 (WHY-02 보조)
- [`하드웨어자료조사.md`](./_reference/하드웨어자료조사.md) — 하드웨어 결정 근거

**짝 문서**:
- [`Born2beRoot_v5_2_HOW_2026-04-18.md`](./Born2beRoot_v5_2_HOW_2026-04-18.md) — 진행 가이드
- [`Born2beRoot_v5_2_WHAT_2026-04-18.md`](./Born2beRoot_v5_2_WHAT_2026-04-18.md) — Subject 매핑

---

# 심화 탐색 안내

본 문서의 각 개념은 **학부 수준** 에서 멈춥니다. 대학원 수준의 실제 구현·알고리즘·성능 분석을 원한다면:

1. 해당 개념 본문 끝의 **"더 깊이 (구글 검색)"** 키워드를 브라우저에 복붙
2. 상위 3~5 결과 중 `*.edu`, `*.org`, 공식 man page, 논문 PDF 우선 클릭
3. "how it works internally", "implementation", "benchmark" 같은 수식어를 검색어에 추가하면 깊이 증가

본 3 문서 + 위 키워드 자료로 **컴퓨터공학 학부 4 년 과정의 시스템 관련 전공 지식을 거의 커버** 합니다. Born2beRoot 평가 통과 + Rank 01 이후 rank 에서 요구하는 시스템 기반 지식의 출발점으로 충분.
