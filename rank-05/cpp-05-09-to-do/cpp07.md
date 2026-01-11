# CPP07

### 주요 주제

```
C++ templates
```

이 모듈은 이해하기 가장 간단했던 CPP 모듈이라고 생각합니다. Template은 미래 프로젝트에서 많은 시간을 절약해 줄 수 있는 정말 직관적인 개념입니다! 바로 이 주제로 들어가 보겠습니다. :smile:

### Templates

C++의 Templates는 코드를 중복시키지 않고도 다양한 데이터 타입에서 작동할 수 있는 일반적인(Generic) 코드를 작성할 수 있도록 허용합니다. 아주 간단한 예시를 사용해 보겠습니다.

**두 값을 바꾸는(swap) 함수를 만들고 싶다고 상상해 보세요.**

Templates 없이 구현한다면, 바꾸고 싶은 데이터 타입별로 별도의 함수를 만들어야 할 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
void swapIntegers(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

void swapDoubles(double& a, double& b) {
    double temp = a;
    a = b;
    b = temp;
}
```
{% endcode %}

Templates를 사용하면 다양한 데이터 타입에서 작동하는 단일 함수를 만들 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
template <typename T>
void swapValues(T& a, T& b) {
    T temp = a;
    a = b;
    b = temp;
}
```
{% endcode %}

*   `template <typename T>`: 이 줄은 Placeholder 타입 `T`를 사용하여 Template을 선언합니다. 이는 컴파일러에게 우리가 `T`를 사용하여 다양한 데이터 타입을 나타낼 것임을 알려줍니다.
*   `void swapValues(T& a, T& b)`: 이것은 `T`로 표현되는 모든 데이터 타입의 값을 바꿀 수 있는 일반 함수입니다. 두 개의 레퍼런스(reference)를 매개변수로 받고(원본 값을 수정하기 위해), 임시 변수에도 `T`를 사용합니다.

이제 `swapValues` 함수를 사용하여 정수, double, 또는 기타 다른 데이터 타입에 대해 별도의 swap 함수를 작성할 필요 없이 값을 바꿀 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
int main() 
{
    int x = 5, y = 10;
    double a = 2.5, b = 7.3;

    swapValues(x, y);  // 정수를 바꿉니다
    swapValues(a, b);  // double을 바꿉니다

    return 0;
}

```
{% endcode %}

그리고 기본적으로 이것이 전부입니다. 여러분은 또한 Class templates를 구현해야 합니다. :

### Class templates

C++의 **Class templates**는 **다양한 데이터 타입 또는 객체와 함께 작동할 수 있는 일반적인(Generic) 클래스를 만드는** 방법입니다. 이는 개념적으로 함수 Templates와 유사하지만, 일반 함수를 만드는 대신 일반 클래스를 생성합니다.

다양한 타입의 객체를 담을 수 있는 `Box`라는 일반 컨테이너 클래스를 만들고 싶다고 가정해 봅시다. Class template을 사용하여 이를 달성할 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
template <typename T>
class Box {
private:
    T content;

public:
    Box(const T& item) : content(item) {}

    T getItem() const {
        return content;
    }
};
```
{% endcode %}

*   `template <typename T>`: 이 줄은 Placeholder 타입 `T`를 사용하여 Class template을 선언합니다. 이는 컴파일러에게 `T`가 다양한 데이터 타입 또는 객체 타입을 나타낼 것임을 알려줍니다.
*   `class Box`: 이것은 `Box`라는 일반 클래스의 선언입니다 (기본적으로 다른 모듈에서 했던 고전적인 선언 방식입니다).
*   `T content;`: 이것은 `T` 타입의 멤버 변수이며, `Box`가 담을 수 있는 내용을 나타냅니다.
*   `Box(const T& item) : content(item) {}`: 이것은 `T` 타입의 객체를 매개변수로 받아 `content` 멤버를 초기화하는 생성자입니다.
*   `T getItem() const { return content; }`: 이것은 `Box`의 내용을 검색하는 멤버 함수입니다.

이제 `Box` Class template을 사용하여 다양한 타입의 인스턴스를 만들 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
main() {
    Box<int> intBox(42);            // 정수를 담는 Box
    Box<double> doubleBox(3.14);    // double을 담는 Box
    Box<std::string> stringBox("Hello, World!"); // 문자열을 담는 Box

    int intValue = intBox.getItem();
    double doubleValue = doubleBox.getItem();
    std::string stringValue = stringBox.getItem();

    return 0;
}
```
{% endcode %}

Class templates는 재사용 가능하고 타입에 안전한(type-safe) 컨테이너 클래스 또는 데이터 구조를 만들고자 할 때 특히 유용합니다.

어쨌든 Templates는 이해하기 어려운 개념이 아니며, 곧 익숙해지실 것입니다! 하지만 Templates를 확실하게 이해하시기 바랍니다. 왜냐하면 컨테이너를 다루는 모듈 8에서 Templates가 필요하기 때문입니다. 그때 뵙겠습니다!