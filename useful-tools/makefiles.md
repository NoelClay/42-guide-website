---
description: "Makefile이 다소 신비롭고 어려운 개념일 수 있기에, 저는 최선을 다해 기본적인 내용을 설명해 드리겠습니다."
---

# 📄 Makefiles

### 서론

{% hint style="warning" %}
다음 예시들에서는 주로 _executable_(실행 파일)이라는 용어를 사용하겠습니다. 하지만 Makefiles는 Library를 생성하는 데도 사용될 수 있다는 점을 참고하십시오. Library용 Makefile을 생성하는 단계는 이 페이지의 후반부에서 설명합니다.
{% endhint %}

{% hint style="warning" %}
이 GitBook에서 Makefile 예시를 복사하여 붙여넣을 경우, GitBook이 Tab을 4칸 공백(4 spaces tab)으로 자동 변환하기 때문에, 4칸 공백을 단일 Tab으로 모두 변경해야 한다는 점에 유의하시기 바랍니다.
{% endhint %}

{% hint style="info" %}
많은 42 프로젝트에서 Makefile이 필요합니다. 단순히 Github에서 복사하는 것보다 Makefile이 무엇을 하는지, 그리고 어떻게 수정하는지 이해하는 것이 더 좋다고 생각합니다.
{% endhint %}

### Makefile / make 란 무엇인가요?

Makefiles는 기본적으로 많은 파일을 Compile 하거나 Recompile 하는 반복적인 작업을 자동화하는 정교한 Shell Script입니다.

Make는 Makefile의 실행을 시작하도록 설계된 Unix Utility입니다.
Makefile은 _Makefile_이라는 이름으로 생성하는 특별한 파일이며, Shell Command를 포함합니다. 이 Makefile이 포함된 디렉토리에서 _make_ 명령을 실행하면 Makefile에 작성된 명령들이 실행됩니다.

Make는 파일들(일반적으로 Object File)이 마지막으로 업데이트된 시점을 추적하여, Source File을 최신 상태로 유지하는 데 필요한 파일만 업데이트합니다.
만약 많은 Source File 및/또는 Header File을 가진 프로그램이 있는 경우, 다른 파일들이 의존하는 파일을 변경하면 해당 종속된 파일들을 모두 Recompile 해야 합니다. 이것은 극도로 시간이 많이 소요되는 작업입니다.

Makefile은 _규칙(rules)_ 목록을 포함합니다.
이 규칙들은 실행하고자 하는 Command가 무엇인지 시스템에 알려줍니다. 대부분의 경우, 이 규칙들은 일련의 파일들을 Compile 하거나 Recompile 하는 Command입니다.

규칙은 두 부분으로 구성됩니다. 첫 번째 줄은 _dependency line_(의존성 라인)이라고 불리며, 다음 줄(들)은 _actions_ 또는 _commands_라고 불립니다. Action 라인(들)은 반드시 Tab으로 들여쓰기 되어야 합니다.

규칙 구문(Rule syntax):

```
RULE: DEPENDENCY LINE
    [tab]ACTION LINE(S)
```

의존성 라인 역시 두 부분으로 구성됩니다. 콜론 앞의 첫 번째 부분은 _target_ 파일이며, 콜론 뒤의 두 번째 부분은 _prerequisites_(선행 조건)입니다.
첫 번째 부분이 두 번째 부분에 의존하기 때문에 _dependency line_이라고 불립니다. 여러 개의 _prerequisites_ 파일은 공백으로 구분되어야 합니다.

Makefile이 생성된 후, _make_를 입력하여 프로그램이 (Re)compile 될 수 있습니다. _Make_는 Makefile을 읽고, 의존성 트리(dependency tree)를 생성하며, 필요한 모든 Action을 취합니다.
모든 종속성이 업데이트될 필요가 없을 수 있으므로, Makefile의 모든 규칙을 실행하지는 않습니다. _Target_ 파일이 누락되었거나 종속성 파일(들)보다 오래된 경우에만 Target 파일을 Rebuild 할 것입니다.

별도로 지시하지 않는 한, Make는 Build 과정에서 오류가 발생하면 중단됩니다.

### 간단한 Makefile 예시

_hello.c_, _hellofunc.c_, _hello.h_라는 세 가지 파일로 시작하겠습니다.

<details>

<summary>hello.c</summary>

{% code lineNumbers="true" %}
```c
#include "hello.h"

int    main(void)
{
    printHello(void);

    return (0);
}
```
{% endcode %}

</details>

<details>

<summary>hellofunc.c</summary>

<pre class="language-c" data-overflow="wrap" data-line-numbers><code class="lang-c"><strong>#include "hello.h"
</strong>
void    printHello(void)
{
    printf("Hello World!\n);
}
</code></pre>

</details>

<details>

<summary>hello.h</summary>

```c
#ifndef HELLO_H
# define HELLO_H
# include <stdio.h>

void    printHello(void);
#endif
```

</details>

일반적으로는 다음 명령을 실행하여 이 파일들을 Compile 할 것입니다:
`gcc -Wextra -Wall -Werror hello.c hellofunc.c -I. -o hello`

{% hint style="info" %}
`-I.`는 gcc가 현재 디렉토리(.)에서 Include 파일인 _hello.h_를 찾도록 하기 위해 포함됩니다.
{% endhint %}

이 Command는 두 개의 .c 파일을 Compile 하고 _hello_라는 이름의 Executable을 생성합니다.

이 파일들을 위해 생성할 수 있는 가장 간단한 Makefile은 다음과 같이 보일 것입니다:

{% code title="Makefile" lineNumbers="true" %}
```makefile
hello: hello.c hellofunc.c
    gcc -Wextra -Wall -Werror hello.c hellofunc.c -I. -o hello
```
{% endcode %}

다음으로, 모든 Makefile에 존재하는 규칙들(Rules)을 설명하겠습니다. 이들은 관례적인 규칙이며, 42 Norm에서 요구됩니다.

더 복잡한 Makefile은 나중에 설명드리겠습니다.

### 규칙 컨벤션

앞서 말씀드렸듯이, 규칙 컨벤션이 있습니다. 이들은 필수는 아니지만 거의 모든 Makefile에 존재하는 규칙이며, Compile 및 Build, 그리고 Cleaning 작업을 더 쉽게 만듭니다. 게다가 이 규칙들은 42 Norm에서 요구됩니다.

<table><thead><tr><th width="203">규칙 이름 (Rule name)</th><th>설명 (Description)</th></tr></thead><tbody><tr><td>all</td><td>Makefile에서 마주치는 첫 번째 규칙이므로 <code>make</code> 명령을 특정 규칙 없이 사용할 때 실행되어야 합니다. 일반적으로 종속성으로 $(NAME) 규칙을 호출합니다 (아래 예시 참조).</td></tr><tr><td>$(NAME)</td><td>이것이 주요 규칙입니다. 생성하고자 하는 executable의 이름을 타겟으로 가지며, 모든 Object File을 executable로 Link 합니다.</td></tr><tr><td>%.o: %.c</td><td>이 규칙은 42 Norm에서 필수는 아니지만 Makefile의 가독성을 훨씬 높여줍니다. 이 규칙은 모든 .o 파일을 타겟으로 가지며, 동일한 이름을 가진 .c 파일을 선행 조건으로 가집니다.</td></tr><tr><td>clean</td><td>이 규칙은 Build 과정에서 생성된 모든 Object File을 삭제하지만, 생성된 executable은 그대로 두는 간단한 정리(Cleaning) 규칙입니다.</td></tr><tr><td>fclean</td><td>이 규칙 역시 정리 규칙이며, 모든 Object File을 삭제하기 위해 <em>clean</em> 규칙에 의존합니다. 모든 Object File이 삭제되면, 이 규칙은 생성된 executable을 삭제합니다.</td></tr><tr><td>re</td><td>이 규칙은 모든 것을 다시 Build 합니다. 이는 모든 Object File과 executable을 삭제하는 <em>fclean</em> 규칙에 의존합니다. 모든 Object File과 executable이 삭제되면, <em>re</em> 규칙은 <em>all</em> 규칙을 호출하여 모든 것을 다시 Build 합니다.</td></tr></tbody></table>

<details>

<summary>컨벤션을 준수하는 간단한 Makefile (모든 내용 설명됨)</summary>

{% code title="Makefile" lineNumbers="true" %}
```makefile
# 이 Makefile 예시는 일부 변수(variables)와 자동 변수(automatic variables)를 포함합니다.
# 변수와 자동 변수가 없는 동일한 Makefile은 아래에 있습니다.
# NAME은 생성하고자 하는 executable의 이름을 포함하는 변수입니다.
NAME = excutableName

# SRC는 프로젝트를 빌드하는 데 필요한 모든 .c 파일을 포함하는 변수입니다.
# 모든 파일 이름은 공백으로 구분되어야 하며, 저는 모든 파일을 가져오기 위해 
# Wildcard(*.c)를 사용하지 않았습니다.
# Wildcard를 사용하는 것이 더 간단할 수 있지만, 소스 폴더에 원치 않는 파일을 
# 추가함으로써 빌드 프로세스에 쉽게 포함될 수 있기 때문에 42 Norm에서 허용되지 않습니다.
SRC = main.c test.c

# OBJ는 모든 .o 파일을 포함하는 변수입니다.
# 보시다시피 저는 OBJ = 대신 OBJ := 라고 작성했습니다.
# := 와 = 의 차이점은 = 가 우리가 C에서 아는 것처럼 할당(assignment)이라는 것입니다. 
# := 은 make에게 결과를 변수에 추가(append)하도록 지시합니다.
# 오른쪽의 내용이 모든 .c 파일에 대해 실행될 것이기 때문에 결과를 추가해야 합니다.
# 오른쪽의 내용은 기본적으로 "SRC 변수에서 발견하는 모든 .c 파일에 대해, 
# 해당하는 .o 파일을 나에게 줘"라는 의미입니다.
# 그러면 각 .c 파일에 대한 결과가 OBJ 변수에 추가될 것입니다.
# 이것은 단순히 SRC 변수의 각 파일에서 .c를 .o로 대체합니다.
OBJ := $(SRC:%.c=%.o)

# ALL은 Makefile의 첫 번째 규칙이므로, 규칙을 지정하지 않고 make 명령을 
# 호출할 때의 기본 규칙이 됩니다.
# 위 설명 표에서 말했듯이, 이 규칙은 $(NAME) 규칙을 종속성으로 가지며, 
# 따라서 $(NAME) 규칙을 호출할 것입니다.
all: $(NAME)

# $(NAME)은 위에서 언급했듯이 Makefile의 주요 규칙입니다.
# 이 규칙은 OBJ 변수를 종속성으로 가집니다. 이는 하나 이상의 .o 파일이 
# 누락된 경우, make는 $(NAME) 명령을 실행하기 전에 해당 파일들을 
# 빌드하려고 시도할 것임을 의미합니다 (해당 규칙이 존재하는 경우). 
# 모든 종속성이 준비되면 명령이 실행될 것입니다.
# 이 명령은 두 가지 자동 변수, $^ 와 $@ 를 사용합니다.
#
# $^: 모든 선행 조건(prerequisites)의 이름이며, 공백으로 구분됩니다.
# $@: 이는 타겟의 이름에 해당하며, 이 경우 executableName이 될 것입니다.
$(NAME): $(OBJ)
    gcc -Wextra -Wall -Werror $^ -o $@

# %.o 규칙은 하나의 .c 파일을 해당하는 object (.o) 파일로 Compile 합니다.
# %.o: %.c 패턴은 파일 이름이 .o로 끝나는 무언가를 빌드하려면, 
# 동일한 접두사를 가지지만 .o 대신 .c로 끝나는 파일이 필요함을 지정합니다.
# $<: 첫 번째 선행 조건의 이름입니다.
# $@: 이는 타겟의 이름에 해당하며, 이 경우 executableName이 될 것입니다.
%.o: %.c
    gcc -Wextra -Wall -Werror -c $< -o $@

# CLEAN 규칙은 선행 조건이 없습니다.
# 이 규칙은 rm -f 쉘 명령 뒤에 OBJ 변수의 내용, 즉 모든 .o 파일 이름을 
# 실행합니다.
# 그 결과 모든 .o 파일이 삭제됩니다.
clean:
    rm -f $(OBJ)

# FCLEAN 규칙은 CLEAN 규칙을 선행 조건으로 가지며, 이는 CLEAN 규칙이 
# 먼저 실행될 것임을 의미합니다.
# CLEAN 규칙이 완료되면, fclean 명령이 실행될 것입니다.
# 그 결과 모든 .o 파일과 생성된 executable($(NAME)이라는 이름의)이 
# 삭제됩니다.
fclean: clean
    rm -f $(NAME)

# RE 규칙은 FCLEAN과 ALL 규칙을 선행 조건으로 가집니다.
# 선행 조건은 왼쪽에서 오른쪽으로 읽혀지므로, 첫 번째로 실행될 규칙은 
# FCLEAN 규칙이고, 그 다음 ALL 규칙이 실행될 것입니다.
# RE 규칙에는 명령이 없으므로, ALL 규칙이 완료되면 RE 규칙도 완료됩니다.
re: fclean all
```
{% endcode %}

</details>

<details>

<summary>컨벤션을 준수하는 간단한 Makefile (변수 확장됨)</summary>

{% code title="Makefile" lineNumbers="true" %}
```makefile
NAME = excutableName

SRC = main.c test.c
OBJ := $(SRC:%.c=%.o)

all: executableName

executableName: main.o test.o
    gcc -Wextra -Wall -Werror main.o -o executableName

main.o: main.c
    gcc -Wextra -Wall -Werror -c main.c -o main.o

test.o: test.c
    gcc -Wextra -Wall -Werror -c test.c -o test.o

clean:
    rm -f main.o test.o

fclean: clean
    rm -f executableName

re: fclean all
```
{% endcode %}

</details>

### 변수 명명 컨벤션

모든 Makefile에서 예상되는 규칙에 대한 규칙 컨벤션이 있듯이, 변수에 대한 명명 컨벤션도 있습니다. 여기서는 Makefile에서 예상되는 변수들을 설명하고, 명확하지 않은 변수들에 대해 왜 이런 이름으로 명명되었는지 설명하겠습니다.

<table><thead><tr><th width="172">변수 (Variable)</th><th>설명 (Description)</th></tr></thead><tbody><tr><td>NAME</td><td>빌드 타겟 이름 (executable / library / 기타)</td></tr><tr><td>CC</td><td>이 변수는 Compiler를 포함합니다. C의 경우 gcc, C++의 경우 g++ 입니다.</td></tr><tr><td>CCFLAGS</td><td>이 변수는 Compiler Flag를 포함합니다. 예: 42에서는 -Wall -Werror -Wextra를 사용해야 합니다.</td></tr><tr><td>CPPFLAGS</td><td>이 변수는 Compiler Preprocessor Flag를 포함합니다. 예: <br>Include Directory를 지정하는 -I (아래 전체 예시 참조)<br>컴파일 시 Macro를 정의하는 -D MACRO=value (Get Next Line에서 사용됨)</td></tr></tbody></table>

{% hint style="info" %}
CPPFLAGS가 C++ Compiler Flag를 의미하는 것이 아니라, 여기서 "PP"는 PreProcessor를 의미한다는 점에 유의하십시오.
{% endhint %}

### Automatic Variable

Makefile에서 사용할 수 있는 소위 _Automatic Variable_이 많이 있습니다. 저는 가장 많이 사용되는 변수들(즉, 42 Cursus에서 가장 유용할 변수들)만 예시와 함께 설명해 드리겠습니다.

<table><thead><tr><th width="193">Automatic Variable</th><th>설명 (Description)</th></tr></thead><tbody><tr><td>$@</td><td>규칙의 Target 파일 이름입니다.</td></tr><tr><td>$&#x3C;</td><td>첫 번째 Prerequisite(선행 조건)의 이름입니다.</td></tr><tr><td>$?</td><td>Target보다 최신인 모든 Prerequisite의 이름이며, 이름 사이는 공백으로 구분됩니다.<br>Target이 존재하지 않으면 모든 Prerequisite이 포함됩니다.</td></tr><tr><td>$^</td><td>모든 Prerequisite의 이름이며, 이름 사이는 공백으로 구분됩니다.</td></tr></tbody></table>

{% hint style="info" %}
Automatic Variable에 대한 더 자세한 설명은 다음 링크에서 확인하실 수 있습니다:
[https://www.gnu.org/software/make/manual/html\_node/Automatic-Variables.html](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html)
{% endhint %}

<details>

<summary>다른 모든 Automatic Variable 목록</summary>

* $%
* $+
* $|
* $\*
* $(@D)
* $(@F)
* $(\*D)
* $(\*F)
* $(%D)
* $(%F)
* $(\<D)
* $(\<F)
* $(^D)
* $(^F)
* $(+D)
* $(+F)
* $(?D)
* $(?F)

</details>

### 완전한 Makefile 예시 (EXECUTABLE)

아래에서는 Executable을 Compile 하기 위한 완전한 Makefile을 작성하고 각 단계를 설명하겠습니다 (간단한 Makefile에서 했던 것처럼).

이 Makefile에는 _Filename Function_이 포함되어 있는데, 이는 Makefile을 유지 관리하기 쉽게 만들기 위해 Makefile에서 수행할 수 있는 또 다른 기능입니다.

<details>

<summary>완전한 executable Makefile 예시</summary>

{% code title="Makefile" lineNumbers="true" %}
```makefile
# 이 Makefile은 Executable을 Compile 하기 위한 완전한 예시입니다.

# SRC는 모든 필수 Source File 이름을 포함하는 변수입니다.
# 보시다시피, 저는 .c 확장자 없이 파일 이름만 입력했습니다.
# .c 확장자는 addsuffix Filename Function을 사용하여 각 파일 이름에 추가됩니다.
# 이 Filename Function은 다음과 같은 프로토타입을 가집니다.
# $(addsuffix suffix, names...)
# suffix는 두 번째 인수로 추가하는 모든 파일 이름에 추가됩니다.
SRC = $(addsuffix .c, main ft_isalpha ft_isdigit ft_isalnum ft_isascii ft_isprint)

# OBJS는 모든 .o 파일 이름을 포함하는 변수입니다.
# 보시다시피 저는 OBJS = 대신 OBJS := 라고 작성했습니다.
# := 와 = 의 차이점은 = 가 우리가 C에서 아는 것처럼 할당(assignment) 연산자라는 것입니다. 
# := 은 make에게 결과를 변수에 추가(append)하도록 지시합니다.
# 오른쪽의 내용이 각 .c 파일에 대해 순서대로 실행될 것이기 때문에 결과를 추가해야 합니다.
# 오른쪽의 내용은 기본적으로 "SRC 변수에서 발견하는 모든 .c 파일에 대해, 
# 해당하는 .o 파일 이름을 나에게 줘"라는 의미입니다.
# 그러면 각 .c 파일에 대한 결과가 OBJ 변수에 추가될 것입니다.
# 이것은 단순히 SRC 변수의 각 파일에서 .c를 .o로 대체합니다.
OBJS := $(SRC:%.c=%.o)

# BONUS_SRC는 모든 보너스 Source File 이름을 포함하는 변수입니다.
# 보시다시피, 저는 .c 확장자 없이 파일 이름만 입력했습니다.
# .c 확장자는 addsuffix Filename Function을 사용하여 각 파일에 추가됩니다. 
# 저는 또한 이 Filename Function을 사용하여 대부분의 42 주제에서 요구하는 
# _bonus 접미사를 추가합니다.
# 이 Filename Function은 다음과 같은 프로토타입을 가집니다.
# $(addsuffix suffix, names...)
# suffix는 두 번째 인수로 추가하는 모든 파일 이름에 추가됩니다.
# 그런 다음 $(addprefix, names...) Filename Function을 사용하여 보너스 
# 디렉토리에 대한 접두사를 추가합니다.
# $(addprefix, names...)는 addsuffix 함수와 유사하게 작동하지만, 
# 각 파일 이름의 끝에 텍스트를 추가하는 대신 시작 부분에 추가합니다.
BONUS_SRC = $(addprefix bonus/, $(addsuffix _bonus.c, ft_lstnew ft_lstadd_back ft_lstadd_front))

# BONUS_OBJS는 모든 보너스 .o 파일 이름을 포함하는 변수입니다.
# 이것은 OBJS 변수와 동일한 방식으로 작동합니다.
BONUS_OBJS := $(BONUS_SRC:%.c=%.o)

# SUPP_SRC는 모든 추가 Source File을 포함하는 변수입니다.
# BONUS_SRC 변수와 동일한 방식으로 작동하며, .c 접미사를 모든 파일 이름에 
# 추가한 다음, $(addsuffix suffix, names...) 및 
# $(addprefix prefix, names...) Filename Function을 사용하여 supp/ 접두사를 
# 모든 파일 이름에 추가합니다.
SUPP_SRC = $(addprefix supp/, $(addsuffix .c, ft_putchar ft_putstr ft_putnbr))

# SUPP_OBJS는 모든 추가 .o 파일 이름을 포함하는 변수입니다.
# OBJS 및 BONUS_OBJS 변수와 동일한 방식으로 작동합니다.
SUPP_OBJS := $(SUPP_SRC:%.c=%.o)

# Source File 및 Object File에 대해 그랬던 것처럼, 매번 작성하고 싶지 않은 
# 것들을 위해 몇 가지 변수를 선언했습니다.

# NAME은 생성하고자 하는 executable의 이름을 포함하는 변수입니다.
NAME = executable

# CC는 Compiler를 포함하는 변수입니다.
# 일반적으로 C의 경우 gcc, C++의 경우 g++ 입니다.
CC = gcc

# CCFLAGS는 Compiler Flag를 포함하는 변수입니다.
# -Wall : 이 플래그는 모든 구조적 경고(warnings)를 활성화합니다.
# -Wextra : 이 플래그는 -Wall에 의해 활성화되지 않는 일부 추가 경고 플래그를 활성화합니다.
# -Werror : 이 플래그는 모든 경고를 오류로 전환하여 Compiler가 경고를 만났을 때 중단시킵니다.
# 이 플래그들을 사용할 때 실제로 활성화되는 플래그와 각 플래그가 무엇을 하는지에 대한 
# 자세한 내용은 다음 링크에서 확인할 수 있습니다.
# https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#Warning-Options
CCFLAGS = -Wall -Werror -Wextra

# INC_DIR은 Include Directory의 경로를 포함하는 변수입니다.
# Include Directory는 .c 파일에 포함된 모든 .h 파일을 넣는 데 사용됩니다.
# 파일을 Compile 할 때 Include Directory를 Preprocessor Flag로 포함하면, 
# Header File을 포함할 때마다 경로를 지정할 필요가 없습니다.
# #include "main.h" 대신 #include "../include/main.h"와 같이 작성할 수 있습니다.
INC_DIR = .

# CPPFLAGS는 Preprocessor Flag를 포함하는 변수입니다.
# 이 예시에서는 INC_DIR 변수에서 가져온 Include Directory 경로를 지정하는 
# -I 플래그만 포함합니다.
CPPFLAGS = -I$(INC_DIR)

# OBJS_BASBO는 필수 Source File과 보너스 Source File의 모든 Object File 
# 이름을 포함하는 변수입니다.
OBJS_BASBO = $(OBJS) $(BONUS_OBJS)

# OBJS_ALL은 필수, 보너스 및 추가 Source File의 모든 Object File 이름을 
# 포함하는 변수입니다.
OBJS_ALL = $(OBJS_BASBO) $(SUPP_OBJS)

# RM은 rm -f 쉘 명령에 대한 Alias 변수입니다.
# 삭제해야 할 때마다 rm -f를 작성하는 대신 이를 사용할 수 있기 때문에 
# Alias 변수라고 말합니다.
# -f 옵션을 넣지 않으면 rm 함수를 사용할 때 파일이 존재하지 않으면 오류가 
# 발생하지만, -f는 삭제할 파일이 존재하든 안 하든 실행을 강제합니다.
RM = rm -f

# ALL은 Makefile의 첫 번째 규칙이므로, 규칙을 지정하지 않고 make 명령을 
# 호출할 때의 기본 규칙이 됩니다.
# 이 규칙은 $(NAME) 규칙을 종속성으로 가지며, 따라서 $(NAME) 규칙을 호출할 것입니다.
all: $(NAME)

# $(NAME)은 Makefile의 주요 규칙입니다.
# 이 규칙은 OBJS 변수를 종속성으로 가집니다. 이는 하나 이상의 .o 파일이 
# 누락된 경우, make는 $(NAME) 명령을 실행하기 전에 해당 파일들을 
# 빌드하려고 시도할 것임을 의미합니다 (해당 규칙이 존재하는 경우).
# 모든 종속성이 준비되면 명령이 실행될 것입니다.
# 실행되는 명령은 gcc Linking이며, 모든 .o 파일을 $(NAME)이라는 
# Executable 파일로 Link 합니다.
$(NAME): $(OBJS)
	$(CC) $(CCFLAGS) $^ -o $@

# %.o 규칙은 하나의 .c 파일을 해당하는 object (.o) 파일로 Compile 합니다.
# %.o: %.c 패턴은 파일 이름이 .o로 끝나는 무언가를 빌드하려면, 
# 동일한 접두사를 가지지만 .o 대신 .c로 끝나는 파일이 필요함을 지정합니다.
# $<: 첫 번째 선행 조건의 이름입니다.
# $@: 이는 타겟의 이름, 즉 콜론의 왼쪽에 있는 것에 해당합니다.
%.o: %.c
	$(CC) $(CPPFLAGS) $(CCFLAGS) -c $< -o $@


# CLEAN 규칙은 선행 조건이 없습니다.
# 이 규칙은 $(RM) Alias 변수 뒤에 OBJS_ALL 변수의 내용, 즉 모든 .o 파일 이름을 
# 실행합니다.
# 그 결과 모든 .o 파일이 삭제됩니다.
clean:
	$(RM) $(OBJS_ALL)

# FCLEAN 규칙은 CLEAN 규칙을 선행 조건으로 가지며, 이는 CLEAN 규칙이 
# 먼저 실행될 것임을 의미합니다.
# CLEAN 규칙이 완료되면, fclean 명령이 실행될 것입니다.
# 그 결과 모든 .o 파일과 생성된 executable($(NAME)이라는 이름의)이 
# 삭제됩니다.
fclean: clean
	$(RM) $(NAME)

# RE 규칙은 FCLEAN과 ALL 규칙을 선행 조건으로 가집니다.
# 선행 조건은 왼쪽에서 오른쪽으로 읽혀지므로, 첫 번째로 실행될 규칙은 
# FCLEAN 규칙이고, 그 다음 ALL 규칙이 실행될 것입니다.
# RE 규칙에는 명령이 없으므로, ALL 규칙이 완료되면 RE 규칙도 완료됩니다.
re: fclean all

# BONUS 규칙은 $(NAME) 규칙과 동일한 방식으로 작동하지만, 종속성으로 
# OBJS 변수 대신 OBJS_BASBO 변수를 가집니다.
# OBJS_BASBO 변수는 OBJS와 BONUS_OBJS 변수를 포함하므로, 이 두 변수에 포함된 
# 모든 .o 파일은 이 보너스 규칙의 종속성입니다.
bonus: $(OBJS_BASBO)
	$(CC) $(CCFLAGS) $? -o $(NAME)

# EVERYTHING 규칙은 $(NAME) 규칙과 동일한 방식으로 작동하지만, 종속성으로 
# OBJS 변수 대신 OBJS_ALL 변수를 가집니다.
# OBJS_ALL 변수는 OBJS, BONUS_OBJS 및 SUPP_OBJS 변수를 포함하므로, 이 세 변수에 
# 포함된 모든 .o 파일은 이 everything 규칙의 종속성입니다.
everything: $(OBJS_ALL)
	$(CC) $(CCFLAGS) $? -o $(NAME)

# .PHONY 타겟은 파일에 의존하지 않는 타겟을 지정합니다.
# 자세한 내용은 이 페이지 아래의 ".PHONY targets" 섹션을 참조하십시오.
.PHONY: all clean fclean re bonus everything
```
{% endcode %}

</details>

### 완전한 Makefile 예시 (LIBRARY)

아래에서는 Library를 Compile 하기 위한 완전한 Makefile을 작성하고 각 단계를 설명하겠습니다 (간단한 Makefile에서 했던 것처럼).

이 Makefile에는 _Filename Function_이 포함되어 있는데, 이는 Makefile을 유지 관리하기 쉽게 만들기 위해 Makefile에서 수행할 수 있는 또 다른 기능입니다.

<details>

<summary>완전한 library Makefile 예시</summary>

<pre class="language-makefile" data-title="Makefile" data-line-numbers><code class="lang-makefile"># 이 Makefile은 C Library를 Compile 하기 위한 완전한 예시입니다.

# SRC는 모든 필수 Source File을 포함하는 변수입니다.
# 보시다시피, 저는 .c 확장자 없이 파일 이름만 입력했습니다.
# .c 확장자는 addsuffix Filename Function을 사용하여 각 파일에 추가됩니다.
# 이 Filename Function은 다음과 같은 프로토타입을 가집니다.
# $(addsuffix suffix, names...)
# suffix는 두 번째 인수로 추가하는 모든 파일 이름에 추가됩니다.
<strong>SRC = $(addsuffix .c, ft_isalpha ft_isdigit ft_isalnum ft_isascii ft_isprint)
</strong>
# OBJS는 모든 .o 파일 이름을 포함하는 변수입니다.
# 보시다시피 저는 OBJS = 대신 OBJS := 라고 작성했습니다.
# := 와 = 의 차이점은 = 가 우리가 C에서 아는 것처럼 할당(assignment) 연산자라는 것입니다. 
# := 은 make에게 결과를 변수에 추가(append)하도록 지시합니다.
# 오른쪽의 내용이 각 .c 파일에 대해 순서대로 실행될 것이기 때문에 결과를 추가해야 합니다.
# 오른쪽의 내용은 기본적으로 "SRC 변수에서 발견하는 모든 .c 파일에 대해, 
# 해당하는 .o 파일 이름을 나에게 줘"라는 의미입니다.
# 그러면 각 .c 파일에 대한 결과가 OBJ 변수에 추가될 것입니다.
# 이것은 단순히 SRC 변수의 각 파일에서 .c를 .o로 대체합니다.
OBJS := $(SRC:%.c=%.o)

# BONUS_SRC는 모든 보너스 Source File을 포함하는 변수입니다.
# 보시다시피, 저는 .c 확장자 없이 파일 이름만 입력했습니다.
# .c 확장자는 addsuffix Filename Function을 사용하여 각 파일에 추가됩니다.
# 이 Filename Function은 다음과 같은 프로토타입을 가집니다.
# $(addsuffix suffix, names...)
# suffix는 두 번째 인수로 추가하는 모든 파일 이름에 추가됩니다.
# 그런 다음 $(addprefix, names...) Filename Function을 사용하여 보너스 
# 디렉토리에 대한 접두사를 추가합니다.
# $(addprefix, names...)는 addsuffix 함수와 유사하게 작동하지만, 
# 각 파일 이름의 끝에 텍스트를 추가하는 대신 시작 부분에 추가합니다.
BONUS_SRC = $(addprefix bonus/, $(addsuffix _bonus.c, ft_lstnew ft_lstadd_back ft_lstadd_front ))

# BONUS_OBJS는 모든 보너스 .o 파일 이름을 포함하는 변수입니다.
# 이것은 OBJS 변수와 동일한 방식으로 작동합니다.
BONUS_OBJS := $(BONUS_SRC:%.c=%.o)

# SUPP_SRC는 모든 추가 Source File을 포함하는 변수입니다.
# BONUS_SRC 변수와 동일한 방식으로 작동하며, .c 접미사를 모든 파일 이름에 
# 추가한 다음, supp/ 접두사를 모든 파일 이름에 추가합니다.
SUPP_SRC = $(addprefix supp/, $(addsuffix .c, ft_putchar ft_putstr ft_putnbr))

# SUPP_OBJS는 모든 추가 .o 파일 이름을 포함하는 변수입니다.
# OBJS 및 BONUS_OBJS 변수와 동일한 방식으로 작동합니다.
SUPP_OBJS := $(SUPP_SRC:%.c=%.o)

# Source File 및 Object File에 대해 그랬던 것처럼, 매번 작성하고 싶지 않은 
# 것들을 위해 몇 가지 변수를 선언했습니다.

# NAME은 생성하고자 하는 Archive의 이름을 포함하는 변수입니다.
NAME = libft.a

# CC는 Compiler를 포함하는 변수입니다.
# 일반적으로 C의 경우 gcc, C++의 경우 g++ 입니다.
CC = gcc

# CCFLAGS는 Compiler Flag를 포함하는 변수입니다.
# -Wall : 이 플래그는 모든 구조적 경고(warnings)를 활성화합니다.
# -Wextra : 이 플래그는 -Wall에 의해 활성화되지 않는 일부 추가 경고 플래그를 활성화합니다.
# -Werror : 이 플래그는 모든 경고를 오류로 전환하여 Compiler가 경고를 만났을 때 중단시킵니다.
# 이 플래그들을 사용할 때 실제로 활성화되는 플래그와 각 플래그가 무엇을 하는지에 대한 
# 자세한 내용은 다음 링크에서 확인할 수 있습니다.
# https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html#Warning-Options
CCFLAGS = -Wall -Wextra -Werror

# INC_DIR은 Include Directory의 경로를 포함하는 변수입니다.
# Include Directory는 Library에 필요한 모든 .h 파일을 넣는 데 사용됩니다.
# 파일을 Compile 할 때 Include Directory를 Preprocessor Flag로 포함하면, 
# Header File을 포함할 때마다 경로를 지정할 필요가 없습니다. 
# #include "main.h" 대신 #include "../include/main.h"와 같이 작성할 수 있습니다.
INC_DIR = .

# CPPFLAGS는 Preprocessor Flag를 포함하는 변수입니다.
# 이 예시에서는 INC_DIR 변수에서 가져온 Include Directory 경로를 지정하는 
# -I 플래그만 포함합니다.
CPPFLAGS = -I$(INC_DIR)

# RM은 rm -f 쉘 명령에 대한 Alias 변수입니다.
# 삭제해야 할 때마다 rm -f를 작성하는 대신 이를 사용할 수 있기 때문에 
# Alias 변수라고 말합니다.
# -f 옵션을 넣지 않으면 rm 함수를 사용할 때 파일이 존재하지 않으면 오류가 
# 발생하지만, -f는 삭제할 파일이 존재하든 안 하든 실행을 강제합니다.
RM = rm -f

# ARNAME은 ar rcs $(NAME) 쉘 명령을 위한 Alias 변수입니다.
ARNAME = ar rcs $(NAME)

# RANNAME은 ranlib $(NAME) 쉘 명령을 위한 Alias 변수입니다.
RANNAME = ranlib $(NAME)

# OBJS_BASBO는 필수 Source File과 보너스 Source File의 모든 Object File 
# 이름을 포함하는 변수입니다.
OBJS_BASBO = $(OBJS) $(BONUS_OBJS)

# OBJS_ALL은 필수, 보너스 및 추가 Source File의 모든 Object File 이름을 
# 포함하는 변수입니다.
OBJS_ALL = $(OBJS_BASBO) $(SUPP_OBJS)

# ALL은 Makefile의 첫 번째 규칙이므로, 규칙을 지정하지 않고 make 명령을 
# 호출할 때의 기본 규칙이 됩니다.
# 이 규칙은 $(NAME) 규칙을 종속성으로 가지며, 따라서 $(NAME) 규칙을 호출할 것입니다.
all: $(NAME)

# $(NAME)은 Makefile의 주요 규칙입니다.
# 이 규칙은 OBJS 변수를 종속성으로 가집니다. 이는 하나 이상의 .o 파일이 
# 누락된 경우, make는 $(NAME) 명령을 실행하기 전에 해당 파일들을 
# 빌드하려고 시도할 것임을 의미합니다 (해당 규칙이 존재하는 경우).
# 모든 종속성이 준비되면 명령이 실행될 것입니다.
# 이 명령들은 변수 선언 섹션에서 위에서 설명된 내용입니다.
# ARNAME : ar rcs $(NAME)
# RANNAME : ranlib $(NAME)
$(NAME): $(OBJS)
    $(ARNAME) $(OBJS)
    $(RANNAME)

# %.o 규칙은 하나의 .c 파일을 해당하는 object (.o) 파일로 Compile 합니다.
# %.o: %.c 패턴은 파일 이름이 .o로 끝나는 무언가를 빌드하려면, 
# 동일한 접두사를 가지지만 .o 대신 .c로 끝나는 파일이 필요함을 지정합니다.
# $<: 첫 번째 선행 조건의 이름입니다.
# $@: 이는 타겟의 이름, 즉 콜론의 왼쪽에 있는 것에 해당합니다.
%.o: %.c
    $(CC) $(CPPFLAGS) $(CCFLAGS) -o $@ -c $<

# CLEAN 규칙은 선행 조건이 없습니다.
# 이 규칙은 $(RM) Alias 변수 뒤에 OBJS_ALL 변수의 내용, 즉 모든 .o 파일 이름을 
# 실행합니다.
# 그 결과 모든 .o 파일이 삭제됩니다.
clean:
    $(RM) $(OBJS_ALL)

# FCLEAN 규칙은 CLEAN 규칙을 선행 조건으로 가지며, 이는 CLEAN 규칙이 
# 먼저 실행될 것임을 의미합니다. 
# CLEAN 규칙이 완료되면, fclean 명령이 실행될 것입니다.
# 그 결과 모든 .o 파일과 생성된 Library($(NAME)이라는 이름의)가 삭제됩니다.
fclean: clean
    $(RM) $(NAME)

# RE 규칙은 FCLEAN과 ALL 규칙을 선행 조건으로 가집니다.
# 선행 조건은 왼쪽에서 오른쪽으로 읽혀지므로, 첫 번째로 실행될 규칙은 
# FCLEAN 규칙이고, 그 다음 ALL 규칙이 실행될 것입니다.
# RE 규칙에는 명령이 없으므로, ALL 규칙이 완료되면 RE 규칙도 완료됩니다.
re: fclean all

# BONUS 규칙은 $(NAME) 규칙과 동일한 방식으로 작동하지만, 종속성으로 
# OBJS 변수 대신 OBJS_BASBO 변수를 가집니다.
# OBJS_BASBO 변수는 OBJS와 BONUS_OBJS 변수를 포함하므로, 이 두 변수에 포함된 
# 모든 .o 파일은 이 보너스 규칙의 종속성입니다.
# 이 명령들은 변수 선언 섹션에서 위에서 설명된 내용입니다.
# ARNAME : ar rcs $(NAME)
# RANNAME : ranlib $(NAME)
bonus: $(OBJS_BASBO)
    $(ARNAME) $(OBJS_BASBO)
    $(RANNAME)

# EVERYTHING 규칙은 $(NAME) 규칙과 동일한 방식으로 작동하지만, 종속성으로 
# OBJS 변수 대신 OBJS_ALL 변수를 가집니다.
# OBJS_ALL 변수는 OBJS, BONUS_OBJS 및 SUPP_OBJS 변수를 포함하므로, 이 세 변수에 
# 포함된 모든 .o 파일은 이 everything 규칙의 종속성입니다.
# 이 명령들은 변수 선언 섹션에서 위에서 설명된 내용입니다.
# ARNAME : ar rcs $(NAME)
# RANNAME : ranlib $(NAME)
everything: $(OBJS_ALL)
    $(ARNAME) $(OBJS_ALL)
    $(RANNAME)
</code></pre>

</details>

### .PHONY 타겟

Phony Target은 실제로 파일 이름이 아닌 타겟입니다. 이는 명시적인 요청(예: `make clean`)을 할 때 실행되어야 하는 레시피(recipe)의 이름일 뿐입니다.

Phony Target을 사용하는 이유는 두 가지입니다:

* 동일한 이름의 파일과의 충돌을 피하기 위함입니다.
* 성능을 개선하기 위함입니다.

레시피가 타겟 파일을 생성하지 않는 규칙을 작성하면, 타겟이 Re-making을 위해 등장할 때마다 레시피가 실행될 것입니다. 다음은 예시입니다:

{% code title="Makefile" lineNumbers="true" %}
```makefile
clean:
    rm -f *.o
```
{% endcode %}

rm Command는 clean이라는 이름의 파일을 생성하지 않으므로, 아마도 그러한 파일은 존재하지 않을 것입니다.

따라서 `make clean`을 실행할 때마다 rm Command가 실행될 것입니다.

이 예시에서, clean이라는 이름의 파일이 이 디렉토리에 생성되면 clean 타겟은 예상대로 작동하지 않을 것입니다. 선행 조건이 없기 때문에 clean은 항상 최신 상태로 간주되어 레시피가 실행되지 않을 것입니다.

이러한 문제를 피하려면, 다음과 같이 특별한 .PHONY 타겟의 Prerequisite으로 선언하여 타겟을 Phony로 명시적으로 선언할 수 있습니다:

```
.PHONY: clean
clean:
    rm -f *.o
```

이렇게 하면 clean이라는 파일이 존재하더라도 `make clean`은 레시피를 실행할 것입니다.

#### 출처 (Sources)

제가 작성한 모든 내용은 다음 링크에서 더 자세히 확인할 수 있습니다.

{% embed url="https://www.gnu.org/software/make/manual/html_node/" %}
GNU Make Documentation
{% endembed %}
