---
description: '이 프로젝트의 목표는 간단합니다: printf 함수를 복제하는 것입니다.'
---

# ft\_printf

요약하자면, printf 함수는 화면에 <mark style="color:red;">형식이 지정된(formatted)</mark> 문자열을 표시하는 명령어입니다.

{% hint style="info" %}
"형식(format)"이라는 단어는 % 문자로 시작하는 형식 지정자(format specifiers)가 숫자와 같은 데이터 요소를 문자로 변환하는 위치와 방법을 나타낸다는 의미입니다.
{% endhint %}

예를 들어 보겠습니다.

먼저, C에서 printf 함수를 사용하려면 <mark style="color:purple;">"stdio" 라이브러리</mark>(standard input/output, 표준 입/출력)를 포함해야 합니다.

{% code lineNumbers="true" %}
```c
#include <stdio.h>

int main()
{
	printf("hello my name is Laura and i'm 23 years old\n");
	printf("hello my name is %s and i'm %d years old\n", "Laura", 22);
}
```
{% endcode %}

5행에서는 단순한 string을 printf 했습니다. 다음 행인 6행에서는 여러분의 이해를 돕기 위해 형식 지정자를 사용했습니다.

**두 함수는 동일한 결과를 출력하지만, 구성 방식에서 차이가 있습니다.**

<figure><img src="../../.gitbook/assets/Capture.PNG" alt=""><figcaption></figcaption></figure>

문장의 시작 부분에서 각 문자는 함수의 출력으로 문자 그대로 복사/작성됩니다. 그러다가 함수가 **형식 지정자**(% 문자로 시작)를 **발견하면**, 동일한 위치에 있는 인자(argument)를 가져와서 매우 특정한 방식으로 작성합니다.

예를 들어, 첫 번째 %를 발견하면 어떤 타입인지 확인합니다. 우리의 예시에서 첫 번째는 "s"이므로, 첫 번째 인자를 string으로 처리하고, 이 요소를 함수 출력으로 복사하기 위해 특정 메소드를 사용합니다. 다음 %를 발견했을 때도 같은 작업을 수행합니다. 기타 등등.

### 형식 지정자 (Format specifiers)

'%' 문자 뒤의 문자는 각기 다른 의미를 가집니다. 42 학교에서 이 프로젝트를 완료하는 데 필요한 것들만 살펴보겠습니다.

<table><thead><tr><th width="128">문자</th><th>설명</th></tr></thead><tbody><tr><td>%</td><td>% 문자를 출력합니다.</td></tr><tr><td>d, i</td><td>부호 있는 <a href="https://en.wikipedia.org/wiki/Integer">integer</a>로 <em>int를 출력</em>합니다. %d와 %i는 출력에서는 동일하지만, 입력 시 <a href="https://en.wikipedia.org/wiki/Scanf()"><code>scanf</code></a>와 함께 사용할 때는 다릅니다. (%i를 사용하면 숫자 앞에 0x가 붙으면 16진수, 0이 붙으면 8진수로 해석합니다.)</td></tr><tr><td>u</td><td>10진수 unsigned int를 출력합니다.</td></tr><tr><td>x, X</td><td>unsigned int를 <a href="https://en.wikipedia.org/wiki/Hexadecimal">16진수(hexadecimal)</a>로 출력합니다. x는 소문자를 사용하고 X는 대문자를 사용합니다.</td></tr><tr><td>s</td><td>널 종료(\0) string을 출력합니다.</td></tr><tr><td>c</td><td>단일 문자 (char)를 출력합니다.</td></tr><tr><td>p</td><td>Pointer 또는 다른 변수의 주소를 출력합니다. 출력은 16진수 값으로 표시됩니다. 이는 (void *) 타입의 데이터를 출력하려는 경우에 사용되는 형식 지정자입니다.</td></tr></tbody></table>

### 반환 값 (int)

printf 함수의 프로토타입은 다음과 같이 구성됩니다.

```c
int	ft_printf(const char *format, ...);
```

보시다시피, printf 함수는 <mark style="color:red;">int 타입의 값을 반환합니다</mark>. 하지만 왜 그럴까요?

간단히 말해, 이는 주로 **오류를 확인**하기 위한 것입니다. 반환 값을 사용하여 작업이 성공했는지 여부를 확인할 수 있습니다. printf의 반환 값을 확인하면, 첫 번째 행에서 이미 실패했을 때 수백만 줄을 작성하려고 시도하며 시간을 낭비하지 않도록 실패를 *조기에* 감지할 수 있습니다.

*   양수 값(작성된 문자 수를 나타냄)이 반환되면 작업이 성공했음을 의미합니다.
*   음수가 반환되면 오류가 발생했음을 의미합니다.

함수가 어떤 값을 반환하는지 확인하고 싶다면, printf를 printf로 출력해 볼 수 있습니다.

<pre class="language-c"><code class="lang-c"><strong>int main()
</strong><strong>{
</strong><strong>    int result = printf("Sentence to know how many %s\n", "characters were written");
</strong><strong>    
</strong>    printf("%d characters were written", result);
}
</code></pre>