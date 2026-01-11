# 📠 P2P 평가 - 질문

{% hint style="danger" %}
주의하십시오. 평가자가 가장 먼저 확인할 사항은 귀하의 Git 저장소에 Virtual Machine의 Signature만 포함되어 있고 다른 내용은 '전혀' 없는지 확인하는 것입니다. Signature는 평가 당시 VM에 있던 것과 동일해야 합니다 (만약 평가 후에 VM에서 무언가를 변경하면 Signature가 달라집니다).
{% endhint %}



## 이론적 질문

다음은 평가 중에 요청받게 될 **이론적 질문**입니다.

*   Virtual Machine은 어떻게 작동하며, 그 목적은 무엇입니까?
*   Debian 또는 CentOS 중 하나를 선택한 이유는 무엇입니까?
*   Debian과 CentOS의 차이점은 무엇입니까?
    *   만약 Debian을 선택했다면: aptitude와 apt의 차이점은 무엇이며, APPArmor는 무엇입니까?
    *   만약 CentOS를 선택했다면: SELinux와 DNF는 무엇입니까?
*   강력한 비밀번호 정책의 장점과 단점은 무엇입니까? 그 구현에 대해 무엇을 설명할 수 있습니까?
*   Partition이란 무엇입니까? 그리고 더 일반적인 관점에서 LVM (Logical Volume Management)은 어떻게 작동합니까?
*   sudo란 무엇입니까?
*   UFW란 무엇이며, 이를 사용하는 가치는 무엇입니까?
*   SSH (Secure Shell)란 무엇이며, 이를 사용하는 가치는 무엇입니까?
*   cron은 무엇입니까?

모든 내용(그리고 필요한 것보다 더 많은 내용)은 여기에 설명되어 있습니다.

{% content-ref url="whats-a-virtual-machine.md" %}
[Virtual Machine이란 무엇인가](whats-a-virtual-machine.md)
{% endcontent-ref %}

{% content-ref url="install-your-virtual-machine.md" %}
[Virtual Machine 설치하기](install-your-virtual-machine.md)
{% endcontent-ref %}





## 실질적 질문

다음은 평가 중에 시연하도록 요청받을 **실질적 질문**과 답변을 얻기 위해 작성해야 하는 명령어입니다.

*   포함된 Signature가 평가 대상 Virtual Machine의 ".vdi" 파일 Signature와 동일한지 확인하십시오.
    1.  iTerm을 열고 `cd`를 입력합니다.
    2.  그런 다음 `cd sgoinfre/students/<your_intra_username>/<file of your VM>`를 입력합니다.
    3.  그리고 `shasum VirtualBox.vdi`를 입력합니다 (또는 VM의 .vdi 파일 이름이 무엇이든 상관없습니다).
*   평가 중 스크립트는 10분마다 정보를 표시해야 합니다.

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption><p>10분마다 나타나야 하는 스크립트</p></figcaption></figure>

*   머신은 시작 시 그래픽 환경을 가져서는 안 됩니다.
*   이 머신에 연결을 시도하기 전에 비밀번호가 요청되어야 합니다.
*   사용자로 연결해야 합니다. 이 사용자는 root가 아니어야 합니다.
*   UFW 서비스가 시작되었는지 확인하십시오.
    *   `sudo ufw status`
*   SSH 서비스가 시작되었는지 확인하십시오.
    *   `sudo systemctl status ssh`

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>SSH 서비스의 상태를 찾을 수 있습니다.</p></figcaption></figure>

*   운영 체제가 Debian 또는 CentOS인지 확인하십시오 (10분마다 표시되는 정보 스크립트에서 확인할 수 있습니다).
*   귀하의 로그인 아이디를 가진 사용자가 존재하며 VM에 있는지 확인하십시오. 이 사용자는 "sudo" 및 "user42" 그룹에 속해야 합니다.
    *   `getent group sudo`
    *   `getent group user42`

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
*   비밀번호 정책과 관련하여 주제에서 요구하는 규칙이 다음 단계를 따라 제대로 구현되었는지 확인하십시오.
    *   평가자를 위한 새 사용자를 생성하십시오: `sudo adduser <new_username>`
    *   그/그녀가 비밀번호를 선택하게 하십시오.
    *   "evaluating"이라는 새 그룹을 생성하십시오: `sudo groupadd <groupname>`
    *   새 사용자(평가자의 사용자)에게 그룹을 할당하십시오: `sudo usermod -aG <groupname> <username>`
    *   비밀번호 만료 규칙을 확인하십시오: `sudo chage -l username`

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption><p>귀하의 비밀번호 규칙은 이와 같아야 합니다.</p></figcaption></figure>
*   이 새 사용자가 "evaluating" 그룹에 속하는지 확인하십시오: `getent group evaluating`
*   머신의 Hostname이 다음과 같이 올바르게 형식화되었는지 확인하십시오: login42
    *   `hostnamectl`
*   이 Hostname을 본인의 로그인 아이디로 교체하여 수정하고, 머신을 재시작하십시오 (새로운 Hostname이 업데이트되어야 합니다).
    *   `hostnamectl set-hostname <new_hostname>`
    *   이 파일에서도 Hostname을 변경하십시오: `sudo nano /etc/hosts`
    *   Virtual Machine을 재시작하십시오.
*   머신을 원래 이름으로 복원하십시오 (위와 동일한 작업을 수행합니다).
*   VM의 Partition을 확인하십시오 (출력을 주제의 예시와 비교하십시오).
    *   `lsblk`
*   sudo 프로그램이 Virtual Machine에 올바르게 설치되었는지 확인하십시오.
    *   `dpkg -l | grep sudo –`
*   평가자 사용자 이름을 "sudo" 그룹에 할당하십시오.
    *   `sudo usermod -aG <groupname> <username>`
*   " /var/log/sudo/" 폴더가 존재하며 최소한 하나의 파일이 있는지 확인하십시오.
    *   `cd /var/log/sudo/`
*   이 폴더의 파일 내용을 확인하십시오. sudo와 함께 사용된 명령어 기록을 볼 수 있어야 합니다.
    *   `ls`를 사용하여 이 폴더에 무엇이 포함되어 있는지 확인하십시오.
    *   `cat sudo.log` (이 파일은 sudo 명령어 기록이 있는 파일입니다.)
*   sudo를 통해 명령어를 실행하고 위의 파일들이 업데이트되었는지 확인하십시오.
    *   원하는 내용을 작성하고 (예: `sudo ls`) 파일을 다시 읽어 새 로그가 기록에 추가되었는지 확인하십시오 (`cat sudo.log`).
*   UFW 프로그램이 VM에 설치되어 있고 올바르게 작동하는지 확인하십시오.
    *   `dpkg -l | grep ufw –`
*   UFW의 활성 규칙을 나열하십시오. 포트 4242에 대한 규칙이 존재해야 합니다.
    *   `sudo ufw status numbered`

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
*   포트 8080을 열기 위한 새 규칙을 추가하십시오.
    *   `sudo ufw allow 8080`
*   활성 규칙에 추가되었는지 확인하십시오.
    *   `sudo ufw status numbered`
*   새로 추가된 이 규칙을 삭제하십시오.
    *   `sudo ufw delete <rule_number>` (저의 경우는 2이고, 그런 다음 3으로 다시 수행합니다.)
*   SSH 서비스가 Virtual Machine에 설치되어 있고 작동하는지 확인하십시오.
    *   `dpkg -l | grep ssh –`
*   SSH 서비스가 포트 4242만 사용하는지 확인하십시오.
    *   iTerm을 여십시오.
    *   다음과 같이 작성하십시오: `ssh your_user_id@127.0.0.1 -p 4242`
    *   이제 터미널을 통해 VM에 연결되었습니다.
*   "root" 사용자로 SSH를 사용할 수 없는지 확인하십시오.
*   (10분마다 나타나는) 정보 스크립트를 열고 설명하십시오.
    *   만약 저와 동일한 GitHub 가이드를 따랐다면 다음을 입력하십시오: `cd /usr/local/bin/`
    *   그리고: `cat monitoring.sh`

<details>

<summary>monitoring.sh에 대한 설명</summary>

<pre class="language-bash"><code class="lang-bash">#!/bin/bash #>> 당신은 환경/OS에게 bash를 명령어 인터프리터로 사용하도록 지시하고 있습니다.
arc=$(uname -a) #uname은 시스템에 대한 정보를 표시하며, -a는 모든 정보를 의미합니다.


#grep 또는 "global regular expression print”는 정규 표현식에 포함된 텍스트 파일을 검색하고 
#매칭하는 데 사용되는 명령어입니다.

#awk는 여러 작업을 수행할 수 있는 일종의 슈퍼 명령어입니다.
#for example awk '{ print $2; }'는 각 라인의 두 번째 필드를 출력합니다.

#The /proc/cpuinfo command provides information about 
pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l) 

vcpu=$(grep "^processor" /proc/cpuinfo | wc -l) #각 프로세서에 식별 번호를 제공합니다.
						#하나의 프로세서가 있다면 0을 표시합니다.
						#하나 이상의 프로세서가 있다면, 0 표기법을 사용하여 
						#프로세서를 계산하며 모든 프로세서 정보를 개별적으로 표시합니다.

#The free command provides information about the total amount of the physical and 
#swap memory, as well as the free and used memory
# -m displays the amount of memory in mebibytes
fram=$(free -m | awk '$1 == "Mem:" {print $2}') #사용 가능/미사용 메모리를 표시합니다.

uram=$(free -m | awk '$1 == "Mem:" {print $3}') #사용된 메모리를 표시합니다.

pram=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}') #사용률을 백분율로 표시합니다.


#The df command (short for disk free), is used to display information related to
# file systems about total space and available space.
fdisk=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')

udisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')

pdisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')


cpul=$(top -bn1 | grep '^%Cpu' | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')


lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')


#여기서 목표는 VM에 동적 Partition이 있는지 아는 것입니다.
#따라서 lsblk 명령을 입력했을 때 Partition이 0개보다 많다면
# "yes"를 표시할 수 있습니다.
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)

ctcp=$(ss -neopt state established | wc -l)

ulog=$(users | wc -w)

#hostname 명령어는 시스템 Hostname을 표시합니다.
ip=$(hostname -I) # -I는 모든 IP(네트워크) 주소를 얻는 데 사용됩니다.

mac=$(ip link show | grep "ether" | awk '{print $2}')

cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

#여기서는 위에 생성한 모든 변수를 호출하여
#모든 것을 보기 좋게 표시합니다.
wall "	#Architecture: $arc
	#CPU physical: $pcpu
	#vCPU: $vcpu
	#Memory Usage: $uram/${fram}MB ($pram%)
	#Disk Usage: $udisk/${fdisk}Gb ($pdisk%)
	#CPU load: $cpul
	#Last boot: $lb
	#LVM use: $lvmu
	#Connections TCP: $ctcp ESTABLISHED
	#User log: $ulog
	#Network: IP $ip ($mac)
	#Sudo: $cmds cmd"
</code></pre>

</details>

*   cron을 변경하여 정보 스크립트가 이제 매분마다 나타나도록 하십시오.
    *   `sudo crontab -u root -e`를 입력하여 crontab을 열고 규칙을 추가하십시오.
    *   새 규칙: `*/1 * * * *`
    *   서버를 마지막으로 한 번 더 재시작하십시오.



좋습니다... 이제 프로젝트를 발표할 준비가 되었습니다 :-) 행운을 빕니다!