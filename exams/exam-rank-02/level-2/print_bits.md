# print\_bits

### 과제

{% code overflow="wrap" %}
```
Assignment name  : print_bits
Expected files   : print_bits.c
Allowed functions: write
--------------------------------------------------------------------------------

Write a function that takes a byte, and prints it in binary WITHOUT A NEWLINE
AT THE END.

Your function must be declared as follows:

void	print_bits(unsigned char octet);

Example, if you pass 2 to print_bits, it will print "00000010"
```
{% endcode %}

바이트(byte)를 입력받아 이를 이진수(binary)로 출력하는 함수를 작성해야 하며, 끝에 개행 문자(newline)를 추가해서는 안 됩니다.

함수는 다음과 같이 선언되어야 합니다:

`void print_bits(unsigned char octet);`

예시: 2를 `print_bits`에 전달하면, "00000010"을 출력합니다.

### 주석 처리된 해답

<details>

<summary>print_bits.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
#include <unistd.h>

void print_bits(unsigned char octet)
{
    int i = 8; // 카운터 변수 i를 8로 초기화합니다.
    unsigned char bit = 0; // 변수 bit를 0으로 초기화합니다.

    while (i--) // octet의 각 비트(bit)를 순회합니다.
    {
        bit = (octet >> i & 1) + 48; // octet의 비트를 오른쪽으로 i번 시프트(shift)하고, 비트 값(0 또는 1)을 얻기 위해 1과 비트 AND 연산자를 사용합니다. 이후 48을 더하여 비트 값을 ASCII 표현 ('0' 또는 '1')으로 변환합니다.
        write (1, &bit, 1); // 비트의 ASCII 표현을 표준 출력(stdout)으로 기록합니다.
    }
}


/* 코드를 테스트하고 싶다면 : */
int main()
{
	unsigned char octet = 3;
	print_bits(octet);
}

```
{% endcode %}

</details>