---
description: C 구조체란 무엇인가요
---

# 🧱 C Structure

### 서론

**Structure**는 여러 관련 변수들을 한 곳에 모아 그룹화하는 방법입니다.
Structure 내의 각 변수는 Structure의 **멤버**로 알려져 있습니다.

더 명확히 설명하기 위해, 도서 정보를 저장하고자 하는 도서관의 예시를 사용하겠습니다:

*   도서 제목 (Book title)
*   도서 저자 (Book author)
*   도서 ID (Book ID)

### Array와 Structure의 차이점

Array는 `int` Array, `char` Array 등과 같이 동일한 자료형의 여러 변수를 저장할 수 있게 합니다.

Structure는 **서로 다른** 자료형의 여러 변수를 저장할 수 있게 합니다. Structure 안에는 하나의 `int` 멤버, 하나의 `char` 멤버, 여러 개의 `double` 멤버 등을 가질 수 있습니다.

### Structure 생성하기

Structure는 일반적으로 헤더 파일에 선언되므로, 해당 헤더 파일이 포함된 모든 곳에서 사용할 수 있습니다.

Structure는 다음과 같이 **struct** 키워드를 사용하여 생성합니다.

{% code title="main.h" lineNumbers="true" %}
```c
struct [구조체 태그]
{
   멤버 정의;
   멤버 정의;
   ...
   멤버 정의;
} [하나 이상의 구조체 변수];
```
{% endcode %}

#### Structure Tag와 변수 사용하기

Structure tag와 Structure 변수는 예시를 통해 쉽게 설명할 수 있는 개념입니다.
두 사용법의 차이점을 설명해 드리겠습니다.

{% code lineNumbers="true" %}
```c
// Structure Tag s_st1을 사용하는 경우
struct s_st1
{
    int x;
    char c;
} a = {100, 'a'}, b = {50, 'b'};

// Structure Tag가 없는 경우 (익명 구조체)
struct
{
    int x;
    char c;
} c = {100, 'a'}, d = {50, 'b'};
```
{% endcode %}

예상대로 `a`와 `c`는 동일한 내용을 가집니다.
`b`와 `d` 또한 예상대로 동일한 내용을 가집니다.

차이점은 Structure `s_st1`을 기반으로 하는 경우 코드의 어느 시점에서든 새로운 변수를 생성할 수 있지만, 익명 Structure의 경우에는 그렇게 할 수 없다는 사실에 있습니다.

해당 Structure를 기반으로 새로운 변수를 생성하려면 다음을 수행해야 합니다.

<pre class="language-c" data-line-numbers><code class="lang-c">// s_st1 Structure를 위한 새 변수를 선언합니다.
// 이 작업은 코드 내에서 원할 때마다 수행할 수 있습니다.
<strong>struct s_st1 e;
</strong>e = {25, 'e'};
<strong>
</strong>// 익명 Structure를 위한 새 변수를 선언합니다.
// => 원래 Structure 선언 부분으로 이동해야 합니다.
struct
{
    int x;
    char c;
} c = {100, 'a'}, d = {50, 'b'}, f;
// 목록에 새 변수('f')를 추가합니다.
// 완료되면 변수 f에 값을 할당할 수 있습니다.
f = {25, 'f'};
</code></pre>

보시다시피, 코드에서 나중에 새로운 변수를 선언해야 하는 경우 이름을 지정한 Structure를 사용하는 것이 더 쉬울 수 있습니다.

예를 들어, 저희의 `s_book` Structure는 다음과 같이 선언됩니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
struct s_book
{
    char title[50];
    char author[50];
    int  book_id;
};

// 코드 내에서 나중에 이 라인을 사용하여 새로운 Book을 선언할 수 있습니다.
struct s_book LOTR1 = {"The Lord of the Rings 1", "J.R.R. Tolkien", 44003415};
struct s_book LOTR2;

strcpy(LOTR2.title, "The Lord of the Rings 2");
strcpy(LOTR2.author, "J.R.R. Tolkien");
LOTR2.book_id = 44003416;
```
{% endcode %}

{% hint style="info" %}
코드에서 여러 Book을 생성하기 위해 이 Structure를 사용할 것이므로 이름을 지정했다는 점에 유의하십시오.
{% endhint %}

### Structure 멤버 접근하기

Structure의 멤버에 접근하려면 **멤버 접근 연산자**인 `.`을 사용해야 합니다.

이를 사용하려면 Structure 변수 이름과 접근하고자 하는 멤버 사이에 `.`을 넣어야 합니다. 프로그램에서 어떻게 사용되는지 알아보려면 아래 예시를 확인하십시오.

{% code title="main.c" overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <string.h>

struct s_book
{
    char title[50];
    char author[50];
    int  book_id;
};

int main(void)
{
    // 2개의 새로운 Book을 선언합니다.
    struct s_book book1;
    struct s_book book2;

    // book1 멤버를 설정합니다.
    strcpy(book1.title, "The Lord of the Rings 1");
    strcpy(book1.author, "J.R.R. Tolkien");
    book1.book_id = 44003415;
    
    // book2 멤버를 설정합니다.
    strcpy(book1.title, "The Lord of the Rings 2");
    strcpy(book1.author, "J.R.R. Tolkien");
    book1.book_id = 44003416;
    
    // Book 정보를 출력합니다.
    printf("%s - %s - %d\n", book1.title, book1.author, book1.book_id);
    printf("%s - %s - %d\n", book2.title, book2.author, book2.book_id);
    
    return (0);
}
```
{% endcode %}

위 코드가 컴파일되고 실행되면 다음과 같은 결과가 출력됩니다.

{% code overflow="wrap" %}
```
The Lord of the Rings 1 - J.R.R. Tolkien - 44003415
The Lord of the Rings 2 - J.R.R. Tolkien - 44003416
```
{% endcode %}

### Structure Pointer 멤버 접근하기

Structure Pointer의 멤버에 접근하려면, 또 다른 **멤버 접근 연산자**인 `->`를 사용해야 합니다.

이를 사용하려면 Structure 변수 이름과 접근하고자 하는 멤버 사이에 `->`를 넣어야 합니다. 프로그램에서 어떻게 사용되는지 알아보려면 아래 예시를 확인하십시오.

{% code title="main.c" overflow="wrap" lineNumbers="true" %}
```c
#include <stdio.h>
#include <string.h>

struct s_book
{
    char title[50];
    char author[50];
    int  book_id;
};

void printBook(struct s_book *book);

int main(void)
{
    // 2개의 새로운 Book을 선언합니다.
    struct s_book book1;
    struct s_book book2;

    // book1 멤버를 설정합니다.
    strcpy(book1.title, "The Lord of the Rings 1");
    strcpy(book1.author, "J.R.R. Tolkien");
    book1.book_id = 44003415;
    
    // book2 멤버를 설정합니다.
    strcpy(book1.title, "The Lord of the Rings 2");
    strcpy(book1.author, "J.R.R. Tolkien");
    book1.book_id = 44003416;
    
    printBook(&book1);
    printBook(&book2);
    
    return (0);
}

void printBook(struct s_book *book)
{
   printf("%s - %s - %d\n", book->title, book->author, book->book_id);
}
```
{% endcode %}

위 코드가 컴파일되고 실행되면 다음과 같은 결과가 출력됩니다.

```
The Lord of the Rings 1 - J.R.R. Tolkien - 44003415
The Lord of the Rings 2 - J.R.R. Tolkien - 44003416
```

### Structure의 타입 정의하기 (typedef)

저희는 자료형에 대한 별칭 이름(alias name)을 생성하기 위해 `typedef` 키워드를 사용합니다. 이는 변수를 선언하는 구문을 단순화하기 위해 Structure와 함께 흔히 사용됩니다.

다음 예시를 살펴보십시오.

{% code overflow="wrap" lineNumbers="true" %}
```c
struct s_point
{
  int  x;
  int  y;  
};

int main(void)
{
  struct s_point p1, p2;
}
```
{% endcode %}

`typedef`를 사용하여 단순화된 구문으로 동등한 코드를 작성할 수 있습니다.

{% code overflow="wrap" lineNumbers="true" %}
```c
typedef struct s_point
{
    int x;
    int y;
} t_point;

int main(void)
{
    t_point p1, p2;
}
```
{% endcode %}

### 네이밍 규칙 (Naming convention)

42 Norm을 준수하기 위해 저희는 Structure와 `typedef`의 이름을 특정 방식으로 지정해야 합니다.

이는 또한 코드를 훨씬 쉽게 이해할 수 있게 해줍니다.

Structure 이름은 `s_`로 시작해야 하며, `typedef` 이름은 `t_`로 시작해야 합니다.

```c
// 올바르지 않은 Structure
struct point_structure
{
    int x;
    int y;
};

// 올바른 Structure
struct s_point
{
    int x;
    int y;
};

// 올바르지 않은 typedef
typedef struct s_point
{
    int x;
    int y;
} point;

// 올바른 typedef
typedef struct s_point
{
    int x;
    int y;
} t_point;
```

### Structure를 사용하는 이유

동일한 예시를 사용하여, Book에 대한 정보(제목, 저자, ID)를 저장하고 싶다고 가정해 보겠습니다. 이 정보를 저장하기 위해 `title`, `author`, `id`와 같은 개별 변수를 생성할 수 있습니다.

만약 두 권 이상의 Book 정보를 저장해야 한다면 어떻게 될까요? 이제 각 Book 정보마다 `title2`, `author2`, `id2` 등과 같은 개별 변수를 만들어야 합니다.

더 나은 접근 방식은 관련된 모든 정보를 단일 이름의 `s_book` Structure 아래에 모아 컬렉션으로 만들고 이를 모든 Book에 사용하는 것입니다.

#### 출처

{% embed url="https://www.tutorialspoint.com/cprogramming/c_structures.htm" %}

{% embed url="https://www.programiz.com/c-programming/c-structures" %}

{% embed url="https://www.geeksforgeeks.org/structures-c/" %}

{% embed url="https://stackoverflow.com/questions/44180120/what-is-the-use-of-struct-tag-name-in-c-programming" %}