# 📠 Virtual Machine 설치하기

솔직히 말씀드리자면, 저는 스스로 Virtual Machine을 설치하는 방법을 알지 못합니다. 저는 몇 가지 가이드를 따라야만 했습니다. 제가 사용했던 가이드는 다음과 같습니다.

{% embed url="https://github.com/pasqualerossi/Born2BeRoot-Guide/blob/main/README.md" %}
Born2BeRoot 가이드
{% endembed %}

{% embed url="https://github.com/NoelClay/42-born2beroot_guide/blob/claude/add-korean-translation-7JHtm/README_KR.md" %}
원본 가이드가 깨졌길래 새로운 가이드를 하나 추가했습니다. 한국어로 번역했습니다.
{% endembed %}

다른 가이드들은 인터넷에서 찾을 수 있습니다. 저는 이 가이드를 사용했고 매우 잘 작동했습니다! 만약 이 가이드가 명확하지 않다고 느껴지신다면 다른 가이드를 자유롭게 사용하셔도 됩니다. 이제부터는 위 가이드를 사용하며 거기에 설명되지 않은 몇 가지 이론적인 내용을 설명하려고 합니다.

{% hint style="info" %}
저는 Virtual Machine과 Debian 이미지를 제 sgoinfre 디렉토리에 두었습니다.

_sgoinfr&#x65;_&#xB294; 42 School 네트워크에서 접근 가능하며 각 컴퓨터에서 사용 가능한 서버입니다. 본인의 로그인 이름으로 된 디렉토리가 있으며 본인만 접근할 수 있는 문서 서버입니다.

각 컴퓨터에는 _goinfr&#x65;_&#xB77C;는 폴더도 있으며, 이 폴더는 일부 문서를 저장할 수 있게 해줍니다. 이 폴더는 각 컴퓨터마다 다르며 세션 저장 공간보다 큽니다.

Virtual Machine을 _sgoinfre_ 서버에 두면 프로젝트 진행 중 컴퓨터를 바꿔도 되지만, _goinfre_ 폴더에 둔다면 프로젝트 기간 내내 같은 컴퓨터에 머물러야 합니다.
{% endhint %}

## Part 1, 2 - Virtual Machine 다운로드 및 설치

이 부분은 비교적 간단하게 할 수 있습니다.

빈 Virtual Machine을 생성하는 과정입니다. 이는 마치 컴퓨터를 처음부터 설정하는 것과 같습니다.

## Part 3 - Virtual Machine 접근하기

이 부분에서는 시스템의 첫 번째 요소들을 구성하게 됩니다. 다음과 같은 작업을 수행합니다:

* 시스템이 실행될 언어, 시간대 및 국가를 선택합니다.
* 시스템의 hostname (your\_42\_login42)과 Password를 생성합니다.
* 첫 번째 일반 사용자 (your\_42\_login)와 그 Password를 생성합니다.
* 시스템의 partitions을 구성합니다.

### LVM을 사용한 디스크 <mark style="color:blue;">Partitioning</mark>이란 무엇인가요?

디스크 _Partitionin&#x67;_&#xC740; 하나 이상의 저장 영역(_partition&#x73;_&#xC774;라고 불림)을 생성하여 각 영역을 개별적으로 관리할 수 있도록 하는 작업입니다.

각 OS는 partitions을 지정하는 방식이 다릅니다. Linux (따라서 Debian 또는 CentOS)에서는 다음과 같이 지정됩니다. `sdXN`. 여기서 X는 매체를 나타내는 문자이며 N은 매체 내의 Partition 번호입니다 (예를 들어, 디스크 b의 세 번째 Partition은 `sdb3`입니다).

Partitioning은 보안 측면에서 많은 이점을 제공합니다.

시스템 Partitions의 포화를 방지하기 위해 많은 볼륨을 생성할 수 있는 서비스에는 별도의 Partitions을 할당하는 것이 일반적입니다.

다음은 존재할 수 있는 Partitions (그리고 이 프로젝트에서 우리가 사용하는 Partitions)의 짧은 목록입니다.

| 이름    | 설명                                               |
| ----- | ------------------------------------------------ |
| /     | 나머지 트리를 포함합니다.                                   |
| /boot | Kernel이 사용자 모드 프로그램 실행을 시작하기 전에 사용되는 데이터를 포함합니다. |
| /var  | 가변 파일(Variable files)을 포함합니다.                    |
| /tmp  | 임시 파일(Temporary files)을 포함합니다.                   |
| /home | HOME 사용자 디렉토리를 포함합니다.                            |

그리고 LVM이란 무엇일까요? LVM은 "동적 Partitions"으로 생각할 수 있습니다. 즉, Linux 시스템이 실행되는 동안 커맨드 라인에서 LVM "Partition" (LVM 용어로는 "Logical Volumes")을 생성/크기 조정/삭제할 수 있습니다. 새로 생성되거나 크기가 조정된 Partitions을 Kernel이 인식하도록 시스템을 재부팅할 필요가 없습니다.

{% hint style="info" %}
이 다음 단계들은 프로젝트에서 가장 중요한 부분입니다. 여기서 무엇을 하고 있는지 이해한다면 평가를 잘 통과할 수 있습니다.
{% endhint %}

## Part 4, 5, 6 - Virtual Machine 설정하기

이제 Virtual Machine을 설치했으므로, 내용을 채워야 합니다. 이 단계에서는 아무것도 할 수 없는 빈 시스템만 가지고 있다고 상상해 보세요. 사용하고 싶은 도구들을 추가해야 합니다.

### `apt` vs `aptitude`

VM을 구성하는 동안 `apt` 명령어를 많이 사용했다는 것을 아마 눈치채셨을 겁니다. 실제로 `aptitude`와 `apt`는 Package Management를 처리하는 두 가지 인기 있는 도구 중 하나입니다. 이 프로젝트에서는 `aptitude` 대신 `apt`를 사용했습니다. 둘 다 커맨드 라인에서 Package를 설치하고 제거하는 동일한 기본 기능을 제공하지만, 약간의 차이점이 있습니다.

* `aptitude`는 어떤 Packages가 명시적으로 요청되었고 어떤 Packages가 Dependency 때문에 설치되었는지 기억합니다. 더 이상 필요하지 않은 경우 명시적으로 요청되지 않은 Packages는 자동으로 제거됩니다.
* `apt`는 커맨드 라인에서 지시받은 대로만 명시적으로 실행합니다.

### APPArmor

Mandatory Access Control (MAC) 보안을 제공하는 Linux 보안 시스템입니다. 시스템 관리자가 프로세스가 수행할 수 있는 작업을 제한할 수 있도록 허용합니다. Debian에 기본적으로 포함되어 있습니다.

실행 중인지 확인하려면 `aa-status`를 실행하십시오.

### Git & Vim

이 프로젝트를 진행 중이라면 이미 과거에 git과 vim을 사용해 보셨을 겁니다. 이러한 도구들을 VM에 설치하여 사용할 수 있도록 할 것입니다.

### SSH (Secure Shell)

SSH는 "Secure Shell"의 약자이므로, 보안이 확보된 "Shell" 또는 터미널입니다. Shell을 사용하면 Linux 서버를 로컬에서, 즉 서버 앞에 물리적으로 있을 때뿐만 아니라 원격으로도 관리할 수 있습니다! 특히 Secure Shell (SSH) 덕분입니다.

SSH Protocol을 사용하면 서버에 원격으로 연결하여 관리할 수 있습니다. 예를 들어, 뉴욕에 있으면서 몇 번의 클릭만으로 파리에 있는 컴퓨터를 관리할 수 있습니다.

### UFW (Uncomplicated Firewall)

Firewall은 컴퓨터 네트워크의 요소입니다. 그 역할은 규칙을 통해 허용되거나 금지된 통신을 정의함으로써 네트워크를 보호하는 것입니다. 다시 말해, 들어오고 나가는 네트워크 트래픽을 모니터링하고 트래픽을 차단할지 허용할지 결정합니다.

이 프로젝트에서는 UFW Firewall로 운영 체제를 구성하고 Port 4242만 열어두도록 요청받을 것입니다. 이를 위한 규칙을 설정해야 합니다.

### Password Policy

Password Policy는 사용자가 상대적으로 강력한 Password를 사용하고 올바르게 사용하도록 장려하여 보안을 개선하기 위해 고안된 일련의 규칙입니다. 예를 들어, 이 프로젝트에서는 강력한 Password Policy (30일마다 만료, 10자보다 길어야 하며 논리적인 순서를 포함하지 않아야 함, 대문자, 특수 문자 및 숫자를 포함해야 함)를 구현하도록 요구하고 있습니다.

Password를 추측하거나 쉽게 해킹할 수 있다면, 해킹 시도에 취약해지고 민감한 정보에 접근할 수 있게 될 가능성이 높습니다.

{% hint style="danger" %}
Virtual Machine의 각 사용자는 다음 Password 규칙을 따라야 합니다.

* PASS\_MAX\_DAYS 30
* PASS\_MIN\_DAYS 2
* PASS\_WARN\_AGE 7

기본 사용자에게 이러한 수정 사항이 적용되지 않았을 수 있으며, 수동으로 적용해야 합니다.

사용자와 연결된 Password 매개변수를 확인하려면 다음 명령어를 입력합니다.

`sudo chage -l username`

만약 c가 위의 수치와 일치하지 않는다면, 다음과 같이 수동으로 변경해야 합니다.

`sudo chage -M 30 <user>`\
`sudo chage -m 2 <user>`\
`sudo chage -W 7 <user>`
{% endhint %}

### Groups 및 사용자

Linux는 Group을 사용하여 사용자를 조직화합니다. 이들은 파일 및 폴더의 권한 설정을 단순화하기 위해 존재합니다. Linux는 여러 사용자가 동시에 시스템에 접근하되 서로 다른 권한을 가질 수 있도록 설계되었습니다.

#### Root

`root`는 Super User이며 시스템에서 무엇이든 할 수 있는 권한을 가지고 있습니다. 따라서 추가적인 보안 계층을 위해 일반적으로 `root` 대신 `sudo` 사용자가 사용됩니다.

#### Sudo

sudo를 설치해야 합니다. Sudo는 "superuser do"의 약자입니다. 간단히 말해 `sudo`는 지정된 사용자에게 Super Power를 부여합니다. 즉, 일시적으로 관리자 역할을 수행할 수 있는 권한입니다. `sudo`는 다른 사용자에게 작업을 수행할 목적으로 다른 사용자의 계정에 제한된 접근 권한을 부여하는 데 사용됩니다. `sudo`를 사용하면 사용자는 `root`로 직접 로그인하지 않고도 관리 권한을 가질 수 있습니다.

사용자에게 부여된 이러한 권한 관리는 `/etc/sudoers` 파일에 포함되어 있습니다.

### Crontab

Cron은 Unix 시스템 사용자가 미리 지정된 날짜와 시간에 스크립트, 명령 또는 소프트웨어를 자동으로 실행할 수 있도록 하는 프로그램입니다.

이 프로젝트에서는 10분마다 메시지를 게시해야 합니다. 이를 수행하기 위해 cron이 필요합니다. 작동 방식은 다음과 같습니다.

1. cron을 사용하여 실행하려는 스크립트를 생성합니다 (이 경우 모든 정보를 포함하는 Monitoring 스크립트가 될 것입니다).
2. 그런 다음 `sudo crontab -u root -e`를 입력하여 Crontab을 열고 규칙을 추가합니다.
3. 규칙은 다음과 같이 작성되어야 합니다.

`* * * * * <path>/<name_of_the_program_to_be_executed>`

여기서 `* * * * *`는 매주, 매월, 매일, 매시, 매분마다를 의미합니다. 몇 가지 예를 들어보겠습니다.

`0 * * * *` - 분이 `0`일 때마다 cron이 실행됨을 의미합니다 (즉, 매시간). `0 1 * * *` - cron이 항상 1시에 실행됨을 의미합니다. `* 1 * * *` - 시간이 1시일 때 매분마다 cron이 실행됨을 의미합니다. 즉, `1:00`, `1:01`, ...`1:59`입니다. `*/10 * * * *` - cron이 10분마다 실행됨을 의미합니다.

## Part 7 - Signature.txt

Signature는 Hard Disk Drive 또는 기타 데이터 저장 장치에 대한 고유 식별 번호입니다. 운영 체제는 이를 사용하여 컴퓨터의 저장 장치들을 구별합니다.
