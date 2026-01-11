# CPP01

### 주요 주제

과제의 첫 페이지에 포함된 주요 주제들을 살펴보겠습니다:

```
Memory allocation, pointers to members, references, switch statement
```

### Memory allocation (메모리 할당)

C++는 기본적으로 C의 확장이라는 점을 기억해야 합니다. 따라서 C++에도 Memory allocation(메모리 할당)이 존재합니다. 다만, C에서 사용하는 방식과는 다소 차이가 있습니다.

C에서는 `malloc`과 `free`를 사용하지만, C++에서는 `new`와 `delete`를 사용합니다. 여러분이 이미 C의 메모리 관리를 잘 알고 있으므로, C와 C++를 비교하는 예시를 살펴보겠습니다.

{% code title="C Code" overflow="wrap" lineNumbers="true" %}
```c
#include <stdlib.h>
#include <stdio.h>

int main(void)
{
    int *ptr; // Pointer를 선언합니다.
    
    ptr = malloc(sizeof(int)); // 메모리를 할당합니다.
    *ptr = 45; // 값을 할당합니다.
    printf("ptr value: %d\n", *ptr); // 값을 출력합니다.
    free(ptr); // 메모리를 해제합니다.
    return (0);
}
```
{% endcode %}

{% code title="C++ Code" overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main(void)
{
    int *ptr; // Pointer를 선언합니다.
    
    ptr = new int; // 메모리를 할당합니다.
    *ptr = 45 // 값을 할당합니다.
    std::cout << "ptr value: " << *ptr << std::endl; // 값을 출력합니다.
    delete ptr; // 메모리를 해제합니다.
    return (0);
}
```
{% endcode %}

이 경우, 두 코드는 기능적으로 완전히 동일합니다. 이제 동적으로 배열을 할당하는 방법을 살펴보겠습니다.

{% code title="C Code" overflow="wrap" lineNumbers="true" %}
```c
#include <stdlib.h>

int main(void)
{
    int *ptr;
    
    ptr = malloc (10 * sizeof(int));
    free(ptr);
    return (0);
}
```
{% endcode %}

{% code title="C++ Code" overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main(void)
{
    int *ptr;
    
    ptr = new int[10];
    delete [] ptr;
    return (0);
}
```
{% endcode %}

### Pointers to members (멤버 Pointer)

우선, Pointers to members란 무엇일까요?

C에서는 변수를 가리키는 Pointer와 함수를 가리키는 Pointer가 있다는 것을 기억하실 겁니다. 이들은 C++에도 존재하지만, C++에서는 Class 개념을 Pointer에 도입하기 위해 두 가지 새로운 Pointer 타입이 추가되었습니다.

여기에 Pointers to members에 대한 인트라 영상의 코드 예시를 첨부합니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>
#include <Sample.hpp>

int main()
{
    Sample instance; // Sample Class의 instance를 Stack에 생성합니다.
    Sample *instancep = &instance; // Sample의 instance를 가리키는 Pointer입니다.

    int Sample::*p = NULL; // Sample Class의 멤버 Attribute(속성)를 가리키는 Pointer입니다.
    // Sample::을 추가하면 Sample Class의 멤버 Attribute를 가리킬 것임을 명시합니다.
    void (Sample::*f)(void) const; // Sample Class의 멤버 함수를 가리키는 Pointer입니다.
    
    p = &Sample::foo; // Sample::*p를 foo 멤버 Attribute의 주소로 설정합니다.
    
    std::cout << "Value of member foo: " << instance.foo << std::endl;
    instance.*p = 21; // 이 줄은 `instance` Instance에서 *p가 가리키는 멤버 Attribute의 값을 21로 설정합니다.

    std::cout << "Value of member foo: " << instance.foo << std::endl;
    instancep->*p = 42; // 이 줄은 `instancep` Pointer가 가리키는 Instance에서 *p가 가리키는 멤버 Attribute의 값을 설정합니다.
    std::cout << "Value of member foo: " << instance.foo << std::endl;
    
    f = &Sample::bar;
    
    (instance.*f)(); // Sample Class의 `instance` Instance에서 멤버 함수 Bar를 호출합니다.
    (instancep->*f)(); // 위와 동일하지만, 이번에는 Instance에 대한 Pointer를 사용합니다.
}
```
{% endcode %}

#### .\* operator (연산자)

> 위의 예시에서 16행을 참조합니다.

우리는 `Sample::*p`가 `Sample` Class 내의 멤버 `foo`를 가리키도록 설정했습니다. 문제는 `Sample` Class의 Instance가 여러 개 있을 수 있으므로, 어떤 Instance에서 이 Pointer를 사용할지 다음 구문을 사용하여 명시해야 한다는 점입니다: `instance.*pointerToMemberAttribute`.

이는 `foo` 멤버 Attribute가 여러 Class Instance에 존재할 수 있기 때문입니다.

#### ->\* operator (연산자)

> 위의 예시에서 19행을 참조합니다.

여기서는 `Sample` Instance를 직접 사용하지 않고, `.*` operator 대신 `->*` operator를 사용해야 합니다. 작동 방식은 정확히 같지만, 그 사이에 Dereference(역참조) 단계가 하나 추가됩니다.

`instancePtr`이 `Sample` Class의 Instance를 가리킬 때, `instancePtr->*pointerToMemberAttribute` 구문은 Instance 변수에 직접 사용되는 `.*` operator와 동일하게 동작합니다.

#### 멤버 함수 Pointer를 통한 멤버 함수 호출

> 위의 예시에서 24행과 25행을 참조합니다.

멤버 함수를 가리키는 Pointer도 멤버 Attribute를 가리키는 Pointer와 동일한 방식으로 작동합니다.

먼저 Pointer가 어떤 멤버 함수를 가리킬지 설정해야 하며, 함수를 호출할 때에는 해당 함수를 Class의 어떤 Instance에서 호출할 것인지 명시해야 합니다.

멤버 Attribute를 가리키는 Pointer와 마찬가지로, `.*`와 `->*` 두 operator를 사용합니다.

`.*` operator는 Instance 변수를 직접 사용할 때 사용되며, `->*` operator는 Instance 변수를 가리키는 Pointer가 있을 때 사용됩니다.

### References (참조)

Reference(참조)는 C에는 존재하지 않는 새로운 개념이며, C의 Pointer가 하는 역할과 유사합니다.

Reference에 대한 더 정확한 정의는 다음과 같습니다:

> A reference is a pointer that is constant and is always dereferenced, it can never be `void`\

인트라 비디오에 나오는 첫 번째 예시를 살펴보겠습니다.

{% code title="c++ Refs UnCommented" overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main(void)
{
    int numberOfBalls = 42;
    
    int *ballsPtr = &numberOfBalls;
    int &ballsRef = numberOfBalls;
    
    std::cout << numberOfBalls << " " << *ballsPtr << " " << ballsRef << std::endl;
    
    *ballsPtr = 21;
    std::cout << numberOfBalls << std::endl;
    ballsRef = 84;
    std::cout << numberOfBalls << std::endl;
    
    return (0);
}
```
{% endcode %}

위에서 주어진 정의만으로 이 코드의 출력을 예상할 수 있으신가요?

<details>

<summary>코드 결과</summary>

```sh
$> 42 42 42
$> 21
$> 84
```

</details>

이제 동일한 예시를 가져와서 코드가 어떤 작업을 수행하는지 이해할 수 있도록 주석을 추가해 보겠습니다.

{% code title="c++ Refs commented" overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main(void)
{
    int numberOfBalls = 42; // numberOfBalls 변수에 42를 할당합니다.
    
    int *ballsPtr = &numberOfBalls; // numberOfBalls 변수의 주소를 ballsPtr Pointer에 할당합니다.
    int &ballsRef = numberOfBalls; // ballsRef를 numberOfBalls 변수에 대한 Reference로 할당합니다.
    // Reference는 다른 변수의 주소가 아닌, 다른 변수를 직접 할당한다는 점에 주목하십시오.
    // 일단 Reference가 설정되면, 그것이 참조하는 대상을 변경할 수 없습니다.
    // 이는 나중에 다음과 같은 작업을 할 수 없음을 의미합니다: ballsRef = otherIntValue;
    
    std::cout << numberOfBalls << " " << *ballsPtr << " " << ballsRef << std::endl;
    // 위의 줄은 먼저 numberOfBalls 변수의 값을 출력합니다.
    // 그 다음 * operator를 사용하여 Pointer를 Dereference하여 ballsPtr이 가리키는 값을 출력합니다.
    // 그 다음 numberOfBalls 변수에 대한 Reference인 ballsRef의 값을 출력합니다.
    
    *ballsPtr = 21;
    // ballsPtr이 가리키는 값을 수정하기 위해 Pointer를 Dereference합니다.
    std::cout << numberOfBalls << std::endl;
    // 21이 출력되어야 합니다.
    ballsRef = 84;
    // ballsRef가 참조하는 값을 수정합니다.
    std::cout << numberOfBalls << std::endl;
    // 84가 출력되어야 합니다.

    return (0);
}
```
{% endcode %}

#### C++ Reference에 대해 주목할 사항

* Reference는 어느 정도 Dereference된 Pointer와 같습니다.
* 일단 정의되면 항상 동일한 값을 참조합니다.
* Reference를 직접 값을 할당하지 않고 선언할 수 없습니다.
* Reference는 Constant(상수)이므로, 선언 후에는 참조하는 대상을 변경할 수 없습니다.
* Pointer는 `void`일 수 있지만, Reference는 `void`일 수 없습니다.

위 목록 중 일부 내용을 코드로 표현하여 시각적으로 이해를 돕겠습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main(void)
{
    int intValue = 0;
    int secondValue = 10;
    
    int &valRef = intValue; // 항상 intValue를 참조할 것입니다.
    int &valRef2; // Reference는 참조할 변수를 직접 할당해야 하므로 이 코드는 작동하지 않습니다.
    valRef = secondValue; // valRef는 이미 intValue를 참조하도록 설정되었으므로 이 코드는 작동하지 않습니다.
    int &valRef3 = NULL; // Reference는 void일 수 없으며, Constant이므로 나중에 다른 것을 참조하도록 설정할 수 없기 때문에 이 코드는 작동하지 않습니다.
}
```
{% endcode %}

### Switch statement (Switch 문)

`switch` statement에 대한 내용은 이미 [여기](../../useful-tools/switch-statement.md)에서 읽어보실 수 있습니다.