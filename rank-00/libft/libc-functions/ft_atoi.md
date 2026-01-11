# ft\_atoi

### 주제

{% code overflow="wrap" %}
```
ATOI(3) (simplified)

NAME
    atoi -- convert ASCII string to integer
SYNOPSIS
    int atoi(const char *str);
DESCRIPTION
    The atoi() function converts the initial portion of the string pointed to by str to int representation.
```
{% endcode %}

### 이해하기 쉬운 설명

`atoi()` 함수는 문자열을 해당 `int` 표현으로 변환합니다.

`atoi()` 함수가 수행하는 일부 동작들은 man 페이지에 명확하게 설명되어 있지 않습니다. 여기에 간단히 정리합니다.

*   매개변수로 전달된 문자열은 `isspace(3)`에 의해 결정되는 임의의 개수의 공백(whitespace)으로 시작할 수 있습니다.
*   임의의 개수의 공백 뒤에는 하나의 선택적인 '+' 또는 '-' 부호가 올 수 있습니다.
*   문자열의 나머지 부분은 `int`로 변환되며, 주어진 진법(base)에서 유효한 숫자가 아닌 첫 번째 문자에서 변환을 멈춥니다. (우리의 경우 10진법만 처리하면 되므로, 유효한 숫자는 0-9입니다.)

`isspace(3)` 함수에 대해 언급했는데, 이 함수는 무엇일까요? 이 함수는 `isdigit`, `isalpha` 등과 동일하게 작동하지만, 문자가 다음 중 하나일 경우 0이 아닌 값을 반환합니다.

<details>

<summary>isspace(3)</summary>

*   \t => 탭(tabulation)
*   \n => 새 줄(new line)
*   \v => 수직 탭(vertical tabulation)
*   \f => 폼 피드(form feed)
*   \r => 캐리지 리턴(carriage return)
*   ' ' => 공백(space)

</details>

편의를 위해, 우리는 `isspace(3)` 함수를 `ft_atoi()` 함수를 위한 `static` 헬퍼(helper) 함수로 코딩할 것입니다.

### 힌트

{% code title="ft_atoi.c" overflow="wrap" lineNumbers="true" %}
```c
int    ft_atoi(const char *str)
{
    while (/* 문자가 공백인지 확인 */)
        /* 문자열에서 진행 */
    if (/* 문자가 +이고 다음 문자가 -가 아닌지 확인 */)
        /* 문자열에서 진행 */
    if (/* 문자가 -인지 확인 */)
        /* 부호를 음수로 저장 */
    while (/* 문자열에 무언가 있고 그것이 0-9 숫자인지 확인 */)
        /* 현재 숫자 값을 int 값으로 변환 */
        /* 이미 변환한 값을 덮어쓰지 않음 */
    /* int 결과에 부호를 곱함 */
    return (/* 결과 */);
}

static int    ft_isspace(int c)
{
    if (/* c가 공백 문자 중 하나인지 확인 */)
        return (/* 당신이 선택한 0이 아닌 값 */);
    return (0);
}
```
{% endcode %}

### 주석이 달린 해답

<details>

<summary>ft_atoi</summary>

{% code title="ft_atoi.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

int    ft_atoi(const char *str)
{
    int    result;
    int    sign;
    int    i;
    
    result = 0;
    sign = 1;
    i = 0;
    /* 우리의 isspace 함수 버전을 사용하여 현재 문자가 공백인지 확인합니다.
     */
    while (ft_isspace(str[i]))
        i++;
    /* 현재 문자가 '+' 문자이고 다음 문자가 '-'가 아닌지 확인합니다.
     * 다음 문자가 또 다른 '+'인지 확인할 필요는 없습니다.
     * 왜냐하면 다음 문자 또한 '+'라면, 이후에 '-' 문자를 확인하는
     * 조건문에 들어가지 않을 것이고, 이 조건문에 들어가지 않으면
     * 0-9 숫자만 확인하는 주요 while 루프에 진입하지 않을 것이기 때문입니다.
     */
    if (str[i] == '+' && str[i + 1] != '-')
        i++;
    /* 현재 문자가 '-'라면, 최종 결과에 이 부호(sign)를 곱하여
     * 음수 또는 양수를 얻을 수 있도록 sign을 -1로 설정합니다.
     */
    if (str[i] == '-')
    {
        sign = -1;
        i++;
    }
    /* 문자열의 끝에 도달하지 않았고 문자가 0에서 9 사이의 숫자인 동안 반복합니다.
     * 현재 결과를 10으로 곱하여 결과에 다음 자릿수를 추가합니다.
     * 그런 다음, 현재 문자의 10진수 값에 48을 뺀 값을 결과에 더합니다.
     * 이 '-48'은 ASCII 테이블에서 나옵니다. 문자 '0'의 10진수 값은 48입니다.
     * 우리는 int 결과에 48이 아닌 0을 더하기를 원하므로 48을 뺍니다.
     * 0에서 9 사이의 모든 숫자는 ASCII 테이블에서 순서대로 나열되어 있으므로,
     * 이 뺄셈은 모든 숫자에 대해 작동합니다.
     * 그런 다음 문자열의 다음 문자로 이동합니다.
     */
    while (str[i] && str[i] >= 48 && str[i] <= 57)
    {
        /* 이 확장 가능한 내용 아래에서 이 부분이 어떻게 작동하는지에 대한
         * 더 명확한 예시를 확인해 보십시오.
         */
        result *= 10;
	result += str[i] - 48;
	i++;
    }
    /* 모든 숫자를 int로 변환했을 때, 최종 결과에 sign 변수를 곱합니다.
     * 숫자가 음수라면, -1을 곱하게 되므로 결과의 음수 값을 얻게 됩니다.
     * 숫자가 양수라면, sign 변수가 함수 시작 시 1로 설정되었으므로,
     * 최종 결과에 1을 곱하여 결과 값이 변하지 않은 채로 유지됩니다.
     */
    result *= sign;
    return (result);
}

int    ft_isspace(int c)
{
    /* 문자가 공백 중 하나인지 확인합니다. */
    if (c == 9 || c == 10 || c == 11 || c == 12 || c == 13 || c == 32)
        /* 여기서 c를 반환할 수도 있습니다. 이 지점에 도달하면 c의 값은 0이 아닌 값이 될 것입니다.
         */
        return (1);
    return (0);
}
```
{% endcode %}

</details>

<details>

<summary>변환 예시</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
int main(void)
{
    int result;
    
    result = 0;              // result = 0
    result *= 10;            // result = 0
    result += '1' - 48;      // result = 1
    result *= 10;            // result = 10
    result += '5' - 48;      // result = 15
    result *= 10;            // result = 150
    result += '4' - 48;      // result = 154
}
```
{% endcode %}

이것이 `ft_atoi()` 함수의 `while` 루프에서 어떤 일이 발생하는지 이해하는 데 도움이 되기를 바랍니다.