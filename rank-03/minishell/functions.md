# ▪️ 함수

이미 알고 있는 일부 함수(`printf`, `malloc`, `free` 등)는 여기서 설명하지 않겠습니다. 아마 이 함수들을 모두 사용하지는 않겠지만, 적어도 매뉴얼 페이지 링크를 쉽게 찾을 수 있는 곳을 제공합니다. 또한 일부 함수에 대해서는 사용 예시도 함께 제공합니다.

### readline()

```c
char *readline (const char *prompt);
```

`readline()` 함수는 `prompt`를 프롬프트로 사용하여 터미널에서 한 줄을 읽고 반환합니다. 프롬프트가 매개변수로 주어지지 않으면 터미널에 프롬프트가 표시되지 않습니다. 반환되는 줄은 `malloc`으로 할당되므로, 사용 후 직접 해제해야 합니다(`free`).

<details>

<summary>readline() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <readline/readline.h>
#include <readline/history.h>

int main(void)
{
    char *rl;
    rl = readline("Prompt > ");
    printf("%s\n", rl);
    return (0);
}
```
{% endcode %}

이 프로그램을 컴파일하고 실행하면 다음과 같은 결과가 나타납니다.

```
$> ./minishell
Prompt > Hi ! How are you ?
Hi ! How are you ?
$>
```

</details>

`readline()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/readline.3.html)에서 찾을 수 있습니다.

### rl\_clear\_history()

```c
void rl_clear_history(void);
```

`rl_clear_history()` 함수는 모든 항목을 삭제하여 히스토리 목록을 지웁니다. 이 함수는 `readline` 라이브러리가 히스토리 목록에 저장하는 데이터를 해제합니다.

### rl\_on\_new\_line()

```c
int rl_on_new_line(void);
```

`rl_on_new_line()` 함수는 업데이트 루틴에 우리가 새롭고 비어 있는 줄로 이동했음을 알려주며, 일반적으로 줄을 출력한 후에 사용됩니다.

### rl\_replace\_line()

이 함수에 대한 정보는 찾지 못했습니다.

### rl\_redisplay()

```c
int rl_redisplay(void);
```

`rl_redisplay()` 함수는 화면에 표시되는 내용을 변경하여 현재 `rl_line_buffer`의 내용을 반영합니다.

### add\_history()

```c
void add_history(char *s);
```

`add_history()` 함수는 매개변수로 전달된 줄을 히스토리에 저장하여, 나중에 터미널에서 검색할 수 있도록 합니다 (예: bash에서 위쪽 화살표를 누르는 것과 같이).

### getcwd()

<pre class="language-c"><code class="lang-c"><strong>#include &#x3C;unistd.h>
</strong><strong>char *getcwd(char *buf, size_t size);
</strong></code></pre>

`getcwd()` 함수는 호출 프로세스의 현재 작업 디렉터리(current working directory)인 절대 경로명을 포함하는 널 종료(null-terminated) 문자열을 반환합니다. 경로명은 함수의 결과 및 `buf` 인수를 통해 반환됩니다.

<details>

<summary>getcwd() 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>
#include <stdio.h> // for printf

int main(void)
{
    char *pwd;
    
    pwd = getcwd(NULL, 0);
    printf("pwd: %s\n", pwd);
    return (0);
}
```
{% endcode %}

```shell
$> pwd: /Users/saeby/Documents/tmp
```

</details>

`getcwd()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/getcwd.3.html)에서 찾을 수 있습니다.

### chdir()

```c
#include <unistd.h>
int chdir(const char *path);
```

`chdir()` 함수는 호출 프로세스의 현재 작업 디렉터리를 `path`에 지정된 디렉터리로 변경합니다.

<details>

<summary>chdir() 예시</summary>

```c
#include <unistd.h>
#include <stdio.h> // for printf

int main(void)
{
    char *pwd;
    
    pwd = getcwd(NULL, 0);
    printf("pwd before chdir: %s\n", pwd);
    chdir("/Users/saeby/Documents/42/minishell");
    pwd = getcwd(NULL, 0);
    printf("pwd after chdir: %s\n", pwd);
    return (0);
}
```

```shell
$> pwd before chdir: /Users/saeby/Documents/tmp
$> pwd after chdir: /Users/saeby/Documents/42/minishell
```

</details>

`chdir()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man2/chdir.2.html)에서 찾을 수 있습니다.

### stat() & lstat() & fstat()

{% code overflow="wrap" %}
```c
#include <sys/stat.h>
int stat(const char *restrict pathname, struct stat *restrict statbuf);
int lstat(const char *restrict pathname, struct stat *restrict statbuf);
int fstat(int fd, struct stat *statbuf);
```
{% endcode %}

이 함수들은 `statbuf`가 가리키는 구조체에 파일에 대한 정보를 반환합니다.

이 함수들에 대한 더 자세한 정보는 [여기](../../exams/exam-rank-02/level-1/ft_swap.md)에서 찾을 수 있습니다.

### opendir()

```c
#include <sys/types.h>
#include <dirent.h>
DIR *opendir(const char *name);
```

`opendir()` 함수는 디렉터리 이름에 해당하는 디렉터리 스트림을 열고, 디렉터리 스트림에 대한 Pointer를 반환합니다. 스트림은 디렉터리의 첫 번째 항목에 위치합니다.

`opendir` 함수에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/opendir.3.html)에서 찾을 수 있습니다.

### readdir()

{% code overflow="wrap" %}
```c
#include <dirent.h>
struct dirent *readdir(DIR *dirp);
```
{% endcode %}

`readdir()` 함수는 `dirp`가 가리키는 디렉터리 스트림에서 다음 디렉터리 항목을 나타내는 `dirent` 구조체에 대한 Pointer를 반환합니다. 디렉터리 스트림의 끝에 도달하거나 오류가 발생하면 `NULL`을 반환합니다.

`readdir`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/readdir.3.html)에서 찾을 수 있습니다.

### closedir()

```c
#include <sys/types.h>
#include <dirent.h>
int closedir(DIR *dirp);
```

`closedir()` 함수는 `dirp`와 연결된 디렉터리 스트림을 닫습니다. `closedir()` 호출이 성공하면 `dirp`와 연결된 기본 파일 디스크립터(file descriptor)도 닫힙니다. 이 호출 후에는 디렉터리 스트림 디스크립터 `dirp`를 사용할 수 없습니다.

`closedir`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/closedir.3.html)에서 찾을 수 있습니다.

### strerror()

```c
#include <string.h>
char *strerror(int errnum);
```

`strerror()` 함수는 `errnum` 인수로 전달된 오류 코드를 설명하는 문자열에 대한 Pointer를 반환합니다. 이 문자열은 애플리케이션에 의해 수정되어서는 안 되지만, 이후의 `strerror()` 또는 `strerror_l()` 호출에 의해 수정될 수 있습니다. `perror()`를 포함한 다른 라이브러리 함수는 이 문자열을 수정하지 않습니다.

`strerror`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/strerror.3.html)에서 찾을 수 있습니다.

### perror()

```c
#include <stdio.h>
void perror(const char *s);
```

`perror()` 함수는 시스템 또는 라이브러리 함수 호출 중에 발생한 마지막 오류를 설명하는 메시지를 표준 오류(standard error)로 출력합니다.

`perror`에 대한 더 자세한 정보는 [여기](../../exams/exam-rank-02/level-1/ft_swap.md)에서 찾을 수 있습니다.

### isatty()

```c
#include <unistd.h>
int isatty(int fd);
```

`isatty` 함수는 `fd`가 터미널인지 테스트합니다.

`isatty`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/isatty.3.html)에서 찾을 수 있습니다.

### ttyname()

```c
#include <unistd.h>
char **ttyname(int fd);
```

`ttyname()` 함수는 파일 디스크립터 `fd`에서 열려 있는 터미널 장치의 널 종료 경로명에 대한 Pointer를 반환하거나, 오류 발생 시 `NULL`을 반환합니다.

`ttyname()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/ttyname.3.html)에서 찾을 수 있습니다.

### ttyslot()

```c
#include <unistd.h>
int ttyslot(void);
```

이것은 약간의 배경 스토리가 있는 레거시 함수입니다. 작동 방식 및 모든 내용에 대해 [여기](https://man7.org/linux/man-pages/man3/ttyslot.3.html)에서 읽을 수 있습니다.

### ioctl()

```c
#include <sys/ioctl.h>
int ioctl(int fd, unsigned long request, ...);
```

`ioctl()` 시스템 호출은 특수 파일의 기본 장치 매개변수를 조작합니다. 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man2/ioctl.2.html)에서 찾을 수 있습니다.

### getenv()

```c
#include <stdlib.h>
char *getenv(const char *name);
```

`getenv()` 함수는 환경 목록을 검색하여 환경 변수 `name`을 찾고, 해당 값 문자열에 대한 Pointer를 반환합니다.

`getenv()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/getenv.3.html)에서 찾을 수 있습니다.

### tcsetattr()

```c
#include <termios.h>
int tcsetattr(int fildes, int optional_actions, const struct *termios_p);
```

`tcsetattr()` 함수는 [여기](https://man7.org/linux/man-pages/man3/tcsetattr.3p.html)에 설명된 대로, `termios_p`에 의해 참조되는 `termios` 구조체에서 열린 파일 디스크립터 `fildes`가 참조하는 터미널과 관련된 매개변수를 설정합니다.

### tcgetattr()

```c
#include <termios.h>
int tcgetattr(int fildes, struct termios *termios_p);
```

`tcgetattr()` 함수는 `fildes`가 참조하는 터미널과 관련된 매개변수를 가져와서 `termios_p`가 참조하는 `termios` 구조체에 저장합니다.

더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/tcgetattr.3p.html)에서 찾을 수 있습니다.

### tgetent()

```c
#include <curses.h>
#include <term.h>
int tgetent(char *bp, const char *name);
int tgetflag(char *id);
int tgetnum(char *id);
char *tgetstr(char *id, char **area);
char *tgoto(const char *cap, int col, int row);
int tputs(const char *str, int affcnt, int (*putc)(int));
```

이 루틴들은 `termcap` 라이브러리를 사용하는 프로그램의 변환을 돕기 위해 포함됩니다. 이 모든 함수에 대한 더 자세한 정보는 [여기](https://linux.die.net/man/3/tgetent)에서 찾을 수 있습니다.
