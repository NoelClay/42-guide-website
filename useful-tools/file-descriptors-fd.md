---
description: >-
  파일 디스크립터(File Descriptor)가 무엇인지 설명합니다. 이는 대부분의 프로젝트에서 사용되기 때문입니다.
---

# 🗃️ 파일 디스크립터 (File Descriptor, FD)

### 파일 디스크립터란 무엇일까요?

File Descriptor는 열려 있는 파일을 고유하게 식별하는 `int` 변수입니다.

### 용어 (Terminology)

설명을 진행하기 전에, 이 페이지에서 사용할 몇 가지 용어를 먼저 정의하겠습니다.

| 용어 (Term) | 설명 (Description) |
|---|---|
| File Descriptor | File Descriptor Table 내 File Table Entry의 인덱스입니다. |
| File Descriptor Table | File Table Entry의 배열이며, 각 Process는 자신만의 File Descriptor Table을 갖게 됩니다. |
| File Table Entry | 파일에 대한 정보를 포함하는 구조체(structure)입니다. |
| Global File Table | 시스템 전체(system wide)의 모든 파일을 포함하는 테이블입니다. (모든 파일을 한 번에 포함할 수는 없지만, 운영체제(Operating System)는 이 테이블에 없는 파일을 요청하면 자동으로 테이블을 업데이트합니다). |

예를 들어, `read(2)` 함수와 같이 File Descriptor를 사용할 때 다음과 같은 일이 발생합니다.

1. Global File Table에서 파일을 검색합니다.
   1. 파일을 찾으면 다음 단계로 이동합니다.
   2. 파일을 찾지 못하면, 운영체제가 요청된 파일을 사용할 수 있도록 Global File Table을 업데이트한 다음, 1단계로 돌아갑니다.
2. 요청된 파일에 대한 File Table Entry를 File Descriptor Table에 생성합니다.
3. 생성된 File Table Entry에 사용되지 않은 첫 번째 File Descriptor를 할당합니다.

<mark style="color:red;">// 여기에 스키마 삽입</mark>

### 표준 File Descriptor

대부분의 Unix 시스템과 마찬가지로 C 언어에서는 File Descriptor Table에 자동으로 추가되는 3가지 표준 File Descriptor가 있습니다. 이 File Descriptor들은 표준 입/출력 File Descriptor이며 항상 다음 값을 갖습니다.

* 0: 이 File Descriptor는 stdin (=> 표준 입력, 터미널)을 나타냅니다. 이는 터미널에서 사용자 입력을 읽을 때 사용되는 File Descriptor입니다.
* 1: 이 File Descriptor는 stdout (=> 표준 출력, 터미널)을 나타냅니다. 이는 터미널에 데이터를 쓸 때 사용되는 File Descriptor입니다.
* 2: 이 File Descriptor는 stderr (=> 표준 에러 출력, 터미널)을 나타냅니다. 이는 터미널에 에러를 쓸 때 사용되는 File Descriptor입니다. 정보는 동일한 방식으로 기록되지만, 에러를 파일에 기록하는 프로그램은 stderr File Descriptor에 기록된 모든 내용을 파일로 리다이렉션(redirection)할 수 있습니다. 이는 운영체제에 의해 다른 방식으로 관리됩니다.

### 예시 (Examples)

`write(2)` 함수를 사용할 때, 실제로는 File Descriptor를 사용하고 있는 것입니다.

`write(2)` 함수의 원형(prototype)은 다음과 같습니다.

```c
 size_t write(int fd, const void *buf, size_t count);
```

보시다시피, 이 함수의 첫 번째 매개변수는 fd라고 불리는 `int`형입니다. 익숙하게 들리시나요?

첫 번째 매개변수를 사용하여 어느 파일에 쓸지 지정할 수 있습니다. 사용 방법을 보여주는 간단한 예시를 만들어 보겠습니다.

{% code title="main.c" lineNumbers="true" %}
```c
// write(2) 함수를 위한 unistd 헤더 포함
#include <unistd.h>
// open(2) 함수를 위한 fcntl 헤더 포함
#include <fcntl.h>

// main 함수 아래에 선언된 함수들의 원형
void ft_putchar_terminal(char c);
void ft_putchar_test_file(char c);

// main 함수
int main(void)
{
    // 문자 T로 ft_putchar_terminal 함수 호출
    ft_putchar_terminal("T");
    // 문자 F로 ft_putchar_test_file 함수 호출
    ft_putchar_test_file("F");
    return (0);
}

/* 이 함수는 standard output을 통해 터미널에 간단히 하나의 문자를 작성합니다. 
 * 위에서 설명했듯이, stdout을 위한 fd는 1이므로, 
 * write(2) 함수의 첫 번째 매개변수로 1을 넣습니다.
 */
void ft_putchar_terminal(char c)
{
    write(1, &c, 1);
}

/* 이 함수는 open(2) 함수를 사용하여 "test.txt"라는 파일을 엽니다.
 * 파일이 열리면, 파일 디스크립터를 fd 변수에 저장합니다.
 * 그런 다음 파일을 여는 데 오류가 있었는지 확인합니다.
 * 오류가 없으면, test.txt 파일에 문자 F를 작성합니다.
 * 오류가 있으면, standard error output에 오류 메시지를 작성합니다.
 */
void ft_putchar_test_file(char c)
{
    int fd;
    
    /* open(2) 함수를 사용하여 test.txt 파일을 읽기/쓰기(Read/Write) 모드로 열고
     * 반환된 File Descriptor 값을 fd 변수에 할당합니다.
     */
    fd = open("test.txt", O_RDWR);
    /* 파일이 올바르게 열렸는지 확인합니다.
     * 파일을 여는 데 오류가 있으면 open 함수는 -1을 반환합니다.
     */
    if (fd > 0)
        /* test.txt 파일의 File Descriptor를 write(2) 함수의 첫 번째
         * 매개변수로 전달하여 문자 F를 작성합니다.
         */
        write(fd, &c, 1);
    else
        /* 파일을 여는 데 오류가 있었다면, fd 값은 -1이 될 것이고
         * 이전 조건문 안으로 들어가지 않을 것입니다. 따라서 write(2) 함수의
         * 첫 번째 매개변수로 File Descriptor 2(stderr)를 전달하여
         * 오류 메시지를 작성합니다.
         */
        write(2, "test.txt not found.\n", 20);
}
```
{% endcode %}

### 작동 방식

File Descriptor에서 바이트를 읽을 때, 마지막으로 파일의 어느 위치까지 읽었는지 기억합니다.

즉, 파일에서 20바이트를 읽었다면, 다음에 같은 File Descriptor를 사용하여 읽을 때는 21번째 바이트부터 읽기 시작합니다. 아래 예시를 살펴보십시오.

{% code title="main.c" lineNumbers="true" %}
```c
// write(2) & read(2) 함수를 위한 unistd 헤더 포함
#include <unistd.h>
// open(2) 함수를 위한 fcntl 헤더 포함
#include <fcntl.h>

// main 함수 아래에 선언된 함수들의 원형
void ft_putchar_terminal(char c);
void ft_putchar_test_file(char c);

// main 함수
int main(void)
{
    // 문자 T로 ft_putchar_terminal 함수 호출
    ft_putchar_terminal("T");
    // 문자 F로 ft_putchar_test_file 함수 호출
    ft_putchar_test_file("F");
    return (0);
}

/* 이 함수는 standard output을 통해 터미널에 간단히 하나의 문자를 작성합니다. 
 * 위에서 설명했듯이, stdout을 위한 fd는 1이므로, 
 * write(2) 함수의 첫 번째 매개변수로 1을 넣습니다.
 */
void ft_putchar_terminal(char c)
{
    write(1, &c, 1);
}

/* 이 함수는 open(2) 함수를 사용하여 "test.txt"라는 파일을 엽니다.
 * 파일이 열리면, 파일 디스크립터를 fd 변수에 저장합니다.
 * 그런 다음 파일을 여는 데 오류가 있었는지 확인합니다.
 * 오류가 없으면, test.txt 파일에 문자 F를 작성합니다.
 * 오류가 있으면, standard error output에 오류 메시지를 작성합니다.
 */
void ft_putchar_test_file(char c)
{
    int fd;
    
    /* open(2) 함수를 사용하여 test.txt 파일을 읽기/쓰기(Read/Write) 모드로 열고
     * 반환된 File Descriptor 값을 fd 변수에 할당합니다.
     */
    fd = open("test.txt", O_RDWR);
    /* 파일이 올바르게 열렸는지 확인합니다.
     * 파일을 여는 데 오류가 있으면 open 함수는 -1을 반환합니다.
     */
    if (fd > 0)
        /* test.txt 파일의 File Descriptor를 write(2) 함수의 첫 번째
         * 매개변수로 전달하여 문자 F를 작성합니다.
         */
        write(fd, &c, 1);
    else
        /* 파일을 여는 데 오류가 있었다면, fd 값은 -1이 될 것이고
         * 이전 조건문 안으로 들어가지 않을 것입니다. 따라서 write(2) 함수의
         * 첫 번째 매개변수로 File Descriptor 2(stderr)를 전달하여
         * 오류 메시지를 작성합니다.
         */
        write(2, "test.txt not found.\n", 20);
}
```
{% endcode %}