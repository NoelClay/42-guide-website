---
description: "이것은 이 프로젝트를 수행하면서 배우게 될 새로운 개념입니다. 다음 단계로 넘어가기 전에 이 개념을 잘 이해하는 것이 필수적입니다."
---

# ▪️ Variadic functions (가변 인자 함수)

여러분께서 지금까지 과정에서 사용하고 만들어 왔던 함수들은 고정된 인자를 가졌습니다. 인자의 개수가 여러 개일 수는 있었지만, 어떤 인자가 필요할지 항상 사전에 알고 있었습니다.

예를 들어, `strlen`은 입력으로 문자열 하나만을 받습니다 `(int ft_strlen(char *str))`. 그리고 `split`은 두 개의 요소를 입력으로 받습니다 `(**ft_split(char const *s, char c))`. 어떤 개념인지 이해하실 수 있을 겁니다.

**Variadic function**은 <mark style="color:red;">**가변 개수의 인자(variable number of arguments)**</mark>를 허용하는 함수입니다. 이 함수는 함수의 정의에서 **"..."**으로 특징지어집니다.

```c
int	ft_printf(const char *format, ...);

// const char *format은 printf의 필수 인자입니다.
```

{% hint style="warning" %}
Variadic function은 최소한 하나의 **필수 인자(mandatory argument)**를 가지고 있어야 합니다. 가변 인자의 개수에 대한 최소 제한은 없습니다.
{% endhint %}

누군가가 printf 함수를 호출할 때, 우리는 사용자가 몇 개의 요소를 출력하기를 원하는지 사전에 알 수 없습니다. 즉, format specifier(형식 지정자)가 몇 번 사용될지 알 수 없습니다.

```c
printf("hello my name is %s and i am %d old\n", "laura", 23);
```

위의 경우, 함수는 초기 문자열 외에 두 개의 다른 인자를 취합니다. 만약 여러분이 어떤 타입의 인자를 몇 개 사용할지 사전에 알고 있다면, 이 함수는 다음과 같이 재작성될 수 있습니다.

```c
int	ft_printf(const char *format, char *string, int age);
```

다행히 C 언어는 잘 설계되어 있습니다. 입력되는 인자의 개수를 변경할 때마다 printf 함수를 매번 다시 작성할 필요가 없습니다. 여러분은 단순히 "stdarg" 라이브러리를 포함하기만 하면 되며, 이 라이브러리는 새로운 변수 타입인 **`va_list`**와 세 가지의 매우 유용한 _매크로(macros)_인 **`va_start`, `va_arg`** 및 **`va_end`**를 사용할 수 있게 해줍니다.

```c
#include <stdarg.h>
```

이들을 어떻게 사용하는지 자세히 살펴보겠습니다.

## va\_list, va\_start, va\_end & va\_arg

### `va_list` - 새로운 객체 타입

`va_list`는 `va_start`, `va_copy,` `va_arg`, 그리고 `va_end` 매크로가 필요로 하는 정보를 담기에 적합한 <mark style="color:red;">**객체 타입(object type)**</mark>입니다 (이 내용은 몇 분 안에 이해하게 될 것입니다). 다시 말해, 이는 모든 동적 인자(dynamic arguments)를 포함하게 될 목록입니다.

이 타입의 변수를 생성하려면 다른 변수와 동일한 방식으로 생성해야 합니다.

```c
va_list    any_name_you_want;

// 다음 예시를 위해 이를 args라고 부르겠습니다:
va_list    args;
```

이렇게 하면 다음과 같이 도식화할 수 있는 동적 인자 목록이 생성됩니다.

<figure><img src="../../.gitbook/assets/va_list.PNG" alt=""><figcaption></figcaption></figure>

이 예시에서, printf 함수는 필수 인자 외에 2개의 추가 인자를 취합니다. 이 두 인자는 가변 인자이며, 이전에 생성된 va\_list 타입의 변수에 `va_start` 덕분에 저장될 것입니다.

### **`va_start` - 함수 매크로**

`va_start` 매크로 함수는 우리가 va\_list(가변 인자 목록)를 통해 이동하기 시작하기 전에 모든 것을 일종의 방식으로 초기화합니다. 다음과 같이 작성해야 합니다.

```c
va_start( va_list var, parameterN );

// 우리의 예시에서는 다음과 같습니다:
va_start( args, format);
```

* `var`는 arg\_list 타입의 변수입니다 (우리에게는 args).
* `parameterN`은 첫 번째 동적 인자 바로 앞에 오는 이름이 지정된 매개변수입니다 (우리 예시에서 printf의 경우, 초기 문자열이 될 것입니다). 즉, **이는 필수 인자(mandatory argument)입니다.**

이 매크로의 목적은 환경을 설정하고 어떤 요소가 고정되고(stable) 어떤 요소가 가변적일지 정의하는 것입니다. 이때 여러분의 va\_list 변수가 테이블의 모든 요소를 가지게 됩니다.

{% hint style="warning" %}
`va_arg`를 사용하기 전에 반드시 `va_start`가 호출되어야 합니다. 그렇지 않으면 va\_list 변수/테이블이 비어 있게 됩니다.
{% endhint %}

### **`va_arg` - 함수 매크로**

이제 모든 것이 준비되었으므로, 가변 인자를 사용하고 다룰 수 있습니다. 이는 va\_arg를 통해 수행할 수 있습니다.

이 매크로는 Variadic function의 인자에 접근할 수 있도록 해줍니다. `va_arg`가 호출될 때마다 다음 인자로 이동합니다.

`va_arg`는 인자로 우리가 처음에 정의했던 동적 인자 목록 (va\_list 객체)과 다음 인자의 **타입**을 받습니다.

```c
va_arg( va_list var, type_of_the_variable )
```

우리의 예시로 돌아가 보겠습니다.

<figure><img src="../../.gitbook/assets/va_list (1).PNG" alt=""><figcaption></figcaption></figure>

가변 인자 목록(args)의 길이는 2입니다. 첫 번째 위치에 문자열 인자가 있고, 두 번째 위치에 int 인자가 있습니다.

첫 번째 인자에 접근하려면 `va_arg`를 한 번 호출하고 해당 인자의 타입을 명시해야 합니다. 이 경우, 첫 번째 인자는 문자열이며, Pointer로 정의됩니다.

두 번째 인자에 접근하려면 동일하게 수행합니다. 하지만 이번에는 인자의 타입이 "int"입니다.

{% code lineNumbers="true" %}
```c
// 첫 번째 인자 ("string" 타입)에 접근합니다.
va_arg( args,  char * ) // -> "Laura"

// 두 번째 인자 ("int" 타입)에 접근합니다.
va_arg( args, int ) // -> 23
```
{% endcode %}

분명히, 이것은 여러분이 va\_arg가 어떻게 작동하는지 이해할 수 있도록 돕기 위한 이론적인 예시일 뿐입니다. 실제로는 함수가 몇 개의 인자를 취할지 사전에 알 수 없으므로, 지정된 각 포맷(format)에 대해 조건을 만들어야 한다는 것을 짐작할 수 있습니다.

### **`va_end` - 함수 매크로**

프로그램을 완료한 후에는, va\_start를 호출하여 초기화했던 객체를 정리하는 것을 잊지 마십시오.

`va_end`는 이전 예시에서 "args"라고 불렀던 객체를 수정하여 더 이상 사용할 수 없도록 만듭니다.

```c
va_end( va_list var );

// 그리고 우리의 예시에서는 다음과 같습니다:
va_end (args);
```

`va_end()`는 할당된 메모리를 해제합니다.

이제 기본 사항을 파악하고 가장 복잡한 부분(일명 Variadic function, 무섭지 않습니까?)을 이해했으므로, 여러분의 자체 printf인 전설적인 ft\_printf를 만들어 볼 수 있습니다. 단계별로 함께 진행해 보겠습니다.