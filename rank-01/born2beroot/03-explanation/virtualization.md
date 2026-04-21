---
description: "하이퍼바이저, vCPU·pCPU, 가상 디스크 (VDI, Pre-Allocate) 배경 이론."
---

# 💡 Explanation — 가상화

> **문서 유형**: Explanation. 이론·배경·설계 의도. 절차 없음.

## 하이퍼바이저 (Type 1 vs Type 2)

**핵심**: 물리 하드웨어 자원(CPU, 메모리, 디스크) 을 여러 가상 컴퓨터에게 **시간 분할·공간 분할** 로 나누어주는 소프트웨어 계층.

- **Type 1 (Bare-metal)**: 하드웨어 위에 직접 돌아감. VMware ESXi, Microsoft Hyper-V, KVM. 오버헤드 최소, 데이터센터용.
- **Type 2 (Hosted)**: 기존 OS 위에 일반 프로그램처럼 실행. VirtualBox, VMware Workstation. 설치·관리 쉽지만 중간 계층이 추가돼 오버헤드 존재. 개발자 데스크탑용.

Born2beRoot 는 Type 2 VirtualBox.

### 30 초 답변 (평가자 Q&A 용)
"VirtualBox 는 호스트 OS 위에서 실행되는 Type 2 하이퍼바이저입니다. 호스트 커널이 제공하는 CPU 가상화 명령(VT-x/AMD-V) 을 활용해, 게스트 OS 에게 가상 CPU·메모리·디스크를 제공합니다. 게스트는 자신이 진짜 하드웨어 위에서 돈다고 믿습니다."

### 더 깊이
- `"Type 1 vs Type 2 hypervisor differences"`
- `"VT-x AMD-V hardware virtualization explained"`
- `"KVM vs VirtualBox performance"`

---

## <a id="vcpu"></a>vCPU 와 pCPU (과할당의 역설)

**vCPU** = 게스트 OS 가 보는 논리 CPU. **pCPU** = 호스트의 물리 CPU 스레드.

하이퍼바이저가 vCPU → pCPU 로 스케줄링. **SMP VM** 은 여러 vCPU 가 **co-scheduling** (동시에 pCPU 를 얻어야 진행) 을 요구해, vCPU 수가 pCPU 수에 근접하거나 넘으면 **모든 vCPU 가 동시에 비어 있을 때까지 대기** → 유휴 시간 급증.

결과: 욕심내 vCPU 를 많이 주면 **VM 이 오히려 느려짐**.

Born2beRoot 권장: vCPU = 호스트 물리 코어 수 **이하**. 2~3 개면 충분.

### 30 초 답변
"VM 에 vCPU 를 여러 개 주면 게스트 OS 는 멀티코어로 인식하지만, 실제 실행은 호스트의 pCPU 가 모든 vCPU 를 동시에 스케줄링할 수 있어야 가능합니다. vCPU 를 pCPU 보다 많이 할당하면 co-scheduling 대기 때문에 성능이 오히려 떨어집니다."

### 더 깊이
- `"CPU co-scheduling virtual machine"`
- `"vCPU oversubscription penalty"`
- `"VMware Ready Time %RDY"`

---

## 가상 디스크 (VDI, Pre-Allocate)

`.vdi` = VirtualBox Disk Image. 호스트 파일시스템 위의 단일 파일이 게스트 입장에선 블록 장치 하나.

**Dynamic allocation** vs **Fixed-size (Pre-Allocate)**:

| 특성 | Dynamic | Pre-Allocate |
|---|---|---|
| 생성 시 공간 | 거의 0 | 전체 크기 (20 GB) |
| 쓰기 시 | 필요할 때 호스트 확장 | 이미 확보됨 |
| 단편화 | 발생 가능 | 최소 |
| I/O 성능 | 낮음 (확장 오버헤드) | 일관적으로 높음 |
| 호스트 공간 예측 | 어려움 | 명확 |

Born2beRoot 권장: Pre-Allocate. 평가 중 디스크 부족 / I/O 저하 리스크 제거.

### 30 초 답변
"VDI 는 VirtualBox 네이티브 이미지 포맷입니다. Pre-Allocate 로 생성하면 20 GB 를 호스트 디스크에 연속 블록으로 미리 잡아두어 단편화를 방지하고 I/O 성능이 일관됩니다. Dynamic 은 초기 공간을 아끼지만 쓰기마다 확장이 일어나 느립니다."

### 더 깊이
- `"VDI vs VMDK format comparison"`
- `"thin vs thick provisioning storage"`
- `"VirtualBox fixed size dynamically allocated"`

---

## 관련 문서

- [튜토리얼 Part A (VM 생성)](../00-tutorial/install-your-first-vm.md#part-a-virtualbox-vm-생성)
- [설계 결정 ADR-004 (VirtualBox vs UTM)](design-decisions.md#adr-004-virtualbox-over-utm)
- [Subject v5.2 SC-01](../02-reference/subject-v5.2-clauses.md#sc-01-virtualbox-강제)
