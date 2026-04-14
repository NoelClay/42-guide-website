# 📠 P2P 평가 - 질문 리스트

{% hint style="danger" %}
**주의:** 평가자는 가장 먼저 학생의 git 저장소에 가상 머신(VM)의 **서명(signature)** 파일만 존재하며, 그 외 다른 파일은 없는지 확인해야 합니다. 이 서명은 평가 시점의 VM 상태와 완벽히 동일해야 합니다 (만약 이후에 VM 내부를 변경했다면 서명 값이 달라집니다).
{% endhint %}

## 이론 질문 (Theoretical questions)

평가 중에 다음과 같은 **이론적인 질문**을 받게 됩니다.

* **가상 머신(Virtual Machine)**은 어떻게 작동합니까? 그리고 그 목적은 무엇입니까?
* **Debian** 또는 **CentOS** 중 무엇을 선택했으며, 그 이유는 무엇입니까?
* **Debian**과 **CentOS**의 차이점은 무엇입니까?
* **Debian**을 선택한 경우: `aptitude`와 `apt`의 차이점은 무엇이며, **AppArmor**란 무엇입니까?
* **CentOS**를 선택한 경우: **SELinux**와 **DNF**란 무엇입니까?


* 강력한 **비밀번호 정책(Password Policy)**의 장단점은 무엇입니까? 구현에 대해 설명해 보십시오.
* **파티션(Partition)**이란 무엇입니까? 더 포괄적으로 **LVM(Logical Volume Management)**은 어떻게 작동합니까?
* **sudo**란 무엇입니까?
* **UFW**란 무엇이며, 이것을 사용하는 가치는 무엇입니까?
* **SSH(Secure Shell)**란 무엇이며, 이것을 사용하는 가치는 무엇입니까?
* **cron**이란 무엇입니까?

모든 내용(필요한 것 이상으로)은 다음 링크에 설명되어 있습니다.

{% content-ref url="https://www.google.com/search?q=whats-a-virtual-machine.md" %}
[whats-a-virtual-machine.md]()
{% endcontent-ref %}

{% content-ref url="https://www.google.com/search?q=install-your-virtual-machine.md" %}
[install-your-virtual-machine.md]()
{% endcontent-ref %}

---

## 실습 질문 (Practical questions)

다음은 시연해야 할 **실습 질문**들과 답변 확인을 위해 입력해야 하는 명령어들입니다.

* 저장소에 포함된 서명이 평가할 가상 머신의 `.vdi` 파일과 동일한지 확인하십시오.
1. iTerm(또는 터미널)을 열고 `cd`를 입력합니다.
2. `cd sgoinfre/students/<인트라_ID>/<VM 파일 위치>`를 입력하여 이동합니다.
3. `shasum VirtualBox.vdi` (또는 해당 VM의 .vdi 파일명)를 입력하여 해시값을 확인합니다.


* 평가 진행 중, 스크립트가 **10분마다** 정보를 화면에 표시해야 합니다.

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption><p>10분마다 나타나야 하는 스크립트 화면 예시</p></figcaption></figure>

* 머신을 실행할 때 **그래픽 환경(GUI)**이 뜨지 않아야 합니다.
* 이 머신에 연결을 시도하기 전에 **비밀번호**를 요청해야 합니다.
* 사용자 계정으로 로그인해야 합니다. 이때 **루트(root) 계정이 아니어야** 합니다.
* **UFW 서비스**가 시작되었는지 확인하십시오.
* `sudo ufw status`


* **SSH 서비스**가 시작되었는지 확인하십시오.
* `sudo systemctl status ssh`



<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>SSH 서비스 상태 확인 예시</p></figcaption></figure>

* 운영체제가 **Debian** 또는 **CentOS**인지 확인하십시오 (10분마다 표시되는 정보 스크립트에서도 확인할 수 있습니다).
* 본인의 로그인 ID를 가진 사용자가 존재하며 VM에 있는지 확인하십시오. 이 사용자는 "sudo" 및 "user42" 그룹에 속해 있어야 합니다.
* `getent group sudo`
* `getent group user42`


<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
* 과제(Subject)에서 부과된 **비밀번호 정책** 규칙이 제대로 적용되었는지 다음 단계를 통해 확인하십시오.
* 평가자를 위한 새 사용자 생성: `sudo adduser <새_사용자명>`
* 평가자가 직접 비밀번호를 선택하여 설정하게 합니다.
* "evaluating"이라는 이름의 새 그룹 생성: `sudo groupadd <그룹명>`
* 새 사용자(평가자 계정)를 해당 그룹에 할당: `sudo usermod -aG <그룹명> <사용자명>`
* 비밀번호 만료 규칙 확인: `sudo chage -l <사용자명>`


<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption><p>비밀번호 규칙은 이와 비슷하게 보여야 합니다</p></figcaption></figure>
* 이 새 사용자가 "evaluating" 그룹에 속해 있는지 확인하십시오: `getent group evaluating`
* 머신의 **호스트네임(Hostname)**이 다음 형식으로 올바르게 지정되었는지 확인하십시오: `login42` (본인ID42)
* `hostnamectl`


* 호스트네임을 본인의 로그인 ID로 변경하고 머신을 재시작하십시오 (재부팅 후 새 호스트네임이 적용되어야 합니다).
* `hostnamectl set-hostname <새_호스트네임>`
* 다음 파일에서도 호스트네임을 변경하십시오: `sudo nano /etc/hosts`
* 가상 머신 재시작


* 머신을 원래 이름으로 복구하십시오 (위와 동일한 과정을 수행).
* VM의 **파티션**을 확인하십시오 (출력 결과를 과제 예시와 비교).
* `lsblk`


* **sudo** 프로그램이 가상 머신에 제대로 설치되었는지 확인하십시오.
* `dpkg -l | grep sudo`


* 평가자의 사용자 계정을 "sudo" 그룹에 할당하십시오.
* `sudo usermod -aG <그룹명> <사용자명>`


* `/var/log/sudo/` 폴더가 존재하며, 최소 하나 이상의 파일이 있는지 확인하십시오.
* `cd /var/log/sudo/`


* 이 폴더 내의 파일 내용을 확인하십시오. sudo를 사용한 명령어 기록(history)이 보여야 합니다.
* `ls` (폴더 내용 확인)
* `cat sudo.log` (sudo 명령어 기록이 담긴 파일)


* sudo를 통해 명령어를 실행해 보고, 위 파일이 업데이트되었는지 확인하십시오.
* 아무 명령어나 입력해 봅니다 (예: `sudo ls`). 그 후 파일을 다시 읽어(`cat sudo.log`) 새로운 로그가 기록되었는지 확인합니다.


* **UFW** 프로그램이 VM에 설치되어 있고 제대로 작동하는지 확인하십시오.
* `dpkg -l | grep ufw`


* UFW의 활성 규칙을 나열하십시오. **포트 4242**에 대한 규칙이 존재해야 합니다.
* `sudo ufw status numbered`


<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
* **포트 8080**을 개방하는 새 규칙을 추가하십시오.
* `sudo ufw allow 8080`


* 새 규칙이 활성 규칙 목록에 추가되었는지 확인하십시오.
* `sudo ufw status numbered`


* 새로 추가한 규칙을 삭제하십시오.
* `sudo ufw delete <규칙_번호>` (예: 번호가 2번이면 삭제하고, 남은 규칙에 대해 다시 확인 후 삭제)


* **SSH 서비스**가 설치되어 있고 가상 머신에서 작동 중인지 확인하십시오.
* `dpkg -l | grep ssh`


* SSH 서비스가 오직 **포트 4242**만 사용하는지 확인하십시오.
* iTerm(터미널)을 엽니다.
* 입력: `ssh <사용자ID>@127.0.0.1 -p 4242`
* 이제 터미널을 통해 VM에 연결되었습니다.


* **"root" 사용자로는 SSH를 사용할 수 없음**을 확인하십시오.
* 정보 스크립트(10분마다 나타나는 것)를 열고 그 내용을 설명하십시오.
* 저와 같은 github 가이드를 따랐다면: `cd /usr/local/bin/` 입력
* 그다음: `cat monitoring.sh` 입력



<details>

<summary>monitoring.sh에 대한 설명 (코드 해석)</summary>

<pre class="language-bash"><code class="lang-bash">#!/bin/bash #>> 시스템(OS)에게 bash를 명령어 인터프리터로 사용하라고 알립니다.
arc=$(uname -a) # uname은 시스템 정보를 표시하며, -a는 모든(all) 정보를 의미합니다.

# grep 또는 "global regular expression print"는 정규 표현식을 사용하여

# 텍스트 파일을 검색하고 매칭하는 명령어입니다.

# awk는 매우 다양한 기능을 수행할 수 있는 일종의 슈퍼 명령어입니다.

# 예를 들어 awk '{ print $2; }'는 각 줄의 두 번째 필드를 출력합니다.

# /proc/cpuinfo 명령어는 CPU에 대한 정보를 제공합니다.

pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)

vcpu=$(grep "^processor" /proc/cpuinfo | wc -l) # 각 프로세서에 식별 번호를 제공합니다.
# 프로세서가 하나라면 0을 표시합니다.
# 프로세서가 여러 개라면 0부터 시작하는 표기법을 사용하여
# 모든 프로세서 정보를 개별적으로 표시합니다.

# free 명령어는 실제 메모리와 스왑(swap) 메모리의 총량,

# 그리고 사용 중인 메모리와 여유 메모리에 대한 정보를 제공합니다.

# -m은 메모리 양을 메비바이트(mebibytes) 단위로 표시합니다.

fram=$(free -m | awk '$1 == "Mem:" {print $2}') # 여유/사용되지 않은 메모리 표시

uram=$(free -m | awk '$1 == "Mem:" {print $3}') # 사용 중인 메모리 표시

pram=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}') # 사용률을 백분율(%)로 표시

# df 명령어(disk free의 약자)는 파일 시스템과 관련된

# 총 공간 및 사용 가능한 공간 정보를 표시하는 데 사용됩니다.

fdisk=' | awk '{ft += $2} END {print ft}')

udisk=' | awk '{ut += $3} END {print ut}')

pdisk=' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')

cpul=$(top -bn1 | grep '^%Cpu' | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')

lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')

# 여기서 목표는 VM이 동적 파티션(LVM)을 가지고 있는지 확인하는 것입니다.

# lsblk 명령어를 입력했을 때 "lvm"이 포함된 줄이 0개가 아니라면

# "yes"를 출력하고, 그렇지 않으면 "no"를 출력합니다.

lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)

ctcp=$(ss -neopt state established | wc -l)

ulog=$(users | wc -w)

# hostname 명령어는 시스템의 호스트 이름을 표시합니다.

ip=$(hostname -I) # -I는 모든 IP(네트워크) 주소를 가져오는 데 사용됩니다.

mac=$(ip link show | grep "ether" | awk '{print $2}')

cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

# 위에서 생성한 모든 변수를 호출하여

# 미적으로 보기 좋게 모든 정보를 출력합니다.

wall "	#Architecture: $arc
#CPU physical: $pcpu
#vCPU: $vcpu
#Memory Usage: {fram}MB ($pram%)
#Disk Usage: {fdisk}Gb ($pdisk%)
#CPU load: $cpul
#Last boot: $lb
#LVM use: $lvmu
#Connections TCP: $ctcp ESTABLISHED
#User log: $ulog
#Network: IP $ip ($mac)
#Sudo: $cmds cmd"
</code></pre>

</details>

* 정보 스크립트가 이제 **매분(1분마다)** 실행되도록 **cron** 설정을 변경하십시오.
* `sudo crontab -u root -e`를 입력하여 crontab을 열고 규칙을 추가합니다.
* 새 규칙: `*/1 * * * *`
* 서버를 마지막으로 한 번 더 재시작하십시오.



좋습니다... 이제 프로젝트를 발표할 준비가 되었습니다 :-) 행운을 빕니다!

{% embed url="https://github.com/NoelClay/42-born2beroot_guide/blob/claude/add-korean-translation-7JHtm/EVALUATION_KR.md" %}
연습 자료
{% endembed %}
