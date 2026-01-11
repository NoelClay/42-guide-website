# ft\_split

### Subject

{% code overflow="wrap" %}
```
FT_SPLIT (간소화)

이름
    ft_split -- 문자열을 단어 배열로 분할합니다.
개요
    char **ft_split(const char *s, char c);
설명
    구분 문자(delimiter)로 사용되는 문자 c를 기준으로 문자열 s를 분할하여 얻은 문자열들의 배열을 할당(malloc(3) 사용)하고 반환합니다.
    반환되는 배열은 NUL-terminated 되어야 합니다.
매개변수
    s: 분할할 문자열
    c: 구분 문자 (delimiter character)
반환 값
    ft_split()은 s를 분할한 결과로 생성된 문자열 배열을 반환합니다. 메모리 할당에 실패한 경우 NULL을 반환합니다.
허용되는 외부 함수
    malloc(3)
```
{% endcode %}

### Understandable explanation

`ft_split()`에 대해 들어보셨을 수도 있지만, 걱정하지 마십시오. 제가 최대한 자세히 설명해 드리겠습니다.

{% hint style="info" %}
저희가 ft\_split을 할로윈 코스튬 중 하나로 선택한 이유가 있습니다. :joy:
{% endhint %}

과제 요구 사항에 따르면 `ft_split()`은 문자열 배열(=> 문자열은 NUL 문자로 끝나는 문자 배열이므로, 배열의 배열)을 반환해야 합니다.

이를 단어 배열(array of words)로 표현할 수도 있습니다. 문자열 `s`를 받아서 그 문자열의 각 단어를 포함하는 배열로 분할하는 것입니다. 각 단어는 하나 이상의 `c` 문자로 구분되며, 이 `c`가 구분 문자(delimiter)의 역할을 합니다.

또한, 단어 배열은 NUL-terminated 되어야 한다고 명시되어 있습니다. 이는 배열에 요소를 하나 더 할당하고, 그 값을 0으로 설정해야 함을 의미합니다. 이렇게 하면 문자열과 마찬가지로 `while(words[i] != 0)`와 같이 단어 배열을 쉽게 순회할 수 있습니다.

과제 자체는 이해하기 어렵지 않지만, 이 모든 것을 수행하는 코드를 작성하는 것이 더 복잡할 수 있습니다.

### Hints

단일 함수 내에 `ft_split()`을 작성하기에는 줄 수가 충분하지 않으므로, 여러 개의 함수를 작성해야 합니다.

기능적인 `ft_split()`을 달성하기 위해 수행해야 할 모든 작업에 대해 간략하게 살펴보겠습니다 (제가 항목을 나누는 방식이 힌트가 될 수 있습니다 ;))

*   구분 문자에 따라 문자열에 있는 단어의 개수를 셉니다.
*   모든 단어를 담을 수 있을 만큼 충분히 크고, 추가로 1개(0으로 설정할 공간)를 더 담을 수 있는 배열의 배열(단어 배열)을 할당합니다.
*   단어 배열 내의 각 단어에 대한 문자열을 할당하고 단어를 복사합니다.
*   메모리 할당 오류가 발생하면 모든 것을 해제합니다 (Free).

{% code title="ft_split.c" overflow="wrap" lineNumbers="true" %}
```c
char **ft_split(const char *s, char c)
{
   /* s에 있는 모든 단어를 담을 수 있을 만큼 큰 배열을 할당합니다. */
   /* 문자열을 순회하며 단어의 시작점을 찾습니다. */
      /* 단어의 끝을 찾습니다. */
         /* 단어를 단어 배열의 첫 번째 빈 인덱스에 복사합니다. */
   /* 단어 배열을 반환합니다. */   
}

int word_count(/* 필요한 매개변수 */)
{
   /* 문자열에 있는 단어의 개수를 찾아서 반환합니다. */
}

void ft_free(/* 필요한 매개변수 */)
{
   /* 할당한 모든 것을 해제합니다. */
   /* 배열의 각 요소와 배열 자체를 해제해야 합니다. */
}

char *fill_word(/* 필요한 매개변수 */)
{
   /* 단어를 위한 충분한 공간을 할당합니다. */
   /* 위에 할당한 메모리에 단어를 복사합니다. */
   /* 할당된 단어를 반환합니다. */
}
```
{% endcode %}

### Commented solution

<details>

<summary>ft_split</summary>

{% code title="ft_split.c" overflow="wrap" lineNumbers="true" %}
```c
#include "libft.h"

static int word_count(const char *str, char c);
static char *fill_word(const char *str, int start, int end);
static void *ft_free(char **strs, int count);
/* 여러분은 제가 했던 것보다 더 나은 방법을 찾을 수 있을 것입니다.
 * 제가 구현한 것이 가장 좋은 방법은 아니지만, 작동은 합니다.
 */
static void ft_initiate_vars(size_t *i, int *j, int *s_word);

char **ft_split(const char *s, char c)
{
    char **res;
    size_t i;
    int j;
    int s_word;
    
    ft_initiate_vars(&i, &j, &s_word);
    /* 모든 단어를 담을 만큼 충분히 큰 테이블을 할당합니다. */
    res = ft_calloc((word_count(s, c) + 1), sizeof(char *));
    if (!res)
        return (NULL);
    /* 전체 문자열을 순회합니다. */
    while (i <= ft_strlen(s))
    {
        /* 이 검사는 문자열에서 단어의 첫 번째 문자 인덱스를 
         * 찾을 수 있게 해줍니다.
         * s_word는 루프를 돌 때마다 인덱스를 업데이트하지 않도록
         * 트리거 역할을 합니다.
         */
        if (s[i] != c && s_word < 0)
            s_word = i;
        /* 단어의 시작을 찾은 다음, 다른 구분자(separator)를 찾았을 때,
         * (또는 문자열의 끝일 때) 우리는 단어의 끝에 도달했음을 압니다.
         * 이는 문자열의 끝일 수도 있습니다.
         */
        else if ((s[i] == c || i == ft_strlen(s)) && s_word >= 0)
        {
            /* j는 단어 배열 내에서 단어의 인덱스입니다.
             * 여기에서 전체 문자열, 단어의 시작 인덱스, 
             * 그리고 현재의 i (단어의 끝)를 fill_word 함수에 전달합니다.
             * fill_word 함수는 할당된 단어를 반환하고 이를 res[j]에 설정합니다.
             */
            res[j] = fill_word(s, s_word, i);
            /* fill_word가 메모리 할당에 실패했다면
             * 모든 것을 해제해야 합니다. 따라서 
             * 이미 저장한 단어의 개수인 j와 단어 배열을 가지고
             * ft_free 함수를 호출합니다.
             */
            if (!(res[j]))
                return (ft_free(res, j));
            /* 단어를 저장한 후, 루프를 다시 돌 때 다음 단어를 트리거할 수 있도록
             * s_word를 -1로 재설정합니다.
             */
            s_word = -1;
            j++;
        }
        i++;
    }
    return (res);
}

/* 다른 방법을 찾으십시오. 정말입니다. */
static void ft_initiate_vars(size_t *i, int *j, int *s_word)
{
    *i = 0;
    *j = 0;
    *s_word = -1;
}

static void *ft_free(char **strs, int count)
{
    /* 이 루프에서
     * 단어 배열의 각 요소를 순회하며
     * 해제합니다.
     */
    int i;
    
    i = 0;
    while (i< count)
    {
        free(strs[i]);
        i++;
    }
    /* 모든 요소를 해제한 후
     * 단어 배열 자체를 해제합니다.
     */
    free(strs);
    return (NULL);
}

static char *fill_word(const char *str, int start, int end)
{
    char *word;
    int i;
    
    i = 0;
    /* 단어를 저장할 충분한 메모리를 할당합니다. */
    word = malloc((end - start + 1) * sizeof(char));
    if (!word)
        return (NULL);
    /* 이 루프는 str에서 word로 단어를 복사합니다.
     */
    while (start < end)
    {
        word[i] = str[start];
        i++;
        start++;
    }
    /* 마지막 문자를 0으로 설정하여 문자열을 NUL-terminate 합니다.
     * 그리고 단어를 반환합니다.
     * 이 단어는 단어 배열에 저장됩니다.
     */
    word[i] = 0;
    return (word);
}

static int word_count(const char *str, char c)
{
    int count;
    int x;
    
    count = 0;
    x = 0;
    /* x는 트리거(trigger)입니다. x가 0일 때만 단어 세기를 시작합니다.
     * 이렇게 하면 모든 구분자를 건너뛸 수 있습니다. 구분자인 동안은
     * x 변수는 계속 0을 유지합니다.
     * 더 명확히 하자면, 전체 문자열을 순회하면서 구분자가 아닌 문자를
     * 마주쳤고 트리거가 0인 경우, 단어 개수를 하나 늘리고
     * 트리거를 1로 설정하여 다음 구분자를 찾을 때까지 다른 단어를 세지 않게 합니다.
     * 구분자를 찾으면 트리거를 다시 0으로 설정하여 다른 단어가 있다면
     * 셀 수 있도록 합니다.
     * 이 트리거는 구분자로만 구성된 문자열을 처리하는 데 도움이 됩니다.
     */
    while (*str)
    {
        if (*str != c && x == 0)
        {
            x = 1;
            count++;
        }
        else if (*str == c)
            x = 0;
        str++;
    }
    return (count);
}
```
{% endcode %}

</details>