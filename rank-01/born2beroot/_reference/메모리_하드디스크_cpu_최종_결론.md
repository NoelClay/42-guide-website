# 🖥️ LEMP + Netdata 서버 하드웨어 자원 산출 내역서

## 1\. 메모리 (RAM) 요구량 산출

시스템이 멈추지 않고 최악의 동시 접속(151개)을 처리하기 위한 기본 메모리 한계점(Bottleneck) 계산입니다.

| 컴포넌트 | 기본 할당량 | 산출 근거 및 상세 내역 | 공식 출처 |
| :--- | :--- | :--- | :--- |
| **OS** | 1,024MB | 모니터링 쉘 등 기본 데몬 구동 가정 권장 사양 | Debian 12 3.4. Meeting Minimum Hardware |
| **MySQL** | 430MB | **128MB** (InnoDB Buffer Pool) <br> + **302MB** (151 Connections × 2MB Complex Queries) | MySQL 8.0 Ref Manual (15.8.3.1 & 5.1.12.1)<br>MariaDB Memory Optimization Guide |
| **PHP** | 640MB | **128MB** (memory\_limit) × **5명** (pm.max\_children) | php.net ini.core.php<br>php-src/sapi/fpm/www.conf.in |
| **Netdata** | 200MB | 메트릭 수집량에 따른 기본 에이전트 점유율 (100\~200MB) | Netdata Docs (Resource Utilization/RAM) |
| **Lighttpd** | 0MB | 비동기 처리로 메모리 사용량 극소 (무시 가능 수준) | - |
| **WordPress**| 0MB | PHP 프로세스 내부에서 구동되므로 PHP 한도(640MB)에 종속됨 | - |

> 📊 **RAM 최종 산출 결론**
>
>   * **순수 요구량 합계:** 1GB + 430MB + 640MB + 200MB = **약 2,270MB (\~2.2GB)**
>   * **안전빵 계수 (×2):** **4,540MB (\~4.4GB)**
>   * **최종 결정:** 넉넉하게 **4GB \~ 5GB RAM** 할당

-----

## 2\. 하드디스크 (HDD) 요구량 산출

각 모듈의 설치 패키지 용량과 동작 시 발생하는 가변 영역(캐시/로그), 그리고 예상 DB 누적 데이터를 합산한 결과입니다.

| 컴포넌트 | 요구 용량 | 산출 근거 및 상세 내역 | 공식 출처 |
| :--- | :--- | :--- | :--- |
| **OS** | 4,096MB | 데비안 텍스트 기반(No desktop) 최소 권장 설치 용량 | Debian 12 3.4. Meeting Minimum Hardware |
| **Lighttpd** | 51MB | **1MB** (Installed Size 패키지) <br> + **50MB** (메모리 절약을 위한 디스크 임시 파일 가변 버퍼) | Debian Packages Tracker<br>Lighttpd Wiki (Docs ResourceTuning) |
| **PHP** | 365MB | 데비안 환경 최소 종속성 포함 바이너리 용량 | Docker Official Library (PHP Issue \#297) |
| **MySQL** | 2,000MB | **100MB** (설치 패키지) <br> + **100MB** (Redo Log 기본 할당량) <br> + **1,800MB** (300명 × [300자 텍스트 + 2MB 이미지 3장]) | MySQL Router 8.0 Installation<br>MySQL Blog (Dynamic Redo Log) |
| **WordPress**| 100MB | 기본 코어 설치(65MB) + 테마 및 플러그인(20MB+) 여유분 | WP Support Forum (Minimum requirements) |
| **Netdata** | 3,072MB | **1GB** × **3개 티어** (초/분/시 단위 병렬 다운샘플링 기본값) | Netdata Docs (Welcome / Default Disk Footprint) |

> 💾 **HDD 최종 산출 결론**
>
>   * **순수 요구량 합계:** 4GB + 51MB + 365MB + 2,000MB + 100MB + 3,072MB = **약 9,516MB (\~9.5GB)**
>   * **안전빵 계수 (×2):** **19,032MB (\~19GB)**
>   * **최종 결정:** 넉넉하게 **19GB \~ 20GB HDD** 할당

-----

### References

**[1] Debian Documentation.** *3.4. Meeting Minimum Hardware Requirements.* * **URL:** [https://www.debian.org/releases/stable/amd64/ch03s04.en.html](https://www.debian.org/releases/stable/amd64/ch03s04.en.html)
* **근거:** "Install Type: No desktop, RAM: 1GB, Hard Drive: 4GB"

**[2] MySQL 8.0 Reference Manual.** *15.8.3.1 Configurable InnoDB Read-Only Variables.*
* **URL:** [https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)
* **근거:** "Default Value: 134217728 bytes (128 MiB)"

**[3] MySQL 8.0 Reference Manual.** *5.1.12.1 How MySQL Uses Memory.*
* **URL:** [https://dev.mysql.com/doc/refman/8.0/en/memory-use.html](https://dev.mysql.com/doc/refman/8.0/en/memory-use.html)
* **근거:** "max_connections: Default 151"

**[4] MariaDB Knowledge Base.** *Memory Optimization Guide.*
* **URL:** [https://mariadb.com/kb/en/memory-optimization-guide/](https://mariadb.com/kb/en/memory-optimization-guide/)
* **근거:** "A connection uses around 1MB to 2MB of memory at its peak performance depending on the complexity of queries."

**[5] PHP Manual.** *Description of core php.ini directives.*
* **URL:** [https://www.php.net/manual/en/ini.core.php](https://www.php.net/manual/en/ini.core.php)
* **근거:** "memory_limit int: This sets the maximum amount of memory in bytes that a script is allowed to allocate."

**[6] PHP Source Code.** *sapi/fpm/www.conf.in.*
* **URL:** [https://github.com/php/php-src/blob/master/sapi/fpm/www.conf.in](https://github.com/php/php-src/blob/master/sapi/fpm/www.conf.in)
* **근거:** "pm.max_children = 5"

**[7] Netdata Docs.** *Resource utilization - RAM.*
* **URL:** [https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram)
* **근거:** "Netdata by default should need 100MB to 200MB of RAM, depending on the number of metrics"

**[8] Debian Package Tracker.** *Package: lighttpd.*
* **URL:** [https://packages.debian.org/bookworm/lighttpd](https://packages.debian.org/bookworm/lighttpd)
* **근거:** "Installed Size: 975.0 kB"

**[9] Lighttpd Wiki.** *Docs ResourceTuning.*
* **URL:** [https://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ResourceTuning](https://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ResourceTuning)
* **근거:** "For requests and responses over a certain size, lighttpd uses temporary files on disk to store the request and response bodies, limiting memory use."

**[10] Docker Library PHP.** *Issue #297: Base image size.*
* **URL:** [https://github.com/docker-library/php/issues/297](https://github.com/docker-library/php/issues/297)
* **근거:** "So the ~365MB we're at is likely the smallest we can go for Debian variants"

**[11] MySQL 8.0 Reference Manual.** *Chapter 2 Installing MySQL Router.*
* **URL:** [https://dev.mysql.com/doc/mysql-router/8.0/en/mysql-router-installation.html](https://dev.mysql.com/doc/mysql-router/8.0/en/mysql-router-installation.html)
* **근거:** "Disk Space: Minimum requirement is 100 MB."

**[12] MySQL Blog.** *Dynamic InnoDB Redo Log in MySQL 8.0.*
* **URL:** [https://dev.mysql.com/blog-archive/dynamic-innodb-redo-log-in-mysql-80/](https://dev.mysql.com/blog-archive/dynamic-innodb-redo-log-in-mysql-80/)
* **근거:** "The new redo log size is defined by one single variable: innodb_redo_log_capacity (in bytes). The default is 100MB."

**[13] WordPress Support Forum.** *Minimum hosting requirement for WP 6.2.2.*
* **URL:** [https://wordpress.org/support/topic/minimum-hosting-requirement-for-wp-6-2-2/](https://wordpress.org/support/topic/minimum-hosting-requirement-for-wp-6-2-2/)
* **근거:** "I just created a WP fresh installation... basic installation takes around 65 MB... A theme may take around 20 MB more."

**[14] Netdata Docs.** *Welcome to Netdata.*
* **URL:** [https://learn.netdata.cloud/docs/welcome-to-netdata](https://learn.netdata.cloud/docs/welcome-to-netdata)
* **근거:** "Three storage tiers are updated in parallel (per-second, per-minute, per-hour)."

**[15] Netdata Docs.** *Disk Requirements & Retention.*
* **URL:** [https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/disk-&-retention](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/disk-&-retention)
* **근거:** 표 Default Disk Footprint 명시: "tier0: 1 GiB, tier1: 1 GiB, tier2: 1 GiB"