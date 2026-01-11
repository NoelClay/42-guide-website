# swap\_bits

### 주제

{% code overflow="wrap" %}
```
Assignment name  : swap_bits
Expected files   : swap_bits.c
Allowed functions:
--------------------------------------------------------------------------------

1 byte를 입력받아, 그 절반(예제와 같이)을 서로 교환하고 결과를 반환하는 함수를 작성하십시오.

함수는 다음과 같이 선언되어야 합니다:

unsigned char	swap_bits(unsigned char octet);

예제:

  1 byte
_____________
 0100 | 0001
     \ /
     / \
 0001 | 0100
```
{% endcode %}

### 주석 처리된 해답

<details>

<summary>swap_bits.c</summary>

{% code overflow="wrap" lineNumbers="true" %}
```c
// 8-bit octet의 최상위 4 bits (왼쪽 4 bits)를 최하위 4 bits (오른쪽 4 bits)와 교환하는 함수입니다.
unsigned char swap_bits(unsigned char octet)
{
    // 최상위 4 bits를 최하위 4 bits 위치로 오른쪽으로 이동(shift)시키고, 이를 최하위 4 bits를 최상위 4 bits 위치로 왼쪽으로 이동시킨 결과와 OR 연산합니다.
    return ((octet >> 4 | octet << 4));
}
```
{% endcode %}

</details>