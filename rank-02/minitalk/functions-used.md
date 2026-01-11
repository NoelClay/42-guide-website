# ▪️ 사용되는 함수

이 프로젝트에서 여러 함수를 사용할 수 있습니다. `write`, `ft_printf` (여러분의 것!), `malloc`, `free`, 그리고 여러분의 `libft`에 있는 모든 함수는 이미 알고 계실 겁니다. 하지만, 이전에 사용되지 않았던 다른 중요한 함수들이 이 프로젝트의 성공에 **필수적**일 것입니다. 이 함수들을 함께 살펴보겠습니다.

### signal()

```c
sighandler_t signal(int signum, sighandler_t handler);
```

C 언어의 `signal` 함수는 실행 중인 프로그램이 특정 `signal`을 수신할 때 호출될 함수(이를 `signal handler`라고 합니다)를 지정하는 방법입니다. `Signal`은 어떤 이벤트가 발생했음을 나타내기 위해 운영 체제에서 프로그램으로 보내는 메시지입니다. `signal` 함수를 사용하면 특정 `signal`이 수신되었을 때 호출될 함수를 지정하여 해당 `signal`에 응답하여 어떤 조치를 취할 수 있습니다.

<details>

<summary>signal() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>

void signal_handler(int signum) 
{
  printf("Received SIGINT!\n", signum);
  exit(0);
}

int main() 
{
  // SIGINT 및 SIGTERM signal에 대한 signal handler를
  // signal_handler 함수로 설정합니다.
  
  signal(SIGINT, signal_handler);
  signal(SIGTERM, signal_handler);

  while (1) {
    // 여기에서 작업을 수행합니다...
  }

  return 0;
}
```
{% endcode %}

이 예시에서는 `SIGINT` 및 `SIGTERM` 두 `signal`에 대한 `signal handler`를 `signal_handler` 함수로 설정했습니다. `SIGINT` `signal`은 사용자가 `CTRL+C`를 누를 때 생성되며, `SIGTERM` `signal`은 프로그램이 운영 체제에 의해 종료될 때(예: `kill` 명령 실행 시) 생성됩니다. 이 `signal` 중 하나가 수신되면 `signal_handler` 함수가 호출되고, 콘솔에 메시지를 출력한 후 프로그램을 종료합니다.

이 예시는 `signal` 함수의 일반적인 사용 사례, 즉 특정 `signal`이 수신될 때 프로그램을 정상적으로 종료하도록 `signal handler`를 설정하는 방법을 보여줍니다. `SIGINT` 및 `SIGTERM` `signal`에 대한 `signal handler`를 설정함으로써, 프로그램이 종료될 때 자원을 정의되지 않은 상태로 남기지 않고 깔끔하게 종료되도록 보장할 수 있습니다.

</details>

### sigemptyset()

```c
int sigemptyset(sigset_t *set);
```

`sigemptyset` 함수는 `signal set`을 빈 Set으로 초기화하는 데 사용되며, 이는 Set에 어떤 `signal`도 포함되어 있지 않음을 의미합니다. `Signal set`은 `sigaction`과 같은 일부 함수에서 처리할 `signal`을 정의하는 데 사용됩니다. `sigemptyset` 함수는 `signal` Set에 대한 Pointer를 인수로 받고, 어떤 `signal`도 추가하지 않음으로써 이 Set을 비웁니다. 이 함수는 지정된 `signal`을 `signal set`에 추가하는 `sigaddset` 함수와 함께 자주 사용됩니다.

### sigaddset()

```c
int sigaddset(sigset_t *set, int signum);
```

이 함수는 `signal` Set에 `signal`을 추가할 수 있도록 합니다. `sigaddset` 함수는 두 가지 인수를 취합니다. `signal` Set에 대한 Pointer와 Set에 추가할 `signal`의 번호입니다.

`sigemptyset` 및 `sigaddset`을 사용하여 `SIGINT` `signal`을 `signal` Set에 추가하는 예시는 다음과 같습니다:

<details>

<summary>sigemptyset() 및 sigaddset() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <signal.h>

int main(void)
{
    sigset_t signal_set;

    // 빈 signal set을 초기화합니다.
    sigemptyset(&signal_set);

    // SIGINT를 signal set에 추가합니다.
    sigaddset(&signal_set, SIGINT);
```
{% endcode %}

</details>

### sigaction()

{% code overflow="wrap" %}
```c
int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
```
{% endcode %}

C 언어의 `sigaction` 함수는 프로세스가 특정 `signal`을 수신했을 때 취해야 할 동작을 지정하는 데 사용됩니다. 이 함수는 `signal.h` 헤더 파일에 정의되어 있습니다.

`signum` 인수는 동작이 지정되는 `signal`을 나타냅니다. `act` 인수는 `signal`이 수신될 때 취할 동작을 지정하는 `struct sigaction`에 대한 Pointer입니다. `oldact` 인수는 지정된 `signal`에 대한 이전 동작을 검색하는 데 사용되는 `struct sigaction`에 대한 Pointer입니다.

<details>

<summary>sigaction() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <signal.h>
#include <stdio.h>
#include <stdlib.h>

void signal_handler(int signum) {
  printf("Received signal %d\n", signum);
}

int main(void) {
  struct sigaction action;
  action.sa_handler = signal_handler;
  sigemptyset(&action.sa_mask);
  action.sa_flags = 0;

  sigaction(SIGINT, &action, NULL);

  while (1) {
    // 작업을 수행합니다.
  }

  return 0;
}
```
{% endcode %}

이 예시에서는 `SIGINT` `signal`이 수신될 때 취할 동작으로 `signal_handler` 함수가 지정됩니다. `Signal`이 수신되면 `signal_handler` 함수가 호출되어 콘솔에 메시지를 출력합니다. `sigemptyset` 함수는 `signal mask`를 초기화하는 데 사용되며, 이는 `signal handler`가 실행되는 동안 차단되어야 하는 `signal`을 지정합니다. `sa_flags` 필드는 0으로 설정되어 `signal action`의 기본 동작을 지정합니다.

</details>

### kill()

```c
int kill(pid_t pid, int sig);
```

C 언어에서 `kill` 함수는 **프로세스에 `signal`을 보내는 system call**입니다. 이 함수는 `signal.h` 헤더 파일에 정의되어 있습니다.

`pid` 인수는 통신하려는 프로세스의 Process ID (`PID`)를 지정합니다. `sig` 인수는 프로세스로 보낼 `signal`을 지정합니다. 다양한 목적에 해당하는 여러 `signal`을 보낼 수 있습니다. 예를 들어, `SIGKILL` `signal`은 응답하지 않거나 무한 루프에 빠진 프로세스를 종료하는 데 사용됩니다.

<details>

<summary>kill() 예시</summary>

다음은 `kill` 함수를 사용하여 `SIGKILL` `signal`로 프로세스를 종료하는 예시입니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

int main() {
  pid_t pid = getpid();  // 현재 프로세스의 Process ID를 가져옵니다.
  int result = kill(pid, SIGKILL);  // 프로세스에 SIGKILL signal을 보냅니다.
  if (result == 0) {
    printf("Process terminated successfully.\n");
  } else {
    perror("Error terminating process");
  }
  return 0;
}
```
{% endcode %}

`kill` 함수를 사용하여 프로세스를 종료하면 프로세스에 할당된 자원이 정의되지 않은 상태로 남을 수 있으므로, 일반적으로 `kill` 함수를 사용하여 프로세스를 종료하는 것은 피해야 한다는 점에 유의하십시오. 대신, 종료하기 전에 자원을 정리하고 해제할 기회를 제공함으로써 프로세스가 정상적으로 종료되도록 하는 것이 보통 더 좋습니다.

</details>

### getpid()

```c
pid_t getpid(void);
```

C 언어에서 `getpid` 함수는 **현재 프로세스의 Process ID를 반환합니다**. 이 함수는 `unistd.h` 헤더 파일에 선언되어 있습니다.

<details>

<summary>Process ID는 무엇입니까?</summary>

운영 체제에서 프로세스는 **실행 중인 프로그램의 인스턴스**입니다. Process ID (`PID`)는 프로세스가 생성될 때 운영 체제에서 각 프로세스에 할당하는 고유 식별자입니다.

Process ID는 코딩에서 다양한 목적으로 유용합니다. 몇 가지 예는 다음과 같습니다.

1.  프로세스 식별 및 추적: 앞에서 언급했듯이, Process ID는 프로세스의 고유 식별자이므로 시스템 내의 특정 프로세스를 식별하고 추적하는 데 사용할 수 있습니다.
2.  프로세스에 `signal` 보내기: C 언어의 `kill` 함수를 사용하면 프로세스에 `signal`을 보낼 수 있으며, Process ID를 사용하여 `signal`을 보낼 프로세스를 지정할 수 있습니다. 이는 코드에서 프로세스를 제어하고 상호 작용하는 데 유용할 수 있습니다.
3.  프로세스 통신: Process ID는 프로세스가 서로 통신하는 수단으로 사용될 수 있습니다. 예를 들어, 한 프로세스가 `fork` 함수를 사용하여 새 프로세스를 생성한 다음, 부모 프로세스가 자식 프로세스와 통신할 수 있도록 자식 프로세스의 ID를 부모 프로세스에 다시 전달할 수 있습니다.
4.  디버깅: Process ID는 어떤 프로세스가 문제를 일으키거나 예기치 않게 동작하는지 식별하는 데 사용할 수 있으므로 디버깅에 도움이 될 수 있습니다.

전반적으로 Process ID는 코드에서 프로세스를 관리하고 상호 작용하기 위한 유용한 도구입니다.

</details>

<details>

<summary>getpid() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
  pid_t pid;

  pid = getpid();
  printf("The process ID is %d\n", pid);

  return 0;
}
```
{% endcode %}

이 프로그램은 현재 프로세스의 Process ID를 콘솔에 출력합니다. Process ID는 운영 체제에서 각 프로세스에 할당하는 고유 식별자입니다. 이 ID는 시스템 내의 프로세스를 식별하고 추적하는 데 사용됩니다.

</details>

### pause()

```c
int pause(void);
```

`pause()`는 호출하는 프로세스를 `signal`이 수신될 때까지 잠재우는(sleep) C 표준 라이브러리 함수입니다. 프로세스는 `signal handler`가 실행되거나 `signal`이 무시될 때까지 blocked 상태로 유지됩니다.

<details>

<summary>pause() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    printf("Entering pause...\n");
    pause();
    printf("Exiting pause.\n");
    return 0;
}
```
{% endcode %}

</details>

### sleep()

```c
unsigned int sleep(unsigned int seconds);
```

`sleep()` 또한 호출하는 프로세스를 지정된 초(seconds)만큼 잠재우는 C 표준 라이브러리 함수입니다.

<details>

<summary>sleep() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    printf("Sleeping for 3 seconds...\n");
    sleep(3); // 프로그램이 3초 동안 대기합니다.
    printf("Done sleeping.\n");
    return 0;
}
```
{% endcode %}

</details>

### usleep()

```c
int usleep(useconds_t usec);
```

`usleep()`는 호출하는 프로세스를 지정된 마이크로초(microseconds)만큼 잠재우는 C 표준 라이브러리 함수입니다.

<details>

<summary>usleep() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    printf("Sleeping for 500000 microseconds...\n");
    usleep(500000);
    printf("Done sleeping.\n");
    return 0;
}
```
{% endcode %}

</details>

### exit()

```c
void exit(int status);
```

`exit()`는 호출하는 프로세스를 즉시 종료하는 C 표준 라이브러리 함수입니다. 이 함수는 프로세스의 종료 상태(exit status)를 지정하는 정수 인수를 취합니다. 값 0은 성공적인 종료를 나타내고, 0이 아닌 값은 오류를 나타냅니다.

<details>

<summary>exit() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    printf("Exiting with status 0...\n");
    exit(0);
}
```
{% endcode %}

</details>

그리고 이것이 전부입니다. 이제 혼자서 몇 가지 테스트를 해볼 수 있을 것입니다 ;) 어떻게 해야 할지 모르겠다면, 다음 단계에서 만나 뵙겠습니다.
