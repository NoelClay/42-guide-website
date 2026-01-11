# ft\_strtrim

### 주제 (Subject)

{% code overflow="wrap" %}
```
FT_STRTRIM (간소화)

NAME
    ft_strtrim -- 문자열에서 문자 셋을 자릅니다 (trim)
SYNOPSIS
    char *ft_strtrim(const char *s1, const char *set);
DESCRIPTION
    s1의 시작과 끝에 지정된 set에 포함된 문자 없이 s1의 복사본을 할당하고 (malloc(3) 사용) 반환합니다.
PARAMETERS
    s1: 다듬을 (trim) 문자열
    set: 다듬을 (trim) 문자들
RETURN VALUES
    ft_strtrim()은 다듬어진 (trimmed) s1의 복사본을 반환합니다. 메모리 할당에 실패한 경우 NULL을 반환합니다.
AUTHORIZED EXTERNAL FUNCTIONS
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명 (Understandable explanation)

`ft_strtrim()` 함수는 문자열을 받아서 다듬습니다 (trim).

다듬는다는 것은 무슨 의미일까요? 설명해 드리겠습니다.

다듬기(Trimming)란 `s1` 문자열의 시작 **과** 끝에서 `set` 문자열에 지정된 문자들을 제거하는 것을 의미하며, `s1`의 중간에 있는 `set` 문자들은 제거하지 않습니다.

만약 `ababaaaMy name is Simonbbaaabbad` 문자열과 `ab` 셋을 가지고 있다면, `ft_strtrim()` 함수를 통해 얻게 될 결과는 `My name is Simon`입니다.

저희는 `s1`의 시작과 끝에 있는 모든 `a`와 `b`를 제거했지만, `s1` 중간에 있는 `a`는 건드리지 않았습니다.

### 힌트 (Hints)

우리는 `s1`의 시작과 끝에서 문자들을 제거해야 합니다. 그러므로 문자열을 반복하면서 제거해야 할 문자가 있는 동안 계속 인덱스를 전진시키는 것이 어떨까요?

그리고 문자열의 끝에서부터 같은 작업을 수행하여, 다듬어진 문자열의 시작 및 끝 인덱스를 확보합니다.

이것이 기본적으로 제가 이 작업을 수행한 방식이며, 제 코드가 작동하는 방식입니다.

### 주석이 달린 해답 (Commented solution)

<details>

<summary>ft_strtrim</summary>

{% code title="ft_strtim.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

static int to_trim(const char *set, char c);
static char *new_str(const char *s1, size_t start, size_t end);

char *ft_strtrim(const char *s1, const char *set)
{
    int i;
    int j;
    
    i = 0;
    j = ft_strlen(s1) - 1;
    /* 문자열의 길이가 0이라면 다듬을 (trim) 내용이 없으므로,
     * 해제 가능한 빈 문자열을 반환합니다.
     */
    if (ft_strlen(s1) == 0)
        return (ft_strdup(""));
    /* 먼저 문자열의 시작부터 다듬어야 할 set에 포함되지 않는 첫 번째 문자를
     * 찾을 때까지 반복합니다.
     */
    while (to_trim(set, s1[i]))
        i++;
    /* 그런 다음, 이번에는 문자열의 끝에서부터 같은 작업을 수행합니다.
     */
    while (to_trim(set, s1[j]))
        j--;
    /* 이제, 변수들이 의미하는 값은 다음과 같습니다:
     * i : 전체 문자열에서 다듬어진 문자열의 시작 인덱스
     * j : 전체 문자열에서 다듬어진 문자열의 끝 인덱스
     * 위에 언급된 예시를 사용했을 때의 값은 다음과 같습니다.
     * s1 : "ababaaaMy name is Simonbbaaabbad"
     * ft_strlen(s1) : 33
     * 양쪽을 반복한 후에는 다음과 같은 값을 얻습니다:
     * i : 8
     * j : 23
     * 하지만 저희는 끝 인덱스를 전달하는 대신, 새로운 문자열의 길이를 전달해야 하므로
     * 다음과 같은 "계산"을 수행합니다:
     * j - (i - 1) : 23 - (8 - 1) = 23 - 7 = 16
     */
    return (new_str(s1, i, j - (i - 1)));
}

/* 이 함수는 헬퍼 함수입니다. 전달된 세 개의 인자를 기반으로
 * 새로운 문자열을 생성하고 할당합니다.
 */
static char *new_str(const char *s1, size_t start, size_t len)
{
    char *str;
    size_t i;
    
    /* 먼저, 생성할 문자열의 길이가 0이거나
     * 새로운 문자열의 시작이 원본 문자열의 끝보다 뒤에 있는지 확인합니다.
     * 이 경우, 나중에 해제할 수 있는 빈 문자열을 생성하기 위해 ft_strdup을 호출합니다.
     */
    if (len <= 0 || start >= ft_strlen(s1))
        return (ft_strdup(""));
    /* 그런 다음, 새로운 문자열과 NUL-종료 문자를 위한 충분한 메모리를 할당하기 위해
     * ft_calloc을 호출합니다.
     * 함수 끝에서 문자열을 NUL-종료해야 하는지에 대해 신경 쓸 필요가 없도록
     * malloc 대신 calloc을 사용합니다.
     */
    str = ft_calloc(len + 1, sizeof(char));
    if (!str)
        return (NULL);
    i = 0;
    /* i가 새 문자열의 길이보다 작은 동안 반복합니다.
     */
    while (i < len)
    {
        /* 여기서 저희는 s1의 start + i 인덱스에 있는 내용을
         * 새 문자열의 i 인덱스에 복사합니다. 그 이유는 무엇일까요?
         * 스스로 찾아보세요. 이를 설명하는 스키마도 아래에 있습니다.
         */
        str[i] = s1[start + i];
        i++;
    }
    /* 생성한 새 문자열을 반환합니다.
     */
    return (str);
}

/* 이 헬퍼 함수는 문자 c가 다듬을 문자 셋에 포함되어 있으면 1을 반환하고,
 * 그렇지 않으면 0을 반환합니다.
 */
static int to_trim(const char *set, char c)
{
    int i;
    
    i = 0;
    /* 전체 set을 반복하여 문자 c를 다듬을 문자 셋의 모든 문자와
     * 비교하도록 합니다.
     */
    while (set[i])
    {
        if (c == set[i])
            return (1);
        i++;
    }
    return (0);
}
```
{% endcode %}

</details>
