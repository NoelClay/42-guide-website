# ▪️ 사용된 함수들

이 프로젝트에서는 여러 함수를 사용할 수 있습니다. 이미 `write`, `ft_printf`, `malloc`, `free`, 그리고 `libft`의 모든 함수와 같은 일부 함수를 알고 계십니다. 하지만 이전에 사용해 본 적이 없는 다른 중요한 함수들이 이 프로젝트의 성공에 **필수적**일 것입니다. 이 함수들을 함께 살펴보겠습니다.

### access()

```c
int access(const char *pathname, int mode);
```

`access()`는 프로그램이 파일 `pathname`에 접근할 수 있는지 확인합니다.

`mode`는 수행할 접근성 검사를 지정하며, `F_OK` 값이거나 `R_OK`, `W_OK`, `X_OK` 중 하나 이상을 비트별 OR 연산으로 조합한 마스크 값입니다. `F_OK`는 파일의 존재 여부를 테스트합니다. `R_OK`, `W_OK`, `X_OK`는 파일이 존재하며 각각 읽기, 쓰기, 실행 권한이 부여되었는지 테스트합니다.

성공 시 (요청된 모든 권한이 부여된 경우), 0이 반환됩니다. 오류 시 (적어도 하나의 `mode` 비트가 거부된 권한을 요청했거나 다른 오류가 발생한 경우), -1이 반환되며, `errno`가 적절하게 설정됩니다.

<details>

<summary>access() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>
#include <stdio.h>

int main()
{
    if (access("readfile", R_OK) == 0)
        printf("readfile is accessible in reading mode\n");
    if (access("writefile", W_OK) == 0)
        printf("writefile is accessible in writing mode\n");
    if (access("executefile", X_OK) == 0)
        printf("executefile is accessible in execution mode\n");
    if (access("rwfile", R_OK|W_OK) == 0)
        printf("rwfile is accessible in writing and reading mode\n");
}
```
{% endcode %}

이 예시에서는 `access` 함수를 여러 번 사용합니다.

첫 번째로 프로그램이 `readfile`을 읽을 수 있는지 확인합니다.

두 번째로 프로그램이 `writefile`에 쓸 수 있는지 확인합니다.

세 번째로 프로그램이 `executefile`을 실행할 수 있는지 확인합니다.

네 번째는 파일 `rwfile`이 읽기 `AND` 쓰기 모드로 접근 가능한지 확인하기 위해 비트별 `OR` 연산자를 사용한 예시입니다.

</details>

`access()`에 대한 더 많은 정보는 여기서 확인할 수 있습니다: [https://linux.die.net/man/2/access](https://linux.die.net/man/2/access)

### dup2()

```c
int dup2(int oldfd, int newfd);
```

`dup2()`는 `newfd`를 `oldfd`의 복사본으로 만듭니다. 필요한 경우 `newfd`를 먼저 닫습니다. 하지만 다음 사항에 유의하십시오:

*   `oldfd`가 유효한 파일 디스크립터가 아닌 경우, 호출이 실패하며 `newfd`는 닫히지 않습니다.
*   `oldfd`가 유효한 파일 디스크립터이고 `newfd`가 `oldfd`와 동일한 값을 가지는 경우, `dup2()`는 아무것도 수행하지 않고 `newfd`를 반환합니다.

`dup2()`가 성공적으로 반환된 후, 이전 파일 디스크립터와 새 파일 디스크립터는 상호 교환적으로 사용될 수 있습니다. 이들은 동일한 열린 파일 설명을 참조하므로 파일 오프셋 및 파일 상태 플래그를 공유합니다. 예를 들어, 디스크립터 중 하나에서 `lseek()`를 사용하여 파일 오프셋을 수정하면 다른 디스크립터의 오프셋도 변경됩니다.

오류 발생 시, `dup2()` 함수는 `-1`을 반환합니다.

<details>

<summary>dup2() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>
#include <fcntl.h>

int main(int ac, char *av[], char *env[])
{
    (void) ac;
    (void) av;
    int in;
    int out;
    char *grep_args[] = {"grep", "Lausanne", NULL};
    
    // in과 out 파일을 엽니다 (두 파일이 모두 존재한다고 가정합니다)
    in = open("in", O_RDONLY);
    out = open("out", O_WRONLY); 
    
    // 표준 입력을 입력 파일로 대체합니다
    dup2(in, 0);
    // 사용하지 않는 파일 디스크립터를 닫습니다
    close(in);
    close(out);
    
    // grep을 실행합니다
    execve("grep", grep_args, env);
}
```
{% endcode %}

이 예시에서는, 먼저 `in`과 `out` 파일 모두를 각각 읽기 모드와 쓰기 모드로 엽니다.

그런 다음 `dup2()`를 사용하여 `stdin` 파일 디스크립터를 `in` 파일 디스크립터로 대체합니다.
이러한 방식으로, 이후에 오는 명령이 `stdin`에서 무엇을 읽든지 간에, `stdin` 파일 디스크립터가 이제 `in` 파일을 "가리키기" 때문에 그 내용은 `in`의 내용이 됩니다.

이제 `in`과 `out`을 닫을 수 있습니다. 더 이상 사용하지 않기 때문입니다. 맞습니까?

우리는 `stdin` 파일 디스크립터를 `in`과 동일하게 설정했습니다. 따라서 이제 `stdin`만 사용하며, `in`과 `out`은 더 이상 사용되지 않으므로 닫을 수 있습니다.

이제 `execve()` 함수를 사용하여 `grep` 셸 명령을 실행합니다 (이는 이 페이지 아래에서 설명됩니다). `grep`이 인자 없이 실행되면, 실행 전에 표준 입력에서 텍스트를 읽습니다. 그러면 어떻게 될까요?

우리는 `stdin` 파일 디스크립터를 `in`으로 대체했음을 기억하십시오. 따라서 `grep`은 표준 입력에서 읽을 것이고, 표준 입력은 이제 `in` 파일에서 읽으므로, `grep`은 `in` 파일의 내용에 대해 실행될 것입니다.

</details>

`dup2()`에 대한 더 많은 정보는 여기서 확인할 수 있습니다: [https://linux.die.net/man/2/dup2](https://linux.die.net/man/2/dup2)

### pipe()

```c
int pipe(int pipefd[2]);
```

`pipe()`는 파이프, 즉 프로세스 간 통신에 사용될 수 있는 단방향 데이터 채널을 생성합니다. 배열 `pipefd`는 파이프의 양쪽 끝을 참조하는 두 개의 파일 디스크립터를 반환하는 데 사용됩니다. `pipefd[0]`은 파이프의 읽기 끝(read end)을 참조합니다. `pipefd[1]`은 파이프의 쓰기 끝(write end)을 참조합니다. 쓰기 끝으로 작성된 데이터는 파이프의 읽기 끝에서 읽힐 때까지 커널에 의해 버퍼링됩니다.

성공 시, `0`이 반환됩니다. 오류 시, `-1`이 반환되며, `errno`가 적절하게 설정됩니다.

<details>

<summary>pipe() 예시</summary>

```c
/**
 * "cat infile | grep Lausanne" 명령을 실행합니다.  
 */

#include <fcntl.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(int ac, char *av[], char *env[])
{
    (void) ac;
    (void) av;
    int pipefd[2];
    int pid;
    
    char *cat_args[] = {"/bin/cat", "infile", NULL};
    char *grep_args[] = {"/usr/bin/grep", "Lausanne", NULL};
    
    // 파이프 생성
    // 파일 디스크립터는 pipefd[0]과 pipefd[1]에 저장됩니다.
    pipe(pipefd);
    
    if (pid == 0)
    {
        // 자식 프로세스는 여기에 도달하여 "grep Lausanne"을 처리합니다.
        // 표준 입력을 파이프의 입력 부분으로 대체합니다.
        dup2(pipefd[0], 0);
        
        // 사용되지 않는 파이프의 절반을 닫습니다.
        close(pipefd[1]);
        
        // grep을 실행합니다.
        execve("/usr/bin/grep", grep_args, env);
    }
    else
    {
        // 부모 프로세스는 여기에 도달하여 "cat scores"를 처리합니다.
        // 표준 출력을 파이프의 출력 부분으로 대체합니다.
        dup2(pipefd[1], 1);
        
        // 사용되지 않는 파이프의 절반을 닫습니다.
        close(pipefd[0]);
        
        // cat을 실행합니다.
        execve("/bin/cat", cat_args, env);
    }
    // 사용되지 않는 파이프를 닫습니다.
    close(pipefd[0]);
    close(pipefd[1]);
    
    // 자식 프로세스가 완료될 때까지 기다립니다.
    waitpid(pid);
}
```

어떤 일이 발생하는지 확인하려면 주석을 읽어보십시오. 다른 방식으로 설명하기는 상당히 어렵습니다. 프로젝트를 마치면 다이어그램을 추가하겠습니다.

이 예시에서 우리는 파이프를 생성하고, 자식 프로세스를 위해 표준 입력을 파이프의 입력 부분으로 대체합니다.

부모 프로세스를 위해서는 표준 출력을 파이프의 출력 부분으로 대체합니다.

맨 마지막에는 `waitpid()` 함수를 사용하여 프롬프트가 다시 나타나기 전에 자식 프로세스가 완료되기를 기다립니다.

</details>

### fork()

```c
pid_t fork(void);
```

`fork()`는 호출하는 프로세스를 복제하여 새 프로세스를 생성합니다. `child`(자식)라고 불리는 새 프로세스는 `parent`(부모)라고 불리는 호출 프로세스의 정확한 복제본입니다. 다만, [여기](https://linux.die.net/man/2/fork)에서 찾을 수 있는 몇 가지 차이점은 있습니다 (여기에 전부 적기에는 너무 많습니다).

<details>

<summary>fork() 예시</summary>

```c
/**
 * "cat infile | grep Lausanne" 명령을 실행합니다.  
 */

#include <fcntl.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(int ac, char *av[], char *env[])
{
    (void) ac;
    (void) av;
    int pipefd[2];
    int pid;
    
    char *cat_args[] = {"/bin/cat", "infile", NULL};
    char *grep_args[] = {"/usr/bin/grep", "Lausanne", NULL};
    
    // 파이프 생성
    // 파일 디스크립터는 pipefd[0]과 pipefd[1]에 저장됩니다.
    pipe(pipefd);
    
    if (pid == 0)
    {
        // 자식 프로세스는 여기에 도달하여 "grep Lausanne"을 처리합니다.
        // 표준 입력을 파이프의 입력 부분으로 대체합니다.
        dup2(pipefd[0], 0);
        
        // 사용되지 않는 파이프의 절반을 닫습니다.
        close(pipefd[1]);
        
        // grep을 실행합니다.
        execve("/usr/bin/grep", grep_args, env);
    }
    else
    {
        // 부모 프로세스는 여기에 도달하여 "cat scores"를 처리합니다.
        // 표준 출력을 파이프의 출력 부분으로 대체합니다.
        dup2(pipefd[1], 1);
        
        // 사용되지 않는 파이프의 절반을 닫습니다.
        close(pipefd[0]);
        
        // cat을 실행합니다.
        execve("/bin/cat", cat_args, env);
    }
    // 사용되지 않는 파이프를 닫습니다.
    close(pipefd[0]);
    close(pipefd[1]);
    
    // 자식 프로세스가 완료될 때까지 기다립니다.
    waitpid(pid);
}
```

파이프 함수와 동일한 예시를 사용했습니다. 왜냐하면 (적어도 이 프로젝트에서는) 이 둘은 함께 작동하기 때문입니다. 실행하려는 각 명령은 자체적인 자식 프로세스를 필요로 하므로, 실행해야 하는 명령의 수만큼 부모 프로세스를 자식 프로세스로 `fork()`해야 합니다.

</details>

### waitpid()

```c
pid_t waitpid(pid_t pid, int *status, int options);
```

`waitpid()` 시스템 호출은 _pid_ 인자로 지정된 자식이 상태를 변경할 때까지 호출 프로세스의 실행을 일시 중단합니다. 기본적으로 `waitpid()`는 종료된 자식만 기다립니다.

<details>

<summary>waitpid() 예시</summary>

```c
#include <sys/types.h>
#include <sys/wait.h>

int main(void)
{
    int pid;
    
    pid = fork();
    
    waitpid(pid);
}
```

이 예시에서는 메인 프로세스를 `fork()`하여 자식 프로세스를 생성합니다. 자식의 pid를 `pid` 변수에 저장하고 마지막에 그 자식을 기다립니다.

</details>

`wait()` 및 `waitpid()`에 대한 더 많은 정보는 [여기](https://linux.die.net/man/2/wait)에서 찾을 수 있습니다.

### wait()

```c
pid_t wait(int *status);
```

`wait()` 시스템 호출은 호출 프로세스의 자식 중 하나가 종료될 때까지 실행을 일시 중단합니다.

<details>

<summary>wait() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <sys/types.h>
#include <sys/wait.h>

// 자식 하나를 기다림
int main(void)
{
    int status;
    
    wait(&status);
}

// 여러 자식을 기다림
int main(void)
{
    int status;
    
    while (n > 0)
    {
        wait(&status);
        n--;
    }
}
```
{% endcode %}

첫 번째 `main` 함수에서는 추가로 진행하기 전에 하나의 자식 프로세스가 종료될 때까지 기다립니다.
두 번째 `main` 함수에서는 자식들을 한 번에 하나씩 기다립니다. 자식 하나가 종료될 때마다 `n`이 `0`에 도달할 때까지 `1`을 제거하고 계속 진행합니다.

</details>

`wait()` 및 `waitpid()`에 대한 더 많은 정보는 [여기](https://linux.die.net/man/2/wait)에서 찾을 수 있습니다.

### execve()

```c
int execve(const char *filename, char *const argv[], char *const envp[]);
```

`execve()`는 `filename`이 가리키는 프로그램을 실행합니다.

`execve()`는 성공 시 반환되지 않으며, 호출 프로세스는 실행된 `filename`으로 **대체**됩니다.

<details>

<summary>execve() 예시</summary>

```c
#include <unistd.h>

int main(int ac, char **av, char **envp)
{
    (void) ac;
    const char *filename = "/usr/bin/grep";
    char *const argv[] = {"/usr/bin/grep", "a", NULL};
    
    execve(filename, argv, envp);
}
```

</details>

### unlink()

```c
int unlink(const char *pathname);
```

`unlink()`는 파일 시스템에서 이름을 삭제합니다. 만약 해당 이름이 파일에 대한 마지막 링크였고, 파일을 열고 있는 프로세스가 없다면 파일은 삭제되고 파일이 사용하던 공간은 재사용 가능하도록 만들어집니다.

이름이 파일에 대한 마지막 링크였지만, 여전히 파일을 열고 있는 프로세스가 있는 경우, 해당 파일을 참조하는 마지막 파일 디스크립터가 닫힐 때까지 파일은 계속 존재합니다.

성공 시, `0`이 반환됩니다. 오류 시, `-1`이 반환되며, `errno`가 적절하게 설정됩니다.

<details>

<summary>unlink() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main(void)
{
    if (access("tmp", F_OK) == 0)
        unlink("tmp");
    return (0);
}
```
{% endcode %}

이 예시에서는 `access()` 함수를 사용하여 `tmp`라는 파일이 존재하는지 확인하고, 파일이 존재할 경우 `unlink()` 함수를 사용하여 파일을 제거합니다.

</details>

`unlink()`에 대한 더 많은 정보는 [여기](https://linux.die.net/man/2/unlink)에서 찾을 수 있습니다.

### 최종 요약

일반적으로 파이프가 설명되는 방식은 한 명령의 출력을 다음 명령의 입력으로 리디렉션하는 것입니다. 이는 올바른 설명입니다. 하지만 한 가지 중요한 점이 있습니다. `pipex`를 구현할 때, 수행하려는 각 명령에 대해 새로운 자식 프로세스를 시작해야 하며, 이 자식 프로세스들은 모두 동시에 생성됩니다.

이는 모든 명령이 동시에 실행된다는 것을 의미하며, 단지 명령들이 파이프에서 읽기 전에 파이프의 쓰기 끝이 닫히기를 기다릴 뿐입니다.