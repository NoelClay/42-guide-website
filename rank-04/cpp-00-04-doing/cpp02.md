# CPP02

### 주요 주제

```
Ad-hoc polymorphism, operator overloading 및 Orthodox Canonical 클래스 형태
```

Ad-hoc polymorphism과 operator overloading에 대해 자세히 알아보기 전에, 이 모듈의 과제에서 주어진 새로운 규칙들을 살펴보겠습니다.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-25 at 20.40.15.png" alt=""><figcaption><p>CPP02 - 새로운 규칙</p></figcaption></figure>

Orthodox Canonical Form, 와, 정말 복잡하게 들리지만... 사실 그렇게 어렵지 않습니다.

이 규칙은 단순히 클래스에 필수적인 멤버 함수들을 추가하도록 요구하며, 잠시 후에 자세히 살펴보겠습니다.

두 번째 새로운 규칙은, 이미 알고 계실 수도 있습니다. 만약 그렇지 않다면 아래 예시들을 보시면 아이디어를 아주 빠르게 파악하실 수 있을 것입니다.

좋습니다. 이제 Orthodox Canonical Form에 대해 알아보겠습니다.

### Orthodox Canonical Form

과제에서 명시하듯이, OCF (Orthodox Canonical Form)를 준수하려면 4개의 필수 멤버 함수가 필요하며, 그 예시를 살펴보겠습니다.

{% code title="example.hpp" overflow="wrap" lineNumbers="true" %}
```cpp
#ifndef EXAMPLE_HPP
# define EXAMPLE_HPP

class Example
{
    public:
        Example(void);
        Example(const Example& ex);
        Example &operator=(const Example& e);
        ~Example(void);
}

#endif
```
{% endcode %}

클래스 선언 자체는 어렵지 않지만, 아마 이 라인을 제외하고 말입니다:

`Example &operator=(const Example& e);`

제가 처음 이것을 봤을 때 _이게 대체 뭐야_ 라고 생각했지만, 금방 이해가 되기 시작했습니다. 이것은 다음 코드를 작성할 때 프로그램이 어떻게 동작해야 하는지를 알려주는 "단지" 하나의 방법입니다.

{% code title="main.cpp" overflow="wrap" lineNumbers="true" %}
```cpp
#include "Example.hpp"

int main(void)
{
    Example a;
    Example b;
    
    a.num = 10;
    a.name = "Simon";
    
    b = a; 
    // 어떤 값들이 서로 같아야 할까요?
    // 이것이 operator overload를 통해 정의할 수 있는 내용입니다.
}
```
{% endcode %}

4개의 필수 멤버 함수 중에서, 기본 생성자(default constructor)와 소멸자(destructor) 최소 2개는 이미 알고 계실 것입니다. 나머지 두 가지가 무엇을 하는지 간단히 살펴보겠습니다.

첫째, copy constructor입니다. copy constructor는 객체에 대한 참조를 받아들이고, 참조된 객체의 값을 기반으로 새로운 객체를 생성합니다.

{% code title="Example.cpp" overflow="wrap" lineNumbers="true" %}
```cpp
#include "Example.hpp"

Example::Example(const Example& ex) : _val1(ex._val1) /* ... */
{
    return ;
}
```
{% endcode %}

_copy constructor_라는 이름이 매우 명확하므로, 그 핵심 내용을 이해하셨을 것이라 생각합니다.

다음으로, assignment operator overload를 살펴보겠습니다.

> 이것은 또한 _Operator overload_ 주요 주제와 연결됩니다.

{% code title="Example.cpp" overflow="wrap" lineNumbers="true" %}
```cpp
#include "Example.hpp"

Example &Example::operator=(const Example& ex)
{
    // 이 경우, 생성자 함수가 아니므로 초기화 목록(initialization list)을 사용할 수 없습니다.
    this->_val1 = ex._val1;
    return (*this);
}
```
{% endcode %}

### Ad-hoc polymorphism

욕하는 것이 아닙니다. 약속합니다. 설명해 드리겠습니다.

_도대체 ad-hoc polymorphism이란 무엇일까요?_

C와 마찬가지로 C++은 타입이 지정된(typed) 언어라는 점을 기억하십시오. 따라서 `int`를 인수로 받는 함수를 작성했다면, `unsigned int`를 전달할 수 없습니다. 하지만 C++에서는 C에서 할 수 없는 `function overloading`이라는 것을 할 수 있으며, 이것이 ad-hoc polymorphism이 구축되는 방식입니다.

먼저, 다음 C 코드를 컴파일해 보십시오.

{% code title="main.c" overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>

void print(int i);
void print(unsigned int j);

int main(void)
{
    int i = 10;
    unsigned int j = 100;
    
    print(i);
    print(j);
    
    return (0);
}

void print(int i)
{
    printf("Value is: %d\n", i);
}

void print(unsigned int j)
{
    printf("Value is: %u\n", j);
}
```
{% endcode %}

모든 것이 정상이라면, 다음과 같은 오류가 발생해야 합니다.

`main.c:4:6: error: conflicting types for 'print'`

이는 완전히 정상입니다. 이름은 같지만 다른 인자 타입을 받는 두 함수가 있기 때문인데, C에서는 이것이 불가능합니다.

이제 다음 C++ 코드를 시도해 보겠습니다.

{% code title="main.cpp" overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

void print(int i);
void print(unsigned int j);

int main(void)
{
    int i = 10;
    unsigned int j = 100;
    
    print(i);
    print(j);
    print(i, j);
    
    return (0);
}

void print(int i)
{
    std::cout << "Value is: " << i << std::endl;
}

void print(unsigned int j)
{
    std::cout << "Value is: " << j << std::endl;
}

void print(int i, unsigned int j)
{
    std::cout << "Values are: " << i << " and " << j << std::endl;
}
```
{% endcode %}

그리고 이제... 놀랍게도 C++에서는 컴파일 시점에 오류가 없습니다. 왜 그럴까요?

이는 C++에서 function overloading이 허용되기 때문입니다. function overloading을 사용하면 동일한 이름의 함수를 같은 스코프(scope) 내에서 두 개 이상 정의할 수 있습니다.

Ad-hoc polymorphism은 function overloading과 함께 작동하며, 이는 코드 중복(code duplication)을 초래합니다. 함수가 작동하길 원하는 타입마다, 해당 함수를 다른 타입을 사용하여 완전히 처음부터 다시 작성해야 합니다.

{% hint style="info" %}
`Parametric polymorphism`이라는 것도 있지만, 이는 나중에 다룰 또 다른 주제입니다 (-> [CPP07](CPP07))
{% endhint %}

### Operator overloading

이제 이 모듈의 마지막 주제인 operator overloading에 대해 알아보겠습니다.

이전 섹션에서 보셨듯이, C++에서는 동일한 이름의 함수를 두 개 이상 정의하여 함수를 오버로드할 수 있습니다. 이제 `=`, `>=`, `<=`, `==` 등 많은 operator들을 어떻게 오버로드할 수 있는지 보여드리겠습니다.

저는 몇 가지 operator overload 예시만 보여드릴 것입니다. 다른 타입들도 선언 방식에 약간의 변형이 있지만 대부분 비슷하게 작동합니다. 이 모듈의 exercise 02에 도달했을 때 필요한 특정 변형 사항은 직접 찾아보시기를 권장합니다.

{% code title="Example.cpp" overflow="wrap" lineNumbers="true" %}
```cpp
#include "Example.hpp"

// 대입 연산자 (Assignment operator)
Example &Example::operator=(const Example& ex) {...} // 이미 보셨던 내용입니다.

// 비교 연산자 (Comparison operator) (사용 가능한 여러 연산자 중 하나)
bool Example::operator>(const Example& ex)
{
    return (this->_val > ex._val);
}

// 산술 연산자 (Arithmetic operator)
Example Example::operator+(const Exammple& ex) const
{
    return (Example(this->_val + ex._val));
}

// 출력 스트림 연산자 (Output stream operator)
std::ostream &operator<<(std::ostream &o, const Example &ex)
{
    o << "Value is: " << ex._val << std::endl;
    return (o);
}
```
{% endcode %}

앞에서 말씀드렸듯이, 사용 가능한 모든 operator overload를 보여드리지는 않겠지만(아래 참조), 주요한 것들만 보여드렸습니다. 이를 통해 대부분의 선언 방식에 대한 아이디어를 얻으실 수 있습니다. 이제 올바른 검색 용어를 알게 되었으므로 간단한 Google 검색만으로도 원하는 정보를 쉽게 찾을 수 있을 것입니다.

<figure><img src="../../.gitbook/assets/Screenshot 2023-05-26 at 23.26.01.png" alt=""><figcaption><p>오버로드할 수 있는 모든 사용 가능한 연산자... - <a href="https://en.cppreference.com/w/cpp/language/operators">cppreference.com</a></p></figcaption></figure>

#### 도우미 스크립트

만약 이 모든 내용을 읽고 이 부분까지 오셨다면, 일종의 "선물"을 받을 자격이 있습니다. [여기](https://github.com/Laendrun/ocf_script)를 클릭하시면 Orthodox Canonical Form에 필요한 파일과 함수 템플릿을 자동으로 생성해 주는 bash 스크립트를 찾을 수 있습니다.
