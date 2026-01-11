---
description: 이 페이지에서는 Header 파일에서 사용되는 몇 가지 개념을 설명합니다.
---

# 🏁 Header 파일

### Header 파일이란 무엇입니까?

Header 파일은 확장자가 `.h`인 파일이며, 여러 소스 파일 간에 공유될 C 함수 선언(declaration)과 Macro 정의를 포함합니다.

Header 파일은 함수의 정의(definition)가 아닌, 함수의 선언(declaration)만을 포함한다는 점에 유의하십시오.

{% code overflow="wrap" lineNumbers="true" %}
```c
void    ft_putchar(char c);
void    ft_putstr(char *str);
size_t    ft_strlen(char *str);
```
{% endcode %}

선언은 단지 함수의 Prototype입니다.
Header 파일을 포함하는 C 소스 파일을 컴파일할 때, 이 Prototype들은 소스 파일에게 Header에 선언된 함수가 존재하며, 해당 함수의 Parameter가 무엇이고, 어떤 유형의 데이터를 반환하는지 알려줍니다.

이러한 함수들의 정의는 원하는 만큼 많은 C 소스 파일에 작성될 수 있습니다.

즉, `LIBFT` 프로젝트는 모든 함수의 선언을 담고 있는 하나의 Header 파일을 가지고 있지만, 코드를 가독성 있게 유지하기 위해 각 함수는 별도의 파일에 작성되었고, 우리는 모든 소스 파일에 동일한 Header 파일을 포함합니다.

### Header 보호 (Header Protection)

Norm을 통과하려고 할 때 "HEADER PROT"(또는 이와 유사한 것)이라고 불리는 문제로 Header 파일에 어려움을 겪었을 가능성이 높습니다. 하지만 이것은 도대체 무엇일까요?

Header Protection은 전체 Header 파일을 감싸는 코드입니다.

{% code title="header_example.h" overflow="wrap" lineNumbers="true" %}
```c
#ifndef HEADER_EXAMPLE_H
# define HEADER_EXAMPLE_H

// 사용자 함수의 Prototype들이 여기에 위치합니다
// 구조체(Structure) / typedef도 여기에 위치합니다

#endif
```
{% endcode %}

이 코드가 정확히 무엇을 하는지 설명하기 전에, Header를 포함하는 소스 파일이 컴파일될 때 어떻게 보이는지 먼저 보여드리겠습니다.

`LIBFT`를 예시로 계속 사용하겠습니다.

{% code title="libft.h" overflow="wrap" lineNumbers="true" %}
```c
size_t ft_strlen(char *s);
int ft_isdigit(int c);
```
{% endcode %}

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
// 이 코드는 컴파일 전에 보이는 모습입니다.

#include "libft.h"

size_t ft_strlen(char *s)
{
    size_t i;
    
    i = 0;
    while (s[i])
        i++;
    return (i);
}
```
{% endcode %}

{% code title="ft_isdigit.c" overflow="wrap" lineNumbers="true" %}
```c
// 이 코드는 컴파일 전에 보이는 모습입니다.

#include "libft.h"

int ft_isdigit(int c)
{
    return (c >= 48 && c <= 57);
}
```
{% endcode %}

위에서 저는 3개의 파일, 즉 하나의 Header와 동일한 Header 파일을 모두 포함하는 두 개의 소스 파일을 작성했습니다. 위에 있는 것은 컴파일 전 파일의 모습이며, 아래에 있는 것은 컴파일 후 동일한 파일의 모습입니다.

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
size_t ft_strlen(char *s);
int ft_isdigit(int c);

size_t ft_strlen(char *s)
{
    size_t i;
    
    i = 0;
    while (s[i])
        i++;
    return (i);
}
```
{% endcode %}

{% code title="ft_isdigit.c" overflow="wrap" lineNumbers="true" %}
```c
size_t ft_strlen(char *s);
int ft_isdigit(int c);

int ft_isdigit(int c)
{
    return (c >= 48 && c <= 57);
}
```
{% endcode %}

보시다시피, Header 파일의 전체 내용이 각 파일의 상단(컴파일 전에 `include` 구문을 넣었던 위치)에 붙여 넣어졌습니다. 하지만 두 파일 모두 동일한 라이브러리로 함께 컴파일되므로, 두 파일 모두에(또는 Header를 포함했던 모든 파일에) 이를 포함할 필요는 없습니다. 소스 파일 중 하나에만 포함하는 것으로 충분합니다. 이것이 바로 Header Protection의 핵심 목적입니다. 즉, Header를 오직 한 번만 포함하도록 하는 것입니다.

Header Protection을 사용하여 동일한 Header를 다음과 같이 작성하면:

{% code title="libft.h" overflow="wrap" lineNumbers="true" %}
```c
#ifndef LIBFT_H
# define LIBFT_H

size_t ft_strlen(char *s);
int ft_isdigit(int c);

#endif
```
{% endcode %}

컴파일 후 소스 파일은 다음과 같이 보일 것입니다.

{% code title="ft_strlen.c" overflow="wrap" lineNumbers="true" %}
```c
size_t ft_strlen(char *s);
int ft_isdigit(int c);

size_t ft_strlen(char *s)
{
    size_t i;
    
    i = 0;
    while (s[i])
        i++;
    return (i);
}
```
{% endcode %}

{% code title="ft_isdigit.c" overflow="wrap" lineNumbers="true" %}
```c
int ft_isdigit(int c)
{
    return (c >= 48 && c <= 57);
}
```
{% endcode %}

이번에는 Header가 왜 한 번만 포함되었을까요?

Header Protection은 다소 특이한 문법을 가지고 있습니다. 한 줄씩 살펴보겠습니다.

```c
#ifndef LIBFT_H
```

이 첫 번째 줄은 `LIBFT_H가 정의되지 않았다면(if not defined LIBFT_H)`을 의미합니다. Preprocessor는 Macro인 `LIBFT_H`가 정의되었는지 여부를 확인합니다. 정의되지 않았다면, 이 구문과 Header 파일의 마지막 구문 사이에 있는 모든 것을 컴파일에 포함시킵니다.

```c
# define LIBFT_H
```

이는 Macro 정의이며, 다른 값에 대해 정의하는 방식과 동일합니다. 이 Macro의 이름은 특정 형식을 따라야 합니다. 정확히 무엇을 따라야 하는지는 Norm을 확인해 보십시오. 파일 이름이 `libft.h`이고 `example_header.h`에 대한 Macro가 `EXAMPLE_HEADER_H`라는 점을 기억하시면, 쉽게 유추하실 수 있을 것입니다.

왜 이를 정의해야 할까요? 다음에 Header 파일을 포함할 때 이 Macro가 정의되어 있을 것이므로, `ifndef`와 `endif` 사이의 모든 내용이 처음부터 없었던 것처럼 건너뛰어지기 때문입니다. 이렇게 하면 전체 소스 파일 중 단 하나의 소스 파일 상단에만 Header 파일의 내용을 포함하게 됩니다.

마지막으로, Header Protection의 마지막 구문입니다.

```c
#endif
```

이 구문은 Header 파일 상단에서 `ifndef`로 열었던 `if` 문을 종료합니다.

#### 요약 (TLDR)

Header Protection은 모든 Header 파일 내용을 감싸는 Wrapper로서, 이 Header의 내용을 모든 파일이 아닌 오직 하나의 소스 파일에만 포함하도록 하는 역할을 합니다.

Header Protection의 문법은 다음과 같습니다.

```c
#ifndef FILENAME_H
# define FILENAME_H

// 다른 include 파일들 (stdio.h, stdlib.h 등)
// 사용자 함수의 선언
// 구조체(Structure)의 선언

#endif
```

### Macro

Header 파일의 Macro는 `#define` 구문으로 선언하는 것입니다.

예를 들어, 생성하려는 Window의 높이를 담는 `WIN_H` Macro나, `read()` 함수를 호출할 때마다 프로그램이 읽어야 하는 문자 수를 알려주는 `BUFFER_SIZE` Macro를 정의할 수 있습니다 (이는 [`get_next_line`](../rank-01/get_next_line/) 프로젝트에서 수행해야 하는 작업입니다).

다음은 그 예시입니다.

{% code title="example_header.h" overflow="wrap" lineNumbers="true" %}
```c
#ifndef EXAMPLE_HEADER_H
# define EXAMPLE_HEADER_H
# define WIN_H 720
# define WIN_W 1280
# define BUFFER_SIZE 42

char *get_next_line(int fd);

#endif
```
{% endcode %}

여러 화면 해상도를 미리 정의하고, `define` 구문을 주석 처리하거나 해제함으로써 그중 하나를 선택할 수 있습니다. 예를 들면 다음과 같습니다.

{% code title="example_header.h" overflow="wrap" lineNumbers="true" %}
```c
#ifndef EXAMPLE_HEADER_H
# define EXAMPLE_HEADER_H
//# define 720P
# define 360P
# define BUFFER SIZE 42

# ifdef 720P
#  define WIN_W 1280
#  define WIN_H 720
# endif

# ifdef 360P
#  define WIN_W 640
#  define WIN_H 360
# endif

# include <stdio.h>

char *get_next_line(int fd);

#endif
```
{% endcode %}

위의 Header 파일은 `WIN_W` Macro를 `640`으로, `WIN_H` Macro를 `360`으로 설정할 것입니다.

#### Macro는 무엇에 사용됩니까?

정의된 Macro는 Header가 포함된 모든 소스 파일에서 사용될 수 있습니다.

사용법은 매우 간단합니다. Window 크기의 예를 들어보면 다음과 같이 작성할 수 있습니다.

{% code title="main.c" overflow="wrap" lineNumbers="true" %}
```c
#include "example_header.h"

int main(void)
{
    printf("Window Height: %d\n", WIN_H);
    printf("Window Width : %d\n", WIN_W);
    return (0);
}
```
{% endcode %}

위 소스 파일을 컴파일하면 Preprocessor가 Macro 이름을 Header 파일에 설정한 값으로 대체하게 됩니다.

이 예시에서 `WIN_H`는 360으로, `WIN_W`는 640으로 대체됩니다. 따라서 이 프로그램을 실행했을 때 예상되는 결과는 다음과 같습니다.

{% code overflow="wrap" %}
```shell
$> ./a.out
$> Window Height: 360
$> Window Width : 640
$>
```
{% endcode %}