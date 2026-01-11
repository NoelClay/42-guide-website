# CPP05

### 주요 주제

```
반복문과 예외 (try, throw & catch)
```

이 모듈에서는 Exception을 생성하고 코드에서 사용하는 방법을 배우게 됩니다.

먼저, (배가 고프니) 음식 이야기를 해봅시다: **케이크를 굽는 상황을 상상해 보세요.**

1.  **믹싱 볼(Mixing Bowl):** 밀가루, 계란, 설탕 등의 재료를 섞는 곳입니다. 재료를 섞는 동안 작은 실수가 발생할 수 있습니다.
2.  **계란 껍데기(Eggshell):** 실수로 믹스에 계란 껍데기가 떨어졌다면, 케이크에 남아있는 것을 원하지 않으실 겁니다. 따라서 완벽한 케이크를 위해 계란 껍데기를 제거합니다. 이와 유사하게, 코드에서 예상치 못한 일이 발생하면 오류 메시지를 보내 문제를 '던져버립니다(throw away)'.
3.  **안전망(Safety Net):** 이제 믹싱 볼 아래에 안전망이 있다고 상상해 보세요. 만약 계란 껍데기가 떨어지면(오류 발생), 안전망이 그것을 잡아냅니다. 그러면 그 계란 껍데기를 버리거나 실수를 인지하는 등 어떻게 처리할지 결정할 수 있습니다. 코드에서 안전망은 'catch' 블록과 같으며, 프로그램 실행 중 발생하는 예상치 못한 문제를 처리하는 데 도움을 줍니다.

이 비유가 다소 어설플 수도 있지만, C++에서는 정확히 똑같은 일(음... 거의 비슷하게)을 할 것입니다.

이러한 새로운 개념들을 더 잘 이해하기 위해 매우 쉬운 예시를 단계별로 살펴보겠습니다 :)

다음 코드를 확인해 보세요:

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

int main() {
    int dividend, divisor, result;

    std::cout << "Enter the dividend: ";
    std::cin >> dividend;

    std::cout << "Enter the divisor: ";
    std::cin >> divisor;

    try 
    {
        if (divisor == 0) 
        {
            throw "Division by zero is not allowed!";  // Exception을 던집니다.
        }

        result = dividend / divisor;
        std::cout << "Result: " << result << std::endl;
    }
    catch (const char* errorMessage) 
    {
        std::cerr << "Error: " << errorMessage << std::endl;
    }

    return 0;
}
```
{% endcode %}

이 코드는 **세 가지 주요 부분**으로 구성되어 있습니다. 바로 "try" (믹싱 볼), "throw" (계란 껍데기), "catch" (안전망) 블록입니다:

1.  `try` 블록 (12-19행): Exception을 발생시킬 수 있는 코드를 넣는 곳입니다. 이 경우, 나누는 수(divisor)가 0인지 확인하는데, 이는 0으로 나누는 결과를 초래합니다.
2.  `throw (16행, 그리고 14-17행 전체)`: `try` 블록 내의 조건이 충족되면 (이 경우 `divisor`가 0인 경우), 프로그램은 Exception을 던집니다(throws). 이는 오류 메시지("Division by zero is not allowed!")를 발생시킨다는 의미입니다.
3.  `catch` 블록: 이 블록은 Exception이 던져지면 이를 '잡아냅니다(catches)'. 이는 오류를 처리하는 안전망과 같습니다. 이 코드에서는 `const char*` 타입의 Exception을 잡는데, 이는 문자열(오류 메시지)을 가리키는 Pointer입니다.

간단히 말해, 수학적으로 허용되지 않는 0으로 나누기를 시도하면, 코드는 오류 메시지와 함께 Exception을 던집니다. 그리고 `catch` 블록이 이 Exception을 잡아내서 오류 메시지를 표시합니다.

유효한 숫자를 입력하면 나눗셈이 수행되고 Exception은 던져지지 않습니다. 이 경우, 코드는 결과를 표시합니다.

이 메커니즘은 예상치 못한 문제가 발생했을 때 프로그램이 비정상적으로 종료되는 것을 방지하고 오류를 우아하게(gracefully) 처리하도록 돕습니다.

그리고... 이것이 전부입니다. CPP05의 모든 과제는 Exception을 다루는 것에 관한 것입니다.

여러분은 또한 자신만의 Exception, 즉 클래스 형태의 Exception을 생성해야 합니다. 이는 그리 복잡하지 않으므로, 인터넷에서 답을 검색해 보시기 바랍니다. (스포일러: 여러분이 생성하는 Exception은 ... Exception 클래스를 상속받아야 합니다 ;-) )