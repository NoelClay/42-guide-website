# 모듈 0

### 주제 :

{% code overflow="wrap" %}
```
Assignment name  : cpp_module00
Expected files   : Warlock.cpp Warlock.hpp
--------------------------------------------------------------------------------

Make a Warlock class. It has to be in Coplien's form.

It has the following private attributes :
* name (string)
* title (string)

Since they're private, you will write the following getters :
* getName, returns a reference to constant string
* getTitle, returns a reference to constant string

Both these functions will have to be callable on a constant Warlock.

Create the following setter: 
* setTitle, returns void and takes a reference to constant string

Your Warlock will also have, in addition to whatever's required by Coplien's
form, a constructor that takes, in this order, its name and title. Your Warlock
will not be able to be copied, instantiated by copy, or instantiated without a
name and a title.

For example :

Warlock bob;                            // 컴파일되지 않습니다.
Warlock bob("Bob", "the magnificent");  // 컴파일됩니다.
Warlock jim("Jim", "the nauseating");   // 컴파일됩니다.
bob = jim;                              // 컴파일되지 않습니다.
Warlock jack(jim);                      // 컴파일되지 않습니다.

Upon creation, the Warlock says :

<NAME>: This looks like another boring day.

Of course, whenever we use placeholders like <NAME>, <TITLE>, etc...
in outputs, you will replace them by the appropriate value. Without the < and >.

When he dies, he says:

<NAME>: My job here is done!

Our Warlock must also be able to introduce himself, while boasting with all its
might.

So you will write the following function:
* void introduce() const;

It must display:

<NAME>: I am <NAME>, <TITLE>!

Here's an example of a test main function and its associated output:

int main()
{
  Warlock const richard("Richard", "Mistress of Magma");
  richard.introduce();
  std::cout << richard.getName() << " - " << richard.getTitle() << std::endl;

  Warlock* jack = new Warlock("Jack", "the Long");
  jack->introduce();
  jack->setTitle("the Mighty");
  jack->introduce();

  delete jack;

  return (0);
}

~$ ./a.out | cat -e
Richard: This looks like another boring day.$
Richard: I am Richard, Mistress of Magma!$
Richard - Mistress of Magma$
Jack: This looks like another boring day.$
Jack: I am Jack, the Long!$
Jack: I am Jack, the Mighty!$
Jack: My job here is done!$
Richard: My job here is done!$
~$
```
{% endcode %}

---

Warlock 클래스를 생성하십시오. 이 클래스는 Coplien's form을 따라야 합니다.

다음과 같은 private 속성을 가져야 합니다:
* name (string)
* title (string)

이들은 private 속성이므로, 다음의 getter를 작성해야 합니다:
* getName: 상수 string에 대한 const 레퍼런스를 반환합니다.
* getTitle: 상수 string에 대한 const 레퍼런스를 반환합니다.

이 두 함수는 모두 상수 Warlock 객체에 대해서도 호출 가능해야 합니다.

다음 setter를 생성하십시오:
* setTitle: void를 반환하며 상수 string에 대한 레퍼런스를 받습니다.

Warlock은 Coplien's form이 요구하는 모든 것 외에도, 이름(name)과 타이틀(title)을 이 순서대로 받는 생성자를 가져야 합니다. Warlock은 복사되거나, 복사 생성되거나, 이름과 타이틀 없이 인스턴스화될 수 없습니다.

예시:

```cpp
Warlock bob;                            // 컴파일되지 않습니다.
Warlock bob("Bob", "the magnificent");  // 컴파일됩니다.
Warlock jim("Jim", "the nauseating");   // 컴파일됩니다.
bob = jim;                              // 컴파일되지 않습니다.
Warlock jack(jim);                      // 컴파일되지 않습니다.
```

생성 시, Warlock은 다음과 같이 말합니다:

`<NAME>: This looks like another boring day.`

물론, 출력에서 `<NAME>`, `<TITLE>` 등과 같은 플레이스홀더를 사용할 때마다, 여러분은 이를 적절한 값으로 대체해야 합니다. `<`와 `>` 기호는 사용하지 마십시오.

소멸 시, Warlock은 다음과 같이 말합니다:

`<NAME>: My job here is done!`

Warlock은 또한 자신의 모든 힘을 과시하며 자신을 소개할 수 있어야 합니다.

따라서 다음 함수를 작성해야 합니다:
* `void introduce() const;`

이 함수는 다음을 표시해야 합니다:

`<NAME>: I am <NAME>, <TITLE>!`

다음은 테스트 `main` 함수와 그에 따른 출력의 예시입니다:

```cpp
int main()
{
  Warlock const richard("Richard", "Mistress of Magma");
  richard.introduce();
  std::cout << richard.getName() << " - " << richard.getTitle() << std::endl;

  Warlock* jack = new Warlock("Jack", "the Long");
  jack->introduce();
  jack->setTitle("the Mighty");
  jack->introduce();

  delete jack;

  return (0);
}

~$ ./a.out | cat -e
Richard: This looks like another boring day.$
Richard: I am Richard, Mistress of Magma!$
Richard - Mistress of Magma$
Jack: This looks like another boring day.$
Jack: I am Jack, the Long!$
Jack: I am Jack, the Mighty!$
Jack: My job here is done!$
Richard: My job here is done!$
~$
```

<details>

<summary>Warlock.hpp</summary>

```
#pragma once

#include <iostream>

class Warlock
{
    private:
        std::string name;
        std::string title;

        Warlock();
        Warlock(Warlock const &other);
        Warlock &operator=(Warlock const &other);
    public:
        Warlock(std::string const &name, std::string const &title);
        ~Warlock();

        std::string const &getName() const;
        std::string const &getTitle() const;

        void setTitle(std::string const &title);

        void introduce() const;
};
```

</details>

<details>

<summary>Warlock.cpp</summary>

{% code overflow="wrap" lineNumbers="true" %}
```cpp
#include "Warlock.hpp"

Warlock::Warlock(std::string const &name, std::string const &title)
{
    this->name = name;
    this->title = title;
    std::cout << this->name << ": This looks like another boring day.\n";
}

Warlock::~Warlock() {std::cout << this->name << ": My job here is done!\n";}

std::string const &Warlock::getName() const { return (this->name);}
std::string const &Warlock::getTitle() const { return (this->title);}

void Warlock::setTitle(std::string const &title) { this->title = title;}

void Warlock::introduce() const { std::cout << this->name << ": I am " << this->name << ", " << this->title << "!\n";}
```
{% endcode %}

</details>