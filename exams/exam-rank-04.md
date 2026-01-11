---
description: 설명: Minihell 2부 (오 이런, 또?!)
---

# 시험 등급 04

42의 Rank 4 시험은 매우 간단합니다. **마이크로 셸(micro shell)을 재현하는 것**입니다.

## 주제

`허용되는 함수:`

> malloc, free, write, close, fork, waitpid, signal, kill, exit, chdir, execve, dup, dup2, pipe, strcmp, strncmp

셸 명령을 실행하는 것처럼 동작하는 프로그램을 작성해야 합니다.

*   실행할 명령줄은 이 프로그램의 인수로 제공됩니다.
*   실행 파일의 경로는 절대 경로 또는 상대 경로일 수 있지만, 프로그램은 경로를 구축해서는 안 됩니다 (예: PATH 변수를 사용하지 않습니다).
*   bash에서와 같이 "|"와 ";"를 구현해야 합니다.
    *   "|"가 바로 뒤에 또는 앞에 아무것도 없거나, "|" 또는 ";"로 이어지거나 선행되는 경우는 테스트하지 않을 것입니다.
*   프로그램은 built-in 명령인 `cd`를 경로를 인수로만 사용하여 구현해야 합니다 (인수가 없거나 '-' 옵션은 지원하지 않습니다).
    *   `cd`의 인수가 잘못된 개수인 경우, 프로그램은 STDERR에 "error: cd: bad arguments"를 출력하고 '\n'으로 끝나야 합니다.
    *   `cd`가 실패한 경우, 프로그램은 STDERR에 "error: cd: cannot change directory to path\_to\_change"를 출력하고 '\n'으로 끝나야 하며, path\_to\_change는 `cd`에 전달된 인수로 대체되어야 합니다.
    *   `cd` 명령은 "|"에 의해 바로 뒤따르거나 선행되지 않습니다.
*   어떤 유형의 와일드카드 (\*, \~ 등)도 관리할 필요가 없습니다.
*   환경 변수 ($BLA ...)를 관리할 필요가 없습니다.
*   `execve`와 `chdir`를 제외한 시스템 호출이 오류를 반환하면, 프로그램은 즉시 STDERR에 "error: fatal"을 출력하고 '\n'으로 끝낸 후 프로그램을 종료해야 합니다.
*   `execve`가 실패하면, 프로그램은 STDERR에 "error: cannot execute executable\_that\_failed"를 출력하고 '\n'으로 끝나야 하며, executable\_that\_failed는 실패한 실행 파일의 경로(이는 `execve`의 첫 번째 인수여야 합니다)로 대체되어야 합니다.
*   "열린 파일"의 수를 30개 미만으로 제한하더라도, 프로그램은 수백 개 이상의 "|"를 관리할 수 있어야 합니다.

작동해야 하는 예시:

```
$>./microshell /bin/ls "|" /usr/bin/grep microshell ";" /bin/echo i love my microshell
microshell
i love my microshell
$>

>./microshell 
```

<details>

<summary>한 가지 해결책 (저에게 영감을 준)</summary>

[https://github.com/shackbei/microshell-42/blob/main/microshell.c](https://github.com/shackbei/microshell-42/blob/main/microshell.c)

</details>
