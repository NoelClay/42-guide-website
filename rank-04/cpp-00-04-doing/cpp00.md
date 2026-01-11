# CPP00

이 CPP 모듈은 C++의 기본 사항과 이 프로그래밍 언어의 주요 원칙들을 학습하도록 돕기 위해 존재합니다.

이 모듈에서 언급된 모든 주제에 대해 최선을 다해 안내해 드리겠습니다.

### 주요 주제

주제의 첫 페이지에 있는 주요 내용들을 살펴보겠습니다:

```
Namespaces, classes, member functions, stdio streams,
initialization lists, static, const, and some other basic
stuff
```

### Namespaces

Namespace는 코드 내의 변수/메서드 또는 기타 모든 요소의 범위를 지정하는 방법입니다.

두 가지 다른 방식으로 Namespace를 사용하는 간단한 예시를 살펴보겠습니다.

```cpp
#include <iostream>
using namespace std; 
// 이 줄은 namespace를 std로 설정합니다.
// 이는 iostream 헤더에 있는 함수/변수를 사용하기 전에 
// std::를 작성할 필요가 없음을 의미합니다.

// 이 코드는 namespace를 전역적으로 사용하는 예시입니다.
int main(void)
{
    cout << "Hello World!" << endl;
    return (0);
}
```

```cpp
#include <iostream>
// 이번에는 
// using namespace std;
// 줄을 추가하지 않았다는 점에 유의하십시오.

// 이 코드는 위 코드와 정확히 동일하게 작동합니다.
int main(void)
{
    std::cout << "Hello World!" << std::endl;
    return (0);
}
```

이 두 가지는 Namespace를 전역적으로 사용하는 경우와 사용하지 않는 경우의 차이점을 보여주는 간단한 예시입니다. 이 예시 자체는 크게 흥미롭지 않지만, 다음 코드를 살펴보십시오.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

class FirstClass:
{
    public:
        FirstClass();
        ~FirstClass();
        void    print(std::string txt);
};

class SecondClass:
{
    public:
        SecondClass();
        ~SecondClass();
        void    print(void);
};

int main(void)
{
    // 이제 print 함수를 호출하려면 namespace를 명시해야 합니다.
    // 이 namespace는 호출하고자 하는 print 함수의 class에 해당합니다.
    // 예를 들어, print(void) 함수를 호출하려면 다음과 같이 작성해야 합니다.
    SecondClass::print(void);
    // 그리고 print(std::string txt) 함수를 호출하려면 다음과 같이 작성합니다.
    FirstClass::print("My text");
    
    // 만약 다음과 같이 시도한다면
    FirstClass::print(void);
    // 이 함수는 FirstClass namespace에 존재하지 않으므로 작동하지 않을 것입니다.
    // 다음 코드도 마찬가지입니다.
    SecondClass::print("Text");
    // 이유는 동일합니다.
    
    return (0);
}
```
{% endcode %}

{% hint style="warning" %}
참고: 위의 코드는 컴파일되지 않지만, 개념을 이해하는 데 도움이 될 것입니다.
{% endhint %}

동일한 이름을 가진 여러 함수를 가질 수 있으며, 이 경우 올바른 함수를 호출하기 위해 Namespace를 명시할 수 있습니다.

인터넷에서 더 많은 정보를 검색하고 작은 C++ 프로젝트를 구축하여 이것이 정확히 어떻게 작동하는지 이해하도록 노력해 보십시오.

출처: [Microsoft의 C++ Namespace 설명](https://learn.microsoft.com/en-us/cpp/cpp/namespaces-cpp?view=msvc-170)

### Constructor functions & initialization lists

Constructor function은 Class 내부에 있는 특별한 메서드로서, 해당 Class의 새 `object`를 생성할 때마다 자동으로 호출됩니다.

간단한 Constructor는 다음과 같이 구현할 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
class Class {     // Class
  public:           // 접근 지정자 (Access specifier)
    Class() {     // Constructor
      std::cout << "Hello World!";
    }
};

int main() {
  Class Obj;    // Class의 object를 생성합니다. 이렇게 하면 constructor가 자동으로 호출됩니다.
  return 0;
}
```
{% endcode %}

{% hint style="warning" %}
Constructor는 항상 Class 자체와 동일한 이름을 가지며 아무것도 반환하지 않습니다.
{% endhint %}

다른 예시를 살펴보겠습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
class Car
{
    public:
        Car(); // Constructor 선언
        Car(std::string pbrand, std::string pmodel, int pyear); // 두 번째 constructor
        std::string brand;
        std::string model;
        int year;
};

Car::Car(void)
{
    std::cout << "Hello world !" << std::endl;
    brand = "";
    model = "";
    year= 0;
    return ;
}

Car::Car(std::string pbrand, std::string pmodel, int pyear)
{
    brand = pbrand;
    model = pmodel;
    year = pyear;
    return ;
}

int main() {
  // Car object들을 생성하고 다른 값으로 constructor를 호출합니다.
  Car car1();
  Car car2("Ford", "Mustang", 1969);

  // 값 출력
  std::cout << car1.brand << " " << car1.model << " " << car1.year << std::endl;
  std::cout << car2.brand << " " << car2.model << " " << car2.year << std::endl;
  return 0;
}
```
{% endcode %}

`main` 함수에서 두 `car` 모두 `Car object`로 생성되지만, 그중 하나는 모든 속성이 올바른 값으로 직접 설정되고, 다른 하나는 기본값으로 설정될 것입니다.

Constructor function은 특정 Class의 새 `object`를 생성할 때 호출되므로, Constructor 내에서 직접 설정될 기본값을 지정할 수 있습니다. 이렇게 하면 사용하려고 할 때 해당 값들이 올바르다는 확신을 가질 수 있습니다.

다음은 제가 모듈 중 하나에서 사용했던 Constructor들입니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
// 기본 constructor, 모든 값은 호출자에 의해 설정됩니다.
ClapTrap::ClapTrap(void) : _name("Default"), _hp(10), _ep(10), _ad(0)
{
	std::cout << "Default ClapTrap constructor called for ";
	std::cout << _GREEN << this->_name;
	std::cout << _RESET << std::endl;
	return ;
}

// 이름이 지정된 constructor, 다른 값들은 호출자에 의해 설정됩니다.
ClapTrap::ClapTrap(std::string name) : _name(name), _hp(10), _ep(10), _ad(0)
{
	std::cout << "Named ClapTrap constructor called for ";
	std::cout << _GREEN << this->_name;
	std::cout << _RESET << std::endl;
	return ;
}

// 전체 constructor, 모든 값은 호출자에 의해 설정됩니다.
ClapTrap::ClapTrap(std::string name, uint hp, uint ep, uint ad) : _name(name), _hp(hp), _ep(ep), _ad(ad)
{
	std::cout << "Full ClapTrap Constructor called for ";
	std::cout << _GREEN << this->_name << _RESET;
	std::cout << " with " << _YELLOW << this->_hp << _RESET << " hp, ";
	std::cout << _YELLOW << this->_ep << _RESET << " ep, " << _YELLOW << this->_ad << _RESET << " ad.";
	std::cout << std::endl;
	return ;
}
```
{% endcode %}

왜 Constructor 매개변수 뒤에 세미콜론과 속성 이름이 붙어 있을까요?

여기에 Constructor function과 관련된 또 다른 용어인 `initialization lists`가 있습니다.

이것이 바로 `initialization lists`이며, 괄호 안의 값을 기반으로 Class 속성을 설정하는 역할을 합니다.

{% code overflow="wrap" %}
```cpp
ClapTrap::ClapTrap(std::string name, uint hp, uint ep, uint ad) : _name(name), _hp(hp), _ep(ep), _ad(ad)
```
{% endcode %}

이 줄은 다음 코드와 동일합니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
ClapTrap::ClapTrap(std::string name, uint hp, uint ep, uint ad)
{
    _name = name;
    _hp = hp;
    _ep = ep;
    _ad = ad;
}
```
{% endcode %}

이것은 Constructor function 내에서 값을 할당하는 더 쉽고 더 나은 방법입니다.

### Classes

위에 연결된 **w3schools** C++ 소개 가이드를 읽지 않았다면, 지금 가서 읽어보십시오. `Class`는 C++ 및 `OOP`의 핵심 부분이므로 이에 대한 많은 상세 정보가 있습니다.

여기서는 이 모듈의 파일 구조를 보여드리겠습니다. 42 Norm과 C++ 모범 사례를 따르면 여러분이 만들 모든 C++ 프로젝트에서 다음과 유사한 구조를 갖게 될 것입니다.

```
cpp00/
├─ ex00/
│  ├─ ...
├─ ex01/
│  ├─ Makefile
│  ├─ main.cpp
│  ├─ PhoneBook.cpp
│  ├─ PhoneBook.hpp
│  ├─ Contact.cpp
│  ├─ Contact.hpp
```

`Class`를 사용하는 프로젝트를 완성하면, Class 정의를 포함하는 `hpp` 파일과 그에 해당하는 Class 선언을 포함하는 `cpp` 파일을 가지게 될 것입니다.

### Member functions

Member function은 Class의 멤버로 선언된 `operator` 또는 함수입니다.
[Namespaces](cpp00.md#namespaces)의 코드 예시에서 두 `print` 함수 모두 `Member function`입니다.

이보다 더 많은 `Member function`을 가질 수 있습니다. 다음 `Class` 선언을 살펴보십시오 (지금은 다소 복잡해 보일 수 있지만 실제로는 아주 간단합니다).

{% code overflow="wrap" lineNumbers="true" %}
```cpp
class	Bureaucrat
{
	public:
		Bureaucrat(void);
		Bureaucrat(std::string name);
		Bureaucrat(std::string name, int grade);
		Bureaucrat(const Bureaucrat &b);
		~Bureaucrat();
		Bureaucrat	&operator=(const Bureaucrat &b);

		void		setName(std::string name);
		void		setGrade(int grade);
		std::string	getName(void) const;
		int			getGrade(void) const;
		void		incrementGrade(void);
		void		decrementGrade(void);

		void		signForm(AForm &f);
		void		executeForm(AForm &f);
	private:
		std::string	_name;
		int			_grade;
};
```
{% endcode %}

이 예시에서는 9개의 `Member function`, 4개의 `Constructor function`, 1개의 `Destructor function`, 그리고 1개의 `operator member`가 있음을 확인할 수 있습니다.