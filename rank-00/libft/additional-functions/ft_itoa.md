# ft\_itoa

### 주제

{% code overflow="wrap" %}
```
FT_ITOA (simplified)

NAME (이름)
    ft_itoa -- int를 문자열로 변환합니다.
SYNOPSIS (요약)
    char *ft_itoa(int n);
DESCRIPTION (설명)
    n을 나타내는 문자열을 할당(malloc(3) 사용)하고 반환합니다.
    음수도 처리해야 합니다.
PARAMETERS (매개변수)
    n: 변환할 int
RETURN VALUES (반환 값)
    ft_itoa()는 n을 나타내는 문자열을 반환합니다. 메모리 할당이 실패하면 NULL을 반환합니다.
AUTHORIZED EXTERNAL FUNCTIONS (허용된 외부 함수)
    malloc(3)
```
{% endcode %}

### 이해하기 쉬운 설명

`ft_itoa` 함수는 `ft_atoi`와 정반대의 작업을 수행하며, 숫자를 문자열로 변환합니다.

### 힌트

전달받은 int 값으로부터 새로운 문자열을 생성하기 위해 메모리를 할당해야 하므로, 얼마만큼의 메모리를 할당해야 하는지 세어야 합니다.

우리는 문자열의 길이를 세는 함수를 가지고 있으므로, 이번에는 숫자를 나타내는 문자의 개수를 세는 함수를 만들 것입니다.

{% hint style="warning" %}
음수의 경우 `-` 부호 역시 하나의 문자이므로, 길이를 셀 때 잊지 말고 포함해야 합니다.
{% endhint %}

전달받은 값을 나타내는 문자의 개수를 알았다면, 해당 문자 개수에 NUL-terminating character(널 종료 문자)를 위한 공간을 더하여 충분한 메모리를 할당해야 합니다.

이 작업이 완료되면 숫자를 문자열로 변환하는 작업을 시작할 수 있습니다. 가장 쉬운 방법은 오른쪽에서 왼쪽으로 변환하는 것입니다. 문자열의 최종 길이를 알고 있으며, 모듈로 연산자($\%$)를 사용하여 숫자의 마지막 자릿수를 얻을 수 있기 때문입니다. 그런 다음 숫자를 10으로 나누어 마지막 자릿수를 제거할 수 있습니다.

다음 사항을 기억하십시오:

{% code lineNumbers="true" %}
```c
int a = 124;
int b = 10;
int c = a / b;
printf("%d\n", c); => 12
```
{% endcode %}

두 값 모두 정수(integer)이므로, 나눗셈은 정수 나눗셈(integer division)으로 수행됩니다. 즉, 결과에 나머지나 부동 소수점 값이 없다는 것을 의미하며, 이것이 124를 10으로 나눌 때 12를 얻는 이유입니다.

### 주석 처리된 솔루션

<details>

<summary>ft_itoa</summary>

{% code title="ft_itoa.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

static int int_len(long nbr);
static char *pre_conv(int len);

char *ft_itoa(int n)
{
    int len;
    int i;
    char *result;
    long nbr;
    
    /* 여기에서 매개변수로 받은 int n을 long 타입으로 변환합니다.
     * 이는 INT_MIN 및 INT_MAX가 문제가 되지 않도록 하기 위함이며,
     * 다른 모든 숫자와 정확히 같은 방식으로 처리할 수 있게 합니다.
     */
    nbr = n;
    /* 숫자의 길이를 가져옵니다. */
    len = int_len(nbr);
    /* 올바른 크기로 문자열을 할당하고
     * result[0] = '0'을 설정합니다.
     */
    result = pre_conv(len);
    if (!result)
        return (NULL);
    /* 숫자가 0보다 작으면, int_len 함수에서 했던 것과 동일하게
     * 숫자를 자신을 뺀 값(절댓값)과 같게 설정합니다.
     */
    if (nbr < 0)
        nbr = -nbr;
    /* 그런 다음 i = len - 1로 설정합니다. len은 NUL-terminating
     * character를 고려하며, 우리는 이를 덮어쓰지 않기를 원합니다.
     */
    i = len - 1;
     /* 이제 int에서 문자로 변환을 시작할 수 있습니다. */
    while (nbr != 0)
    {
        result[i] = ((nbr % 10) + 48);
        nbr = nbr / 10;
        i--;
    }
    /* 원래 숫자가 음수였다면, 첫 번째 문자를 '-'로 설정합니다. */
    if (n < 0)
        result[0] = '-';
    result[len] = 0;
    /* 마지막으로, 방금 생성한 문자열을 반환할 수 있습니다. */
    return (result);
}

static char *pre_conv(int len)
{
    char *tmp;
    
    /* NUL-terminating character를 건너뛰지 않도록
     * len + 1 크기만큼 메모리를 할당합니다.
     */
    tmp = malloc((len + 1) * sizeof(char));
    if (!tmp)
        return (NULL);
    /* 여기에서 새로 할당된 문자열의 인덱스 0을 문자 '0'으로 설정합니다.
     * 이 작업을 여기서 수행하는 이유는, ft_itoa 함수에서 숫자가 0일 경우
     * 어떤 조건도 작동하지 않게 되며, 첫 번째 문자를 설정하지 않으면
     * tmp[0]에는 메모리에 있던 임의의 값이 남게 됩니다.
     * 이는 문자 '0'일 수도 있지만, 대부분은 임의의 쓰레기 값일 것입니다.
     */
    tmp[0] = '0';
    return (tmp);
}

static int int_len(long nbr)
{
    int count;
    
    count = 0;
    /* 숫자가 0보다 작으면, 마지막에 추가해야 할 - 부호를
     * 고려하기 위해 count에 1을 더합니다.
     * 그리고 숫자를 자신을 뺀 값과 같게 설정하여 양수로 만듭니다.
     */
    if (nbr < 0)
    {
        count++;
        nbr = -nbr;
    }
    /* 숫자가 0과 같으면 count에 1을 더해야 합니다.
     * 이 확인 작업은 count 변수를 0 대신 1로 시작하면 생략할 수 있습니다.
     */
    if (nbr == 0)
        count++;
    /* 이 지점에 도달하면 숫자는 0과 다를 것입니다.
     * 이후 반복문을 통해 숫자를 10으로 나누고, 매번 count에 1을 더합니다.
     */
    while (nbr != 0)
    {
        nbr /= 10;
        count++;
    }
    /* 마침내 이 숫자를 문자열로 표현하는 데 필요한 문자의 개수인
     * count를 반환할 수 있습니다.
     */
    return (count);
}
```
{% endcode %}

</details>
