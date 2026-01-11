# ft\_atoi\_base

### 과제

```
과제명            : ft_atoi_base
요구되는 파일     : ft_atoi_base.c
허용되는 함수     : None
--------------------------------------------------------------------------------

문자열 인자 str (N진법, N <= 16)을 정수(10진법)로 변환하고 그 결과를 반환하는 함수를 작성하십시오.

입력에서 인식되는 문자는 다음과 같습니다: 0123456789abcdef
이 문자들은 요청된 진법(base)에 따라 당연히 잘라내어 사용되어야 합니다. 예를 들어, 4진법은 "0123"을 인식하고 16진법은 "0123456789abcdef"를 인식합니다.

대문자 역시 인식되어야 합니다: "12fdb3"는 "12FDB3"와 동일하게 처리됩니다.

마이너스 부호 ('-')는 문자열의 첫 번째 문자인 경우에만 해석됩니다.

함수는 다음과 같이 선언되어야 합니다:

int	ft_atoi_base(const char *str, int str_base);
```

### 주석 처리된 솔루션

<details>

<summary>ft_atoi_base</summary>

{% code title="ft_atoi_base()" overflow="wrap" lineNumbers="true" %}
```c
int ft_atoi_base(const char *str, int str_base);
int isspace(int c);
int isvalid(int c, int baselen);

int ft_atoi_base(const char *str, int str_base)
{
    int res = 0, sign = 1, i = 0;
    // 공백 문자를 건너뜜
    while (isspace(str[i]))
        i++;
    // 숫자가 음수인지 확인
    if (str[i] == '+' && str[i + 1] != '-')
        i++;
    // 숫자가 음수라면, sign을 -1로 설정하고 '-'를 건너뜀
    if (str[i] == '-')
    {
        sign = -1;
        i++;
    }
    
    // 문자열을 순회하며 숫자를 10진법으로 변환
    while (str[i] && isvalid(str[i], str_base))
    {
        // 결과에 진법을 곱함
        res *= str_base;
        
        // 문자의 값을 결과에 더함
        // 문자가 숫자라면, '0'의 값을 뺍니다.
        // 즉, '5'의 ASCII 값은 53이고 '0'의 ASCII 값은 48이므로,
        // '5' - '0' = 5가 됩니다.
        if (str[i] >= '0' && str[i] <= 9)
            res += str[i] - '0';
        // 문자가 알파벳이라면, 'a' 또는 'A'의 값을 빼고 10을 더합니다.
        // 즉, 'f'의 ASCII 값은 102이고 'a'의 ASCII 값은 97이므로,
        // 'f' - 'a' + 10 = 15가 됩니다.
        else if (str[i] >= 'a' && str[i] <= 'f')
            res += str[i] - 'a' + 10;
        else if (str[i] >= 'A' && str[i] <= 'F')
            res += str[i] - 'A' + 10;
        i++;
    }
    // 결과에 부호를 곱하여 반환
    return (res * sign);
}
// 문자가 해당 진법에 유효한지 확인하는 간단한 함수
int isvalid(int ch, int baselen)
{
    // 진법에 사용되는 문자를 정의
    char *lcbase = "0123456789abcdef";
    char *ucbase = "0123456789ABCDEF";
    int i = 0;
    
    // 진법 문자열을 순회하며 문자가 유효한지 확인
    while (i < baselen)
    {
        // 문자가 진법 문자열에서 발견되면 1을 반환
        if (ch == lcbase[i] || ch == ucbase[i])
            return (1);
        i++;
    }
    return (0);
}

// 문자가 공백 문자(whitespace)인지 확인하는 간단한 함수
int isspace(int c)
{
     if (c == 9 || c == 10 || c == 11 || c == 12 || c == 13 || c == 32)
         return (1);
     return (0);
}

// 함수 테스트를 위해 main을 주석 해제합니다.
// #include <stdio.h>
// int	main(int ac, char **av)
// {
// 	(void) ac;
// 	(void) av;
// 	if (ac == 3)
// 	{
// 		printf("%d", ft_atoi_base(av[1], ft_atoi_base(av[2], 10)));
// 		return (0);
// 	}
// }
```
{% endcode %}

</details>