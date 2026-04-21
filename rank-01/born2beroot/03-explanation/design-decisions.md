---
description: "4 가지 비교(Debian/Rocky, AppArmor/SELinux, UFW/firewalld, VirtualBox/UTM) ADR 스타일 기록."
---

# 💡 Explanation — 설계 결정 (ADR)

> **문서 유형**: Explanation. Subject SC-35 가 요구하는 4 가지 비교를 **Architecture Decision Record** 형식으로.
> **참고**: ADR 은 Michael Nygard 가 제안한 기록 양식으로, 결정의 맥락·선택지·근거·트레이드오프를 한 자리에 남김.

## 목차

| ADR | 결정 | 상태 |
|---|---|---|
| [ADR-001](#adr-001-debian-over-rocky) | Debian over Rocky | 승인 |
| [ADR-002](#adr-002-apparmor-over-selinux) | AppArmor over SELinux | 승인 (Debian 의 필연) |
| [ADR-003](#adr-003-ufw-over-firewalld) | UFW over firewalld | 승인 (Debian 의 자연 선택) |
| [ADR-004](#adr-004-virtualbox-over-utm) | VirtualBox over UTM | 승인 (x86 호스트의 경우) |

---

## <a id="adr-001-debian-over-rocky"></a>ADR-001: Debian over Rocky

### 상태
승인

### 맥락
Subject SC-05 는 Debian 최신 안정판 또는 Rocky 최신 안정판 중 선택하라고 한다. Subject 주석은 "Debian 은 시스템 관리 입문자에게 강력 권장" 이라고 덧붙인다.

### 선택지
1. **Debian 12 Bookworm** (2023 release)
2. **Rocky Linux 9** (2022 release, RHEL 9 호환)

### 결정
**Debian** 선택.

### 근거
- **커뮤니티 규모**: Debian 은 세계 최대 독립 Linux 배포판. 42 커뮤니티·Stack Overflow·블로그 자료가 Rocky 의 수 배.
- **APT 생태계 재사용**: Rank 04 Inception 등 후속 과제도 Debian 계열 사용 가능 → 학습 자산 연속성.
- **AppArmor 기본 내장**: Subject SC-06 이 AppArmor 를 Debian 에 한정해 요구. Rocky 선택 시 SELinux + DNF + RHEL 호환 이슈를 별도로 학습해야 함.
- **입문자 친화**: Subject 자체가 Debian 권장.

### 트레이드오프
- **엔터프라이즈 지원 부재**: Rocky 는 RHEL 바이너리 호환으로 실무에서 자주 쓰임. 취업 시장에서 RHEL/Rocky 경험 가치는 Debian 보다 큼 (특히 금융·대기업).
- **SELinux 미경험**: 현대 컨테이너(OpenShift, Kubernetes)·클라우드 환경은 SELinux 가 더 흔함. Rank 02+ 에서 별도 학습 부담.

### 결과
본 과제 기준으로 Debian 이 **학습 속도 + Subject 충실도 + 커뮤니티 자원** 3 축에서 우세. 단, 장기적으로 Rocky/RHEL 경험은 별도 프로젝트로 보완 권장.

---

## <a id="adr-002-apparmor-over-selinux"></a>ADR-002: AppArmor over SELinux

### 상태
승인 — ADR-001 의 필연적 귀결.

### 맥락
MAC (Mandatory Access Control) 을 Born2beRoot 가 요구 (Debian: AppArmor SC-06, Rocky: SELinux 암묵).

### 선택지
1. **AppArmor** (path-based)
2. **SELinux** (label-based)

### 결정
**AppArmor**.

### 근거
- **Path-based 는 직관적**: `/usr/sbin/sshd` 가 `/etc/ssh/**` 만 읽는다는 규칙이 파일 경로로 직접 표현됨.
- **Debian 기본**: 별도 설정 없이 `systemctl enable apparmor` 만으로 Subject SC-06 충족.
- **운영 오버헤드 낮음**: 기본 프로필만 사용해도 평가 통과. 커스텀 프로필 작성 요구 없음.

### 트레이드오프
- **정교함 부족**: SELinux 의 type enforcement + MLS 는 고보안 환경(국방, 금융 인프라) 표준. AppArmor 는 그런 섬세한 격리에 부족.
- **심볼릭 링크 우회 가능**: 경로 기반이라 심볼릭 링크로 우회 가능한 시나리오가 SELinux 보다 많음.

### 결과
본 과제의 학습 목표(MAC 존재와 기본 동작 이해) 에는 AppArmor 면 충분. 심화 학습은 별도 projet.

---

## <a id="adr-003-ufw-over-firewalld"></a>ADR-003: UFW over firewalld

### 상태
승인 — Debian 선택의 자연 귀결.

### 맥락
방화벽을 Subject SC-10 이 요구. Debian 은 UFW, Rocky 는 firewalld 가 기본.

### 선택지
1. **UFW** (Uncomplicated Firewall, Ubuntu 기원)
2. **firewalld** (Fedora/RHEL 기원)
3. (raw iptables / nftables — Subject 가 별도 도구 요구하므로 제외)

### 결정
**UFW**.

### 근거
- **명령 문법이 직관적**: `ufw allow 4242/tcp`, `ufw status` — 영어 문장과 거의 1:1.
- **Debian 표준 패키지**: `apt install ufw` 한 줄로 설치 완료.
- **Subject 예시 일치**: Subject SC-10 이 "UFW (또는 Rocky 의 firewalld)" 로 명시.

### 트레이드오프
- **단일 zone 만**: firewalld 는 public/internal/trusted 등 zone 별 다른 정책 가능. UFW 는 하나의 룰셋.
- **대규모 운영 약점**: 서버 수십 대에 이중 NIC 가 있고 서브넷별 정책이 복잡하면 firewalld 가 유리.

### 결과
Born2beRoot 처럼 단일 호스트·단일 인터페이스·단일 허용 포트 시나리오에는 UFW 가 최적.

---

## <a id="adr-004-virtualbox-over-utm"></a>ADR-004: VirtualBox over UTM

### 상태
승인 — x86 호스트의 경우.

### 맥락
Subject SC-01 은 VirtualBox 강제, "UTM 은 VirtualBox 불가 시에만" — 즉 **Apple Silicon (ARM64) 호스트의 예외 허용**.

### 선택지
1. **VirtualBox** (Oracle, x86_64 full virtualization)
2. **UTM** (Apple Silicon 용 QEMU 래퍼)

### 결정
- **Intel/AMD x86 호스트** → VirtualBox
- **Apple Silicon (M1/M2/M3) 호스트** → UTM (QEMU TCG 또는 Hypervisor.framework)

### 근거
- **VirtualBox**:
  - x86 하드웨어 가상화 (VT-x/AMD-V) 사용 → near-native 성능
  - 포트 포워딩·공유 폴더 GUI 설정
  - Linux·Windows·macOS (Intel) 공통 사용 가능
- **UTM**:
  - Apple Silicon 에서는 **VirtualBox 미지원** (Oracle 이 ARM 호스트 지원 미완)
  - QEMU 기반, `aarch64` 게스트는 Hypervisor.framework, `x86_64` 게스트는 TCG 에뮬레이션 (느림)

### 트레이드오프
- **UTM + x86_64 게스트 on Apple Silicon**: 에뮬레이션이라 VirtualBox 대비 수 배 느림. 설치 시간·평가 대응 속도 저하.
- **UTM + aarch64 게스트**: Debian ARM64 가능하지만, Subject 의 파티션 예시 (`sda5`) 및 많은 자료가 x86 기준이라 혼선 가능.

### 결과
- 42 학생의 대다수는 Intel x86 학교 iMac 사용 → VirtualBox 자연 선택.
- Apple Silicon 개인 노트북 사용자만 UTM 사용 + 추가 디버깅 감수.

---

## ADR 작성 패턴 재사용

위 4 개 ADR 은 다음 템플릿을 공유:

```markdown
## ADR-NNN: <결정 한 문장>

### 상태
[승인 / 거절 / 대체 / 폐기]

### 맥락
왜 이 결정이 필요해진 상황인지.

### 선택지
1. Option A
2. Option B
...

### 결정
최종 선택.

### 근거
왜 이 선택인지 (장점 나열).

### 트레이드오프
이 선택이 포기한 것 (단점·대안의 장점).

### 결과
선택 이후 관찰된 실제 효과 (6 개월 후 작성 가능).
```

후속 결정 (예: cron vs systemd timer, bash vs python monitoring) 이 생기면 이 패턴으로 추가.

## 관련 문서

- [peer evaluation Gate 2 (4 비교 발표)](../01-how-to/pass-peer-evaluation.md#gate-2-readmemd-4-비교-발표-3-분)
- [Subject SC-35](../02-reference/subject-v5.2-clauses.md#sc-35-project-description--4-가지-비교)
- [가상화 배경](virtualization.md)
- [접근 제어 (AppArmor 상세)](access-control.md)
- [네트워크 보안 (UFW 상세)](network-security.md)
