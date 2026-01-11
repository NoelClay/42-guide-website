# last\_word

### 주제

```
과제 이름  : last_word
요구되는 파일   : last_word.c
허용되는 함수: write
--------------------------------------------------------------------------------

문자열을 인수로 받아 그 문자열의 마지막 단어를 표시하고 그 뒤에 \n을 붙여 출력하는 프로그램을 작성하십시오.

단어는 공백/탭 또는 문자열의 시작/끝에 의해 구분되는 문자열의 일부입니다.

매개변수의 개수가 1이 아니거나 단어가 없는 경우, 개행 문자(\n)를 표시합니다.

예시:

$> ./last_word "FOR PONY" | cat -e
PONY$
$> ./last_word "this        ...       is sparta, then again, maybe    not" | cat -e
not$
$> ./last_word "   " | cat -e
$
$> ./last_word "a" "b" | cat -e
$
$> ./last_word "  lorem,ipsum  " | cat -e
lorem,ipsum$
$>
```

### 주석 처리된 솔루션

<details>

<summary>last_word.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

int main (int ac, char **a)
{
	if (ac == 2)
	{
		int i = 0;

		// 문자열의 끝으로 이동합니다.
		while (a[1][i])
			i++;
		i--;
		// 전체 문자열을 뒤에서부터 반복합니다.
		// (단어가 끝나는) 공백을 찾을 때까지
		while (a[1][i] > 32)
			i--;
		i++;
		// 단어의 시작 부분(i)부터 끝까지 마지막 단어를 화면에 write 합니다.
		while (a[1][i])
		{
			write(1, &a[1][i], 1);
			i++;
		}
	}
	write (1, "\n", 1);
}
```
{% endcode %}

</details>