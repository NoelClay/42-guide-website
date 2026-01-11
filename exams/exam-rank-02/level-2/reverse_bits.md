# reverse\_bits

### 주제

```
과제 이름      : reverse_bits
제출 예상 파일 : reverse_bits.c
허용 함수      :
--------------------------------------------------------------------------------

1 byte를 입력받아, 예시처럼 비트 단위로 뒤집어서(reverse) 그 결과를 반환하는 함수를 작성하십시오.

함수는 다음과 같이 선언되어야 합니다:

unsigned char	reverse_bits(unsigned char octet);

예시:

  1 byte (1 바이트)
_____________
 0010  0110
     ||
     \/
 0110  0100
```

### 주석이 달린 솔루션

<details>

<summary>reverse_bits.c</summary>

{% code title="reverse_bits.c" overflow="wrap" lineNumbers="true" %}
```c
unsigned char	reverse_bits(unsigned char octet)
{
	unsigned int	i;
	unsigned char	res = 0;

	i = 8;
	while (i--)
	{
		// res의 모든 비트를 왼쪽으로 1만큼 이동시킵니다.
		// 예: res: 0000 0101 => 0000 1010
		// octet의 LSB와 1에 대해 비트wise AND 연산을 수행합니다.
		// LSB(Least Significant Bit)는 가장 오른쪽에 있는 비트입니다.
		// 예: octet: 0000 0101 => 1
		// 그런 다음, 두 LSB 사이에 비트wise OR 연산을 수행하고
		// 그 결과를 res에 저장합니다.
		// 0000 1010 | 0000 0001 => res: 0000 1011
		res = (res << 1) | (octet & 1);
		// 원래 octet을 오른쪽으로 1만큼 시프트합니다.
		// 이를 통해 다음 반복에서 다음 비트를 얻을 수 있습니다.
		octet = octet >> 1;
	}
	return (res);
}

// 코드를 테스트하려면:
int	main(void)
{
	unsigned char bit = 0;
	// 다음 줄의 숫자를 변경하면 reverse_bits 함수로 전달되는
	// 비트가 변경됩니다.
	// 5 => 0000 0101 이며, 결과는 1010 0000이 되어야 합니다.
	unsigned char res = reverse_bits((unsigned char)5);

	int i = 8;
	while (i--)
	{
		bit = (res >> i & 1) + 48;
		printf("%c", bit);
	}
}
```
{% endcode %}

</details>