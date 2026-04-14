# 📠 Virtual Machine 설치하기 (상세 보충 가이드 v5.2)

본 문서는 다음 메인 가이드를 따라가면서 부족할 수 있는 하드웨어 설정부터 상세한 설명을 보충하기 위해 작성되었습니다.

{% embed url="https://github.com/NoelClay/42-born2beroot_guide/blob/claude/add-korean-translation-7JHtm/README_KR.md" %}
NoelClay의 Born2BeRoot 한국어 번역 가이드
{% endembed %}

---

## Part 0 - OS 및 하드웨어 결정의 기술적 근거

### 1. 리눅스 배포판 선택: 왜 Debian인가?
많은 엔터프라이즈 환경에서 Rocky Linux(RHEL 계열)를 사용하지만, 본 프로젝트에서 Debian을 선택하는 이유는 다음과 같습니다.

*   **보안 모듈의 관리 용이성 (AppArmor vs SELinux):** 
    Debian은 경로 기반의 **AppArmor**를 기본 MAC으로 사용합니다. Rocky의 **SELinux**는 객체 라벨링 기반으로 제어가 정교하지만 설정이 매우 복잡하여 학습 단계에서 시스템 전체를 불능 상태로 만들 위험이 큽니다 [[1]](https://www.techtarget.com/searchsecurity/tip/SELinux-vs-AppArmor-How-do-they-compare).
*   **미니멀리즘과 제어권:** 
    Debian 'netinst'는 최소한의 패키지만 설치하여 공격 표면(Attack Surface)을 낮출 수 있어 "직접 구축하며 배우는" 취지에 부합합니다 [[2]](https://www.debian.org/intro/about).

> **[주석 1]** "AppArmor is path-based and generally easier for administrators to manage... SELinux has a steeper learning curve." ([TechTarget](https://www.techtarget.com/searchsecurity/tip/SELinux-vs-AppArmor-How-do-they-compare))
> **[주석 2]** "Debian is famous for its 'netinst', offering a smaller attack surface." ([Debian.org](https://www.debian.org/intro/about))

### 2. 버전 선택의 딜레마: Debian 13 (Trixie)의 wall 명령어 결함 분석
현재 Debian 13 최신 버전(v13.2)을 사용할 경우, 서브젝트 필수 요구사항인 `wall` 명령어가 SSH 세션(pts)으로 전달되지 않는 중대한 결함이 발생합니다.

*   **결함의 원인: CVE-2024-28085 (WallEscape) 패치 [[3]](https://42born2code.slack.com/archives/CN9RHKQHW/p1772141416890309)**
    최근 `util-linux` 패키지(2.40-2 이상)에서 `wall` 명령어가 타 사용자의 터미널에 이스케이프 시퀀스를 보내는 취약점이 발견되었습니다. 이를 해결하기 위해 Debian 13은 **`wall` 바이너리에 부여되었던 `setgid tty` 권한을 박탈**했습니다 [[4]](https://metadata.ftp-master.debian.org/changelogs/main/u/util-linux/unstable_changelog).
*   **기술적 결과:** 
    권한 박탈로 인해 `wall`은 더 이상 SSH를 통해 접속한 사용자의 터미널(`dev/pts/X`)에 쓸 수 없게 되었으며, 실행 시 `Permission denied` 에러를 출력합니다 [[5]](https://asec.ahnlab.com/en/79523/).
*   **평가 대응 전략:** 
    1. **안전한 선택:** 기능을 완벽히 구현하기 위해 검증된 **Debian 12 (Bookworm)**를 사용합니다. (평가 시 "최신 버전인 13의 보안 패치로 인해 wall 기능이 마비되었음을 인지하고 있으며, 기능 구현을 위해 12를 택했다"고 논리적으로 방어)
    2. **도전적 선택:** 13을 사용하되, `wall`이 작동하지 않는 이유를 위 CVE 번호를 근거로 설명합니다.

> **[주석 3]** "Debian 13 wall no longer reaches SSH sessions (pts)... impossible to satisfy both 'Tool' and 'Purpose'." - **[namykim(본 문서 작성자)의 42Born2Code Slack 제안 원문](https://42born2code.slack.com/archives/CN9RHKQHW/p1772141416890309)**
> **[주석 4]** "wall: no longer install setgid tty" ([Debian Changelog - util-linux](https://metadata.ftp-master.debian.org/changelogs/main/u/util-linux/unstable_changelog))
> **[주석 5]** "Vulnerability in util-linux allows escape sequences... CVE-2024-28085." ([AhnLab ASEC](https://asec.ahnlab.com/en/79523/))

---

## Part 1 - 하드웨어 리소스 할당의 근본적 설계

리소스 할당은 **워크로드 계산**과 **하이퍼바이저의 스케줄링 메커니즘**에 근거해야 합니다.

### 1. 메모리(RAM) 계산 근거 (Total: 4.4GiB)
서비스별 실점유량과 가변 영역을 합산한 후, 장애 대응을 위한 **안전 계수(Safety Factor) 2배**를 적용한 결과입니다.

| 서비스 | 근거 데이터 (최소/권장 사양) | 합산 (MiB) |
| :--- | :--- | :--- |
| **OS (Kernel/Base)** | [Debian 12 최소 사양 (No Desktop)](https://www.debian.org/releases/stable/amd64/ch03s04.en.html) | 1,024 |
| **MySQL (InnoDB)** | [Buffer Pool(128)](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) + [Memory Use(max_connections)](https://dev.mysql.com/doc/refman/8.0/en/memory-use.html) | 860 |
| **PHP-FPM** | [memory_limit](https://www.php.net/manual/en/ini.core.php) * [pm.max_children(5)](https://github.com/php/php-src/blob/master/sapi/fpm/www.conf.in) | 640 |
| **Netdata** | [Agent Resource Utilization](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram) | 200 |
| **결합 합산** | (1024 + 860 + 640 + 200) * 2 | **~4,448 (4.4GiB)** |

### 2. 스토리지(HDD) 계산 및 설정 (Total: 20GiB)
*   **계산:** [OS(4GB)](https://www.debian.org/releases/stable/amd64/ch03s04.en.html) + [PHP binaries(365MB)](https://github.com/docker-library/php/issues/297) + [MySQL DB 데이터(Redo log 포함)](https://dev.mysql.com/blog-archive/dynamic-innodb-redo-log-in-mysql-80/) 등 실사용량 ~9.5GB 확보 후, 안전 계수 2배 적용 시 약 **19~20GiB**가 도출됩니다.
*   **Pre-allocate Full Size 체크 이유:** 동적 할당은 파일 시스템 단편화와 쓰기 시 호스트 OS에 공간을 요청하는 오버헤드를 유발합니다. **Pre-allocate(고정 크기)**는 물리 공간을 선점하여 I/O 성능을 최적화합니다.

### 3. vCPU 할당 전략 (2 vCPU)
*   **CPU Co-scheduling 병목:** VM은 물리 CPU(pCPU)가 실제로 비어 있을 때만 스케줄링될 수 있습니다 [[6]](https://www.virtualbox.org/manual/ch03.html#settings-processor). vCPU를 너무 많이 할당하면 하이퍼바이저가 여러 pCPU가 동시에 비기를 기다리는 'Wait Time'이 급증하여 시스템이 오히려 멍청해집니다 [[7]](https://lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/).
*   **PHP Worker 최적화:** [PHP Is Single-Threaded](https://instawp.com/php-workers-vs-cpu-cores/)이므로, 설정한 5개의 PHP Worker가 원활히 동작하려면 물리 환경 부하를 고려한 **2 vCPU** 할당이 가장 합리적인 타협점입니다 [[8]](https://www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/).

> **[주석 6]** [VirtualBox Manual Ch. 3](https://www.virtualbox.org/manual/ch03.html#settings-processor)
> **[주석 7]** [Why my two vCPU VM is slow](https://lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/)
> **[주석 8]** [More or less vCPUs can make your VM slower](https://www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/)

---

이제 이 하드웨어 설계를 바탕으로 VirtualBox 설정을 진행하십시오. 가상 머신은 단순한 소프트웨어가 아니라, **엄밀하게 계산된 가상 하드웨어 시스템**입니다.
