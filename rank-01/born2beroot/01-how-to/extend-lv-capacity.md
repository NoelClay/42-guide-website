---
description: "특정 LV (예: /var, /var/log) 가 꽉 찼을 때 lvextend + resize2fs 로 온라인 확장."
---

# 🎯 How-to — LV 용량 확장

> **문서 유형**: How-to. `/var` 또는 `/var/log` 가 꽉 찼을 때 재부팅 없이 확장.
> **전제**: LVM 기반 설치 (Guided encrypted LVM). VG 에 빈 공간 필요.

## 언제 써야 하나

- `df -h` 결과 `/var` 또는 다른 마운트 포인트가 95% 이상
- monitoring.sh 출력의 Disk 항목이 빨간색 수준
- apt 설치 실패 메시지 "No space left on device"

---

## 1. 현재 상태 진단

```bash
df -h
lvs
vgs
```

**읽는 법**:
- `df -h` → 마운트별 사용률
- `lvs` → 각 LV 의 현재 크기
- `vgs` → `VFree` 가 VG 에 남은 여유 공간

예시:
```
VG              #PV #LV #SN Attr   VSize   VFree
<hostname>-vg    1   7   0 wz--n-  20.50g   3.00g   ← 3 GB 여유 있음
```

여유가 0 이면 → [다른 LV 축소](#reduce-first) 필요.

## 2. LV 확장 (가장 흔한 케이스: /var/log 1 GB → 3 GB)

```bash
sudo lvextend -L +2G /dev/<hostname>-vg/var-log
```

또는 "남은 공간 전부":

```bash
sudo lvextend -l +100%FREE /dev/<hostname>-vg/var-log
```

## 3. 파일시스템을 LV 에 맞게 확장

**ext4 는 온라인 확장 가능** — 마운트된 상태 그대로:

```bash
sudo resize2fs /dev/<hostname>-vg/var-log
```

한 방에 하기 (`-r` 옵션):

```bash
sudo lvextend -L +2G -r /dev/<hostname>-vg/var-log
```

## 4. 검증

```bash
df -h /var/log
lvs
```

`df -h` 출력의 Size 열이 커졌으면 성공.

---

## <a id="reduce-first"></a>다른 LV 축소로 공간 확보

**경고**: 축소는 **데이터 손실 위험**. 먼저 백업 필수.

### tmp 축소 예시 (tmp 1 GB → 512 MB)

```bash
# tmp 비우기
sudo rm -rf /tmp/*

# 언마운트 (root 파티션이면 라이브 CD 필요, tmp 는 가능)
sudo umount /tmp

# 파일시스템 축소
sudo e2fsck -f /dev/<hostname>-vg/tmp
sudo resize2fs /dev/<hostname>-vg/tmp 512M

# LV 축소
sudo lvreduce -L 512M /dev/<hostname>-vg/tmp

# 다시 마운트
sudo mount /tmp
```

**root 파티션 축소는 불가** (마운트 해제 못 함). Rescue 모드 + live CD 필요.

---

## 스왑 확장 (보너스)

WordPress/MariaDB 설치 후 메모리 부족이면 스왑 확장:

```bash
sudo swapoff /dev/<hostname>-vg/swap_1
sudo lvextend -L +1G /dev/<hostname>-vg/swap_1
sudo mkswap /dev/<hostname>-vg/swap_1
sudo swapon /dev/<hostname>-vg/swap_1
```

---

## 언제 이걸 하면 안 되나

- **평가 당일**: LV 구성 변경은 `lsblk` 결과를 Subject 예시와 다르게 만들 수 있음. 평가 전날까지 완료.
- **signature 생성 후**: `.vdi` 가 변경되어 서명 불일치. [서명 재생성](recover-from-lockout.md#signature-mismatch) 필요.

## 관련 문서

- [저장 스택 설명](../03-explanation/storage-stack.md)
- [명령 치트시트 (L 섹션)](../02-reference/commands-cheatsheet.md#l)
- [서명 재생성](recover-from-lockout.md#signature-mismatch)
