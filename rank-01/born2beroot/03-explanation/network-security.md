---
description: "NAT·포트 포워딩, SSH, UFW·iptables 의 기본 이론."
---

# 💡 Explanation — 네트워크 보안

> **문서 유형**: Explanation. Born2beRoot 의 네트워크 경계 구성.

## NAT 와 포트 포워딩

**NAT** (Network Address Translation) = 사설 ↔ 공용 IP 주소 변환.

VirtualBox **NAT 모드** 동작:
1. 게스트는 사설 IP `10.0.2.15` 를 받음 (가상 DHCP).
2. 호스트 안의 NAT 엔진이 가상 라우터 역할.
3. 게스트의 **outbound** (인터넷 접근) 는 호스트 네트워크로 자동 전달.
4. 외부 → 게스트 **inbound** 는 **포트 포워딩 규칙** 없이는 불가.

Born2beRoot 전형적 포트 포워딩:

| 호스트 포트 | 게스트 포트 | 용도 |
|---|---|---|
| 2222 | 4242 | SSH |
| 8080 | 80 | lighttpd (WordPress) |
| 19999 | 19999 | Netdata |

### 30 초 답변
"VirtualBox NAT 모드는 게스트를 사설 네트워크 뒤에 숨깁니다. 게스트가 외부로 나가는 통신은 자동 변환되지만, 외부에서 들어오는 건 호스트 포트 → 게스트 포트 매핑 규칙이 필요합니다. SSH 는 호스트 2222 → 게스트 4242 로 매핑됩니다."

### 더 깊이
- `"VirtualBox NAT vs Bridged Network"`
- `"iptables DNAT destination NAT"`
- `"NAT traversal techniques"`

---

## SSH (Secure Shell)

원격 셸 프로토콜, 기본 TCP 포트 **22**. Born2beRoot 는 **4242** 로 변경 (SC-08).

연결 절차:
1. **비대칭 키 교환** (Diffie-Hellman, ECDH) → 대칭 세션 키 합의
2. **대칭 암호** (AES, ChaCha20) 로 트래픽 암호화
3. **사용자 인증** (비번 / 공개키 / Kerberos)

서버 = `sshd` 데몬. 설정: `/etc/ssh/sshd_config`.

Born2beRoot 핵심 지시어:
- `Port 4242` — 표준 포트를 변경해 봇 공격 표면 축소
- `PermitRootLogin no` — root 직접 접속 금지, sudo 경유 강제

### 왜 포트를 바꾸는가
4242 변경은 **진짜 보안** 이라기보단 **노이즈 감소** — 인터넷 봇은 대부분 22 만 스캔. 실제 공격자는 `nmap` 으로 4242 도 찾음. 따라서 이건 **DoS 전 단계 브루트포스 감소** 용.

### 30 초 답변
"SSH 는 원격 셸을 암호화해 제공하는 프로토콜입니다. Diffie-Hellman 키 교환 → AES 대칭 암호화 → 사용자 인증 순으로 연결합니다. 우리는 포트를 22 에서 4242 로 바꾸고 root 로그인을 금지해 공격 표면을 축소합니다."

### 더 깊이
- `"SSH key exchange Diffie-Hellman"`
- `"SSH public key authentication ~/.ssh/authorized_keys"`
- `"sshd_config best practices"`

---

## UFW · iptables · netfilter

**netfilter** = Linux 커널의 패킷 필터링 프레임워크.
**iptables** = 유저 공간 프론트엔드, 체인(INPUT/OUTPUT/FORWARD) × 테이블(filter/nat/mangle/raw) 규칙 관리.
**UFW** (Uncomplicated Firewall) = iptables 위의 **추상 도우미**.

UFW 기본 정책 (Born2beRoot):
```
DEFAULT_INPUT_POLICY="DROP"   ← 들어오는 건 기본 거부
DEFAULT_OUTPUT_POLICY="ACCEPT"
```

= **화이트리스트 모델**. 명시적으로 허용한 포트(4242) 만 통과.

### UFW vs iptables
- 같은 커널 엔진을 조작. 결과물은 동일.
- UFW 가 사람이 읽기 쉬운 명령 (`ufw allow 4242`) 제공.
- 복잡한 규칙 (source IP 특정, rate limit) 은 iptables 가 더 세밀.

### 30 초 답변
"UFW 는 iptables/netfilter 위의 얇은 프론트엔드입니다. '기본 거부 + 명시 허용' 화이트리스트 모델로 설정합니다. 우리는 incoming 을 DROP 기본값으로 두고 4242 TCP 만 ALLOW 해, SSH 외 모든 연결을 차단합니다."

### 더 깊이
- `"iptables chain table explained"`
- `"UFW vs firewalld comparison"`
- `"nftables vs iptables"`

---

## 관련 문서

- [튜토리얼 E2·E4 (SSH + UFW)](../00-tutorial/install-your-first-vm.md#e2-ssh-포트-4242)
- [UFW 자기 잠김 복구](../01-how-to/recover-from-lockout.md#ufw-self-lockout)
- [설정 파일: sshd_config, UFW](../02-reference/config-files.md)
- [설계 결정 ADR-003 (UFW vs firewalld)](design-decisions.md#adr-003-ufw-over-firewalld)
