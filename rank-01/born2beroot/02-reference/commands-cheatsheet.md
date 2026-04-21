---
description: "Born2beRoot 평가·운영 중 가장 자주 쓰는 명령 한 줄 치트시트. 알파벳 순."
---

# 📚 Reference — 명령 치트시트

> **문서 유형**: Reference. 각 명령 한 줄 요약 + 전형적 출력 패턴. "왜" 는 Explanation 참조.

## A

| 명령 | 용도 |
|---|---|
| `/usr/sbin/aa-status` | AppArmor 로드 여부 + 프로필 수 |
| `adduser <name>` | 대화형 사용자 추가 (home, 기본 그룹 자동) |
| `apt update && apt upgrade -y` | 패키지 인덱스 갱신 + 설치본 업그레이드 |
| `apt install <pkg>` | 패키지 설치 |
| `apt purge <pkg>` | 설치본 + 설정 파일 완전 제거 |
| `apt list --installed` | 현재 설치된 패키지 |

## C

| 명령 | 용도 |
|---|---|
| `cat /etc/os-release` | 배포판 정체성 |
| `chage -l <user>` | 비번 만료 정책 조회 |
| `chage -M 30 -m 2 -W 7 <user>` | 기존 계정에 정책 강제 |
| `crontab -l` / `-e` / `-u root` | 사용자/루트 crontab 보기/편집 |
| `cryptsetup luksDump /dev/sda5` | LUKS 헤더 + 키슬롯 조회 |

## D

| 명령 | 용도 |
|---|---|
| `df -h` | 파일시스템별 사용량 |
| `dpkg -l \| grep <pat>` | 설치된 패키지 필터링 |
| `dpkg -L <pkg>` | 패키지의 설치 파일 목록 |

## F

| 명령 | 용도 |
|---|---|
| `free -h` | 메모리 사용량 (단위 자동) |

## G

| 명령 | 용도 |
|---|---|
| `getent passwd <user>` | `/etc/passwd` + NSS 소스 통합 조회 |
| `getent group <grp>` | 그룹 멤버 조회 |
| `groupadd <grp>` | 그룹 생성 |
| `groups <user>` | 사용자의 소속 그룹 전부 |

## H

| 명령 | 용도 |
|---|---|
| `hostname` | 현재 호스트명 |
| `hostnamectl` | 호스트명 + machine-id + 커널 |
| `hostnamectl set-hostname <new>` | 영구 변경 |

## I

| 명령 | 용도 |
|---|---|
| `id <user>` | uid/gid/groups 한 줄 |
| `ip a` / `ip addr` | 네트워크 인터페이스 + IP |
| `ip link show` | MAC 주소 확인 |

## J

| 명령 | 용도 |
|---|---|
| `journalctl -u ssh` | systemd 서비스 로그 |
| `journalctl -b` | 현재 부팅 이후 로그 |
| `journalctl _COMM=sudo` | sudo 호출 추적 |

## L

| 명령 | 용도 |
|---|---|
| `lsblk` | 블록 장치 트리 (파티션/crypt/LVM 한 눈) |
| `lsblk -f` | 파일시스템 + UUID + 마운트까지 |
| `lscpu` | CPU architecture, 물리/논리 프로세서 수 |
| `lvs` | LV 목록 + 크기 |
| `lvextend -L +1G /dev/<vg>/<lv>` | LV 확장 |
| `lvreduce -L -1G /dev/<vg>/<lv>` | LV 축소 (위험) |

## M

| 명령 | 용도 |
|---|---|
| `mount \| column -t` | 마운트 테이블 정렬 |

## N

| 명령 | 용도 |
|---|---|
| `nproc` | 논리 CPU 수 (vCPU) |

## P

| 명령 | 용도 |
|---|---|
| `passwd <user>` | 비번 변경 (pwquality 검증 포함) |
| `ps -ef \| grep <proc>` | 프로세스 검색 |
| `pvs` | Physical Volume 목록 |

## R

| 명령 | 용도 |
|---|---|
| `resize2fs /dev/<vg>/<lv>` | ext4 파일시스템 크기를 LV 에 맞게 조정 |

## S

| 명령 | 용도 |
|---|---|
| `sha1sum <file>` | SHA-1 해시 (Subject 서명용) |
| `ss -tunlp` | 열려 있는 TCP/UDP 포트 + 프로세스 |
| `ss -tunlp \| grep 4242` | SSH 리스닝 확인 |
| `sudo -l` | 현재 사용자의 sudo 권한 요약 |
| `sudo -k && sudo ls` | sudo 타임스탬프 초기화 후 재인증 테스트 |
| `sudo visudo -c` | sudoers 문법 검증 (편집 금지) |
| `systemctl status <svc>` | 서비스 상태 + 최근 로그 |
| `systemctl is-active <svc>` | `active` 인지 한 단어로 |
| `systemctl is-enabled <svc>` | 자동 시작 여부 |

## T

| 명령 | 용도 |
|---|---|
| `top` / `htop` | CPU/메모리 실시간 |

## U

| 명령 | 용도 |
|---|---|
| `/usr/sbin/ufw status verbose` | UFW 전체 상태 |
| `/usr/sbin/ufw status numbered` | 룰 번호 포함 |
| `/usr/sbin/ufw allow 4242/tcp` | 포트 허용 |
| `/usr/sbin/ufw delete allow 4242/tcp` | 룰 삭제 (또는 번호로) |
| `/usr/sbin/ufw reset` | 전체 초기화 |
| `uname -a` | 커널 + 아키텍처 전체 |
| `uptime -s` | 마지막 부팅 시각 |
| `usermod -aG <grp> <user>` | 보조 그룹 추가 (**-a 필수**, 없으면 기존 그룹 삭제됨) |

## V

| 명령 | 용도 |
|---|---|
| `vgs` | Volume Group 목록 |

## W

| 명령 | 용도 |
|---|---|
| `w` | 로그인 사용자 + 활동 |
| `wall "<msg>"` | 활성 TTY 에 브로드캐스트 |
| `who` | 로그인 사용자 목록 |
| `who -b` | 마지막 부팅 시각 |
| `who \| wc -l` | 현재 로그인 세션 수 |

---

## monitoring.sh 에서 자주 쓰는 한 줄 조합

```bash
# Architecture + kernel
uname -a

# Physical CPUs
grep "physical id" /proc/cpuinfo | sort -u | wc -l

# Virtual CPUs
grep -c "processor" /proc/cpuinfo

# RAM 사용률
free -m | awk 'NR==2 {printf "%s/%sMB (%.2f%%)\n",$3,$2,$3*100/$2}'

# Disk 사용률
df -h --total | awk '/^total/ {print $3"/"$2" ("$5")"}'

# CPU %
top -bn1 | grep "Cpu(s)" | awk '{print $2+$4"%"}'

# Last boot
who -b | awk '{print $3" "$4}'

# LVM 사용 여부
if [ $(lsblk | grep -c lvm) -gt 0 ]; then echo yes; else echo no; fi

# Active TCP 연결
ss -t | grep -c ESTAB

# 로그인 사용자 수
who | wc -l

# IPv4 + MAC
hostname -I | awk '{print $1}'
ip link show | awk '/ether/ {print $2; exit}'

# sudo 명령 실행 횟수
journalctl _COMM=sudo | grep -c COMMAND
```

## 관련 문서

- [설정 파일 스펙](config-files.md)
- [Subject v5.2 조항](subject-v5.2-clauses.md)
- [설명: 모니터링](../03-explanation/monitoring-integrity.md)
