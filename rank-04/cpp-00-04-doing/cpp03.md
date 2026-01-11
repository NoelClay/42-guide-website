# CPP03

### 주요 주제

```
Inheritance
```

### Inheritance

이제 _Inheritance(상속)_가 무엇을 의미하는지 살펴보겠습니다.

Inheritance는 한 Class가 다른 Class에게 특성을 물려줄 수 있는 능력을 말합니다.

기존 Class로부터 새로운 Class가 생성되는데, 이 새로운 Class를 _Child Class_ 또는 _Derived Class_라고 부르며, 이 Class를 생성한 기존 Class는 _Base Class_ 또는 _Parent Class_라고 불립니다.

#### Inheritance는 언제 유용할까요

차량 Inventory를 관리하는 작은 프로그램을 만들어야 한다고 가정해 보겠습니다. 이 경우, _Cars_용 Class, _Trucks_용 Class, _Motorbikes_용 Class 등을 만들게 될 것입니다.

<figure><img src="../../.gitbook/assets/cpp-03-classes-1.svg" alt=""><figcaption><p>Inheritance가 없는 Class 예시</p></figcaption></figure>

위에서 보듯이, 모든 종류의 Vehicle은 동일한 기본 Properties를 가지고 있지만, 일부는 추가적인 Properties를 필요로 할 수 있습니다.

새로운 Vehicle 유형을 추가해야 할 때마다 동일한 Properties를 계속해서 반복 입력하고 싶으신가요? 그렇지 않을 것입니다. 그리고 이것이 Inheritance가 존재하는 이유 중 하나입니다.

<figure><img src="../../.gitbook/assets/cpp-03-classes-2.svg" alt=""><figcaption><p>Inheritance를 사용한 Class 예시</p></figcaption></figure>

이것이 더 좋지 않습니까?
여기서 우리는 다른 Class들과 마찬가지로 몇 가지 Properties를 가진 Base Class인 _Vehicle_을 선언합니다. 그런 다음, _Car_ Class를 생성할 때, 이 Class가 _Vehicle_의 사용 가능한 Properties를 확장한다고 명시합니다. 새로운 _Car_를 선언하면, _Car_가 Base Class를 확장(extends)했기 때문에 Base Class의 모든 Properties에 접근하고 설정할 수 있습니다. 다음 코드를 살펴보겠습니다.

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include <iostream>

// 우리의 Base Vehicle Class를 선언합니다.
class Vehicle
{
	public:
		Vehicle(std::string type);
		std::string type;
	private:
};

// 우리의 Vehicle Class를 public하게 확장(extends)하는 Car Class를 선언합니다.
class Car : public Vehicle
{
	public:
		Car(void);
	private:
};

// 출력 Stream을 위한 간단한 Operator Overload입니다.
// 보시다시피, 저희는 << Operator의 오른쪽 부분에 Vehicle이 필요하다고 명시하고 있습니다.
std::ostream &operator<<(std::ostream &o, const Vehicle &v)
{
	o << "This is a vehicle of type " << v.type;
	return (o);
}

// 생성자 (Constructors)
Vehicle::Vehicle(std::string type): type(type) {}
Car::Car(void) : Vehicle("Car") {}

int	main(void)
{
	Car car = Car();
	std::cout << car << std::endl;
	return (0);
}
```
{% endcode %}

이 코드에서 우리의 _Car_는 _Vehicle_의 Sub-Class입니다. 저희는 _Vehicle_ Class로부터 파생된 모든 Sub-Class에서 _Vehicle_ Class의 모든 Properties와 Functions에 접근할 수 있습니다. 만약 우리가 다른 Vehicle 유형을 만들고 싶다면, 단순히 또 다른 Class, 예를 들어 _Truck_을 만들고 _Car_ Class에서 했던 것과 동일하게 처리할 수 있습니다.

#### Car Constructor

`Car` Class는 매개변수를 받지 않는 자체 Constructor를 가지고 있습니다. 이 Constructor의 주요 역할은 `Car` Class를 초기화하고, Parent Class인 `Vehicle` Class Constructor를 하드코딩된 문자열 "Car"와 함께 호출하여 그 Type을 설정하는 것입니다.

```cpp
Car::Car(void) : Vehicle("Car") {}
```

이것은 `Car` 객체가 생성될 때마다 `Vehicle` Constructor를 통해 자동으로 "Car" Type을 얻게 된다는 의미입니다. `void` 키워드는 `Car` Constructor에 매개변수가 필요 없음을 나타냅니다. `Car` Constructor는 Initialization List를 사용하여 미리 정해진 Type으로 Parent `Vehicle` Constructor를 호출합니다.

### Access Specifier (접근 지정자)

Access Specifier는 Class Member의 가시성과 접근성을 제어하므로, Object-Oriented Programming에서 중요한 역할을 합니다. 세 가지 유형의 Access Specifier가 있습니다: `public`, `protected`, 그리고 `private`입니다.

*   `public` Inheritance는 Base Class의 public 및 protected Member들을 Derived Class에서 각각 public 및 protected로 만듭니다. 이는 Derived Class에서 동일한 수준의 접근 권한이 유지됨을 의미합니다.
*   `protected` Inheritance는 Base Class의 public 및 protected Member들을 Derived Class에서 protected로 만듭니다. 이는 Class 계층 구조 외부에서는 이러한 Member들에 대한 접근을 제한하는 동시에, 해당 Class와 그 Derived Class 내에서는 접근 가능하도록 유지합니다.
*   `private` Inheritance는 Base Class의 public 및 protected Member들을 Derived Class에서 private로 만듭니다. 이는 Derived Class의 객체로부터 이러한 Member들에 더 이상 접근할 수 없으며, 오직 Derived Class 내부에서만 접근 가능함을 의미합니다 (본질적으로 상속된 모든 Member들이 Derived Class의 private Member가 됩니다).

다음은 Inheritance에서 다양한 Access Specifier를 보여주는 예시입니다.

```cpp
class Base {
public:
    int publicVar;
protected:
    int protectedVar;
private:
    int privateVar;
};

class PublicDerived : public Base {
    // publicVar는 public입니다.
    // protectedVar는 protected입니다.
    // privateVar는 접근할 수 없습니다.
};

class ProtectedDerived : protected Base {
    // publicVar는 protected입니다.
    // protectedVar는 protected입니다.
    // privateVar는 접근할 수 없습니다.
};

class PrivateDerived : private Base {
    // publicVar는 private입니다.
    // protectedVar는 private입니다.
    // privateVar는 접근할 수 없습니다.
};
```

Class의 Encapsulation과 적절한 계층 구조를 보장하기 위해 올바른 Access Specifier를 선택하는 것이 필수적입니다. 이것은 Class가 서로 그리고 프로그램의 나머지 부분과 상호 작용하는 방식에 중대한 영향을 미칠 수 있습니다.

### Advanced topics (심화 주제)

#### Multiple Inheritance (다중 상속)

Multiple Inheritance는 Class가 둘 이상의 Parent Class로부터 특성과 Behaviors를 상속받을 수 있는 일부 Object-Oriented Programming 언어의 기능입니다. 이를 통해 다양한 Class로부터 여러 Behaviors를 통합하는 새로운 Class를 만들 수 있습니다. C++에서 Multiple Inheritance는 복잡한 계층 구조와 함께, Class가 여러 경로를 통해 동일한 Base Class를 상속받는 Diamond Problem을 유발할 수 있습니다. 다음은 C++의 간단한 Syntax 예시입니다.

```cpp
class Base1 {
public:
    void function1() {}
};

class Base2 {
public:
    void function2() {}
};

class Derived : public Base1, public Base2 {
    // Base1으로부터 function1을, Base2로부터 function2를 상속받습니다.
};

int main() {
    Derived derivedObject;
    derivedObject.function1(); // Base1으로부터
    derivedObject.function2(); // Base2로부터
}
```

Multiple Inheritance를 사용할 때는 발생할 수 있는 복잡성과 잠재적인 모호성을 관리하고, 올바른 Functions 또는 Properties에 접근하는지 확인하는 것이 중요합니다.

#### Diamond Inheritance Problem (다이아몬드 상속 문제)

이 모듈의 마지막 Exercise 이름이 `DiamondTrap`이라는 것을 기억하십니까? 여기서 연관성을 찾아볼 수 있을 것입니다.

Diamond Inheritance는 Subclass 또는 Derived Class가 하나의 Base Class를 상속받는 두 Class로부터 상속받을 때 Object-Oriented Programming에서 발생합니다. Class Diagram으로 시각화하면 다이아몬드와 유사한 모양이 만들어집니다.

**예시:**

```cpp
class Base {
public:
    void baseMethod() {}
};

class Derived1 : public Base {
    // Base로부터 baseMethod를 상속받습니다.
};

class Derived2 : public Base {
    // Base로부터 baseMethod를 상속받습니다.
};

class SubDerived : public Derived1, public Derived2 {
    // Derived1과 Derived2 모두로부터 baseMethod를 상속받으려고 시도합니다.
    // baseMethod가 계층 구조 내 두 곳에 존재하기 때문에 모호성을 유발할 수 있습니다.
};
```

`SubDerived`가 `baseMethod`를 사용하려고 할 때, 컴파일러는 `Derived1`의 `baseMethod` 버전을 사용해야 할지, 아니면 `Derived2`의 버전을 사용해야 할지 확신하지 못할 수 있습니다. 이러한 모호성이 Diamond Problem의 핵심입니다.

**Virtual Inheritance (가상 상속)을 통한 해결책:**

Virtual Inheritance를 사용하면 Base Class의 단일 Instance만 모든 Derived Class에 의해 상속되어야 함을 지정할 수 있으므로, 확장된 Class 계층 구조에 Base Class의 여러 '복사본'이 포함되는 것을 방지할 수 있습니다.

```cpp
class Base {
public:
    void baseMethod() {}
};

class Derived1 : virtual public Base {
    // Base로부터 baseMethod를 Virtual하게 상속받습니다.
};

class Derived2 : virtual public Base {
    // Base로부터 baseMethod를 Virtual하게 상속받습니다.
};

class SubDerived : public Derived1, public Derived2 {
    // baseMethod의 단일 Instance를 상속받습니다.
};
```

Virtual Inheritance를 사용하면, `SubDerived`는 `Base` Class로부터 상속된 하나의 `baseMethod`만 갖게 되어 모호성을 해결하고 Diamond Problem을 피할 수 있습니다.

Inheritance에 대한 더 많은 정보는 [IBM 문서](https://www.ibm.com/docs/en/zos/2.4.0?topic=reference-inheritance-c-only) 또는 [geekforgeeks 문서](https://www.geeksforgeeks.org/inheritance-in-c/)에서 찾으실 수 있습니다.