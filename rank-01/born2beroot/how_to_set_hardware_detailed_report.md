# [심화 기술 보고서] Born2beRoot 프로젝트의 하드웨어 리소스 최적화 설계 및 실증적 분석

**작성자:** namykim (42 Gyeongsan)  
**대상 프로젝트:** Born2beRoot (42 Curriculum)  

---

## 1. 하드웨어 설계의 필연성: 불투명한 수치에 대한 의문

본 프로젝트의 시작 단계에서 마주하는 가장 큰 난관은 서브젝트(Subject.pdf)가 제시하는 리소스 할당 수치의 불투명성입니다. 단순히 가이드라인에 있는 "최소 사양"을 맹목적으로 입력하는 행위는 시스템 엔지니어링의 본질인 **예측 가능성(Predictability)**을 결여합니다.

본 보고서는 운영 체제와 애플리케이션 서비스가 실제로 점유하는 자원을 ((최선을 다해)) 정밀하게 산출하여, 안정적인 서버 운영을 위한 하드웨어 사양을 확정하는 과정을 기술합니다. 왜 우리가 이 수치를 선택해야만 했는지, 운영 체제와 하이퍼바이저의 스케줄링 원리를 통해 그 타당성을 순차적으로 증명해 나갑니다. 실제 출처를 하나 하나 검색해보며 수치를 가져왔기 때문에 타당성을 이해하는데 도움이 되는 자료가 되길 바랍니다.

---

## 2. 메모리(RAM) 리소스 산출 공정: 이론과 실제의 간극 메우기

서버의 가용성은 가용 메모리의 양에 직결됩니다. 본 분석에서는 터지지 않는 "개빵빵한 안전빵" 환경을 구축하기 위해, 운영 체제부터 모니터링 에이전트까지 각 계층(Layer)의 점유량을 역추적하여 합산하였습니다. 특히 데이터베이스와 애플리케이션 서버가 동시 접속자를 처리할 때 발생하는 '가변적 메모리'를 계산하는 데 집중하였습니다.

### 2.1 계층별 자원 조사 과정 및 근거
하드웨어 조사는 각 서비스가 피크(Peak) 상태일 때 어느 정도의 메모리를 소비하는지 확인하는 것부터 시작되었습니다.

1.  **OS 계층 (Debian 12):** 
    모니터링 스크립트 등 기본적인 데몬이 모두 돌아가는 환경을 가정했습니다.
    *   > *"For a typical installation (no desktop environment), the minimum memory requirement is 1GB."* — [[Debian 12 Manual Ch. 3.4]](https://www.debian.org/releases/stable/amd64/ch03s04.en.html)
2.  **데이터베이스 계층 (MySQL 8.0):** 
    가장 고민이 많았던 영역입니다. MySQL은 데이터 캐싱을 위한 고정 기본 용량(128MiB) 외에도 연결 수(`max_connections`)에 따라 가변적인 메모리를 소모합니다.
    *   **고정 영역:** > *"Default Value: 134217728 bytes (128 MiB)"* — [[MySQL 8.0 Reference: innodb_buffer_pool_size]](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)
    *   **가변 영역 (연결당 소모량):** 기본 최대 연결 수는 151개입니다 [[max_connections]](https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_max_connections). 그렇다면 연결 1개당 얼만큼의 메모리를 잡아야 할까요? 복잡한 쿼리(정렬, 읽기 등) 수행 시 개별 세션 버퍼가 동적 할당되어 연결당 피크 시 약 1~2MB의 메모리를 점유한다는 점을 확인했습니다 [[Percona Blog: Understanding MySQL Memory Usage]](https://www.percona.com/blog/understanding-mysql-memory-usage-with-performance-schema/). 
    *   **고찰:** 최악의 경우를 가정하여 151개 연결에 2MB씩 곱해 **302MB**를 가변 영역으로 산출했습니다. 물론 스택오버플로우의 분석처럼 실제로는 이렇게 모든 연결이 동시에 최대 메모리를 점유하고 있지 않으며, 작업 후 빠르게 해소되어 0MB에 가깝게 운용됩니다 [[StackOverflow]](https://stackoverflow.com/questions/65780309/mysql-per-thread-memory-variables-that-lead-to-thread-memory-usage/65781074#65781074). 하지만 최악의 병목을 막기 위해 보수적으로 계산했습니다.
    *   **소계:** 128MB + 302MB = 430MB.
3.  **애플리케이션 계층 (PHP-FPM):** 
    동시에 실행되는 PHP 프로세스 수(`pm.max_children`)와 개별 프로세스가 점유 가능한 최대 메모리의 곱으로 계산함.
    *   > *"This sets the maximum amount of memory in bytes that a script is allowed to allocate."* — [[PHP Manual: memory_limit (128MB)]](https://www.php.net/manual/en/ini.core.php)
    *   > *"pm.max_children = 5"* — [[PHP-FPM Config: www.conf.in]](https://github.com/php/php-src/blob/master/sapi/fpm/www.conf.in)
    *   **소계:** 128MB × 5 = 640MB. (WordPress는 PHP 위에서 돌아가므로 이 용량에 종속됩니다.)
4.  **모니터링 계층 (Netdata):** 
    실시간 지표 수집량에 따른 에이전트 오버헤드를 측정함.
    *   > *"Netdata by default should need 100MB to 200MB of RAM..."* — [[Netdata Agent Utilization]](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram)

### 2.2 결정의 근거: "안전빵" 2.0배수의 도입
이론적 합산값은 1GB + 430MB + 640MB + 200MB = **약 2,270MB (~2.27GB)**입니다. 하지만 시스템이 절대 터지지 않게 충분한 자원을 주기 위해 여기에 **2배수**를 곱했습니다.
*   **최종 결정:** ~4.54GB. 따라서 넉넉하게 **4GB ~ 5GB RAM**을 할당하는 것이 근거가 확보된 안전한 용량입니다.

---

## 3. 스토리지(HDD) 용량 설계: 성능과 수명의 최적점

각 모듈의 설치 용량과 가변 영역, 그리고 유저 데이터를 합산했습니다.

### 3.1 계층별 자원 조사 과정 및 근거
1.  **OS:** 최소 2GB지만 넉넉하게 **4,000MB (4GB)** 할당 [[Debian Manual]](https://www.debian.org/releases/stable/amd64/ch03s04.en.html).
2.  **Lighttpd:** 패키지 설치 크기 1MB [[Debian Packages]](https://packages.debian.org/bookworm/lighttpd). 여기에 5명의 PHP 워커가 한 번에 10MB씩 디스크 임시 파일 가변 버퍼를 쓸 상황을 대비해 50MB 추가 [[Lighttpd ResourceTuning]](https://redmine.lighttpd.net/projects/lighttpd/wiki/Docs_ResourceTuning). (소계 51MB)
3.  **PHP:** 데비안 공식 패키지는 작지만 도커 등 의존성을 포함한 안전치로 **365MB** 산정 [[PHP Base image size]](https://github.com/docker-library/php/issues/297).
4.  **MySQL & User Data (고찰의 핵심):** 
    *   **MySQL 패키지:** 대략 **355MB** 추정 [[설치 레퍼런스]](https://postil.tistory.com/entry/MySQL-%EC%84%A4%EC%B9%98).
    *   **Redo Log:** > *"The default is 100MB."* [[MySQL Redo Log]](https://dev.mysql.com/doc/refman/8.0/en/innodb-redo-log.html)
    *   **유저 데이터 고찰:** 300명 접속 기준을 상정함. 여기서 구조를 정확히 이해해야 함. 한글 300자 텍스트는 MySQL DB 영역에 저장되나, 2MB 이미지 3장은 워드프레스의 `uploads` 폴더 디스크에 파일로 저장됨. 이 둘을 합산한 실제 여유분으로 **1,800MB**를 산정함.
    *   **소계:** 355MB + 100MB + 1800MB = 2,255MB.
5.  **WordPress:** 기본 설치(65MB)와 테마(20MB) 여유를 포함해 **100MB** [[WP Support]](https://wordpress.org/support/topic/minimum-hosting-requirement-for-wp-6-2-2/).
6.  **Netdata:** 3개 스토리지 티어가 병렬로 업데이트되며 각각 1GB씩 필요함 [[Netdata Welcome]](https://learn.netdata.cloud/docs/welcome-to-netdata), [[Disk Footprint]](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/disk-&-retention). (소계 3,000MB)

### 3.2 결정: 최종 합산 및 Pre-allocate의 당위성
순수 요구량 합산은 9,771MB입니다. 여기에 2배수를 적용하여 **19,542MB (~19.54GB)**가 도출되었습니다. 

따라서 **20GB**를 할당하되, 반드시 **"Pre-allocate Full Size"**를 체크해야 합니다. 동적으로 할당하면 디스크를 쓸 때마다 호스트에 공간을 요청하게 되어 I/O 병목이 발생하기 때문입니다.

---

## 4. vCPU 할당 전략: 하이퍼바이저와 OS의 '진실 게임'

자원 조사 중 가장 많이 사고를 뜯어고친 부분입니다. GUI 창에서 32개까지 코어를 매길 수 있으니, "초과 할당만 아니면 코어를 많이 줄수록 무조건 좋은 거 아닌가?"라고 생각했습니다. 하지만 이는 시스템 아키텍처를 오해한 것이었습니다.

### 4.1 하이퍼바이저의 스케줄링 메커니즘 (SMP)
가상 머신 환경은 대칭형 다중 처리(SMP)를 지원하여 여러 프로세서가 시분할로 작업을 수행하도록 돕습니다 [[SMP Wikipedia]](https://en.wikipedia.org/wiki/Symmetric_multiprocessing).
> *"Processor(s): Sets the number of virtual CPU cores the guest OSes can see... You should not configure virtual machines to use more CPU cores than are available physically."* — [[VirtualBox Manual Ch. 3]](https://www.virtualbox.org/manual/ch03.html#settings-processor)

### 4.2 하이퍼바이저는 진실만을 전달해야 한다 (Co-scheduling 병목)
이해하기 쉽게 비유하자면, VM은 OS를 속이는 장치입니다. 메모리는 8기가인데 64기가인 척 가짜로 속일 수 있습니다. 디스크 공간도 속일 수 있습니다. 하지만 **CPU는 가짜로 대체시킬 장치가 없습니다.** 

OS의 스케줄링은 자신이 물리적으로 가진 CPU 한도 내에서 일어납니다. 만약 VM에게 "너는 2코어 4스레드를 가지고 있어"라고 속였다면, 하이퍼바이저는 실제로 물리적(pCPU)으로 2코어 4스레드의 여유 공간이 동시에 날 때까지 기다려야 합니다. 

만약 물리적 여유가 1.5코어밖에 안 난다면 어떻게 될까요? VM은 알잘딱하게 1코어만 쓰는 게 아니라, **정확히 2코어 4스레드의 자원이 완전히 비워질 때까지 멍청하게 기다립니다(Wait Time).** 성능을 높이려고 vCPU를 늘렸다가 오히려 시스템을 멍청이로 만들어버리는 완벽한 병목입니다 [[Why my two vCPU VM is slow]](https://lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/), [[More or less vCPUs can make your VM slower]](https://www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/).

### 4.3 최적의 타협점: PHP Worker와의 연동 (3 vCPU 결정)
세팅하려는 환경에서 가장 큰 병목이 일어나는 시나리오는 '미친 트래픽 몰림'입니다. 이를 처리하기 위해 PHP Worker 5개를 배치할 예정입니다. 
그렇다면 워커 5개에 맞춰 vCPU도 5개를 줘야 할까요? [PHP Is Single-Threaded](https://instawp.com/php-workers-vs-cpu-cores/)이므로 코어 수와 워커 수를 맞추는 것이 이상적이지만, 5개는 물리적 환경을 고려할 때 오버헤드가 우려됩니다.

*   **최종 결정:** PHP 워커 5개의 부하와 OS 및 다른 작업들이 돌아가게 하기 위한 여유를 고려하여(필요 코어 수 6개 상정), 이를 절반으로 나눈 **3 vCPU**를 할당하여 타협점을 찾기로 결정했습니다.

---

## 5. 참고 문헌 및 인용 근거 (References)

*   **Debian 12 Minimum Hardware:** [www.debian.org/releases/stable/amd64/ch03s04.en.html](https://www.debian.org/releases/stable/amd64/ch03s04.en.html)
*   **MySQL InnoDB Buffer Pool Size:** [dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)
*   **MySQL max_connections:** [dev.mysql.com/doc/refman/8.4/en/server-system-variables.html](https://dev.mysql.com/doc/refman/8.4/en/server-system-variables.html#sysvar_max_connections)
*   **Percona Understanding MySQL Memory:** [www.percona.com/blog/understanding-mysql-memory-usage-with-performance-schema/](https://www.percona.com/blog/understanding-mysql-memory-usage-with-performance-schema/)
*   **StackOverflow MySQL Thread Memory:** [stackoverflow.com/questions/65780309/mysql-per-thread-memory-variables-that-lead-to-thread-memory-usage/65781074#65781074](https://stackoverflow.com/questions/65780309/mysql-per-thread-memory-variables-that-lead-to-thread-memory-usage/65781074#65781074)
*   **PHP memory_limit Directive:** [www.php.net/manual/en/ini.core.php](https://www.php.net/manual/en/ini.core.php)
*   **Netdata Agent Utilization:** [learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram](https://learn.netdata.cloud/docs/netdata-agent/resource-utilization/ram)
*   **MySQL Router Installation:** [postil.tistory.com/entry/MySQL-%EC%84%A4%EC%B9%98](https://postil.tistory.com/entry/MySQL-%EC%84%A4%EC%B9%98)
*   **VirtualBox Processor Settings:** [www.virtualbox.org/manual/ch03.html#settings-processor](https://www.virtualbox.org/manual/ch03.html#settings-processor)
*   **CPU Co-scheduling Analysis:** [lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/](https://lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/)
*   **More or less vCPUs can make your VM slower:** [www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/](https://www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/)
