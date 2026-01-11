# ▪️ 사용 가능한 함수

`printf`를 기억하십니까? 네, 맞습니다. 여러분이 직접 재구현하셨죠. 잘 하셨습니다. 하지만 이 프로젝트에서는 `stdlib`에 있는 진짜 `printf`를 사용할 수 있습니다.

또한 다른 많은 함수에도 접근할 수 있지만, 이 프로젝트에서는 여러분의 `libft`를 **사용할 수 없습니다**.

이 함수들 중 일부는 보너스 파트에서만 사용됩니다. 저는 이 함수들을 사용하지 않았으므로, 사용 방법을 직접 찾아보시기 바랍니다. 이 프로젝트를 위해 프로그램을 컴파일할 때는 `-pthread` 플래그를 사용해야 합니다.

### usleep()

```c
int usleep(useconds_t usec);
```

`usleep()`는 호출 프로세스를 지정된 마이크로초(microseconds) 동안 대기(sleep) 상태로 만드는 C 표준 라이브러리의 함수입니다.

<details>

<summary>usleep() 예시</summary>

```c
#include <stdio.h>
#include <unistd.h>

int main(void) {
    printf("Sleeping for 500000 microseconds...\n");
    usleep(500000);
    printf("Done sleeping.\n");
    return 0;
}
```

</details>

### gettimeofday()

```c
int gettimeofday(struct timeval *restrict tv, struct timezone *restrict tz);
```

_**gettimeofday()**_ 함수는 시스템의 클럭 시간을 가져옵니다. 현재 시각은 1970년 1월 1일 00:00:00 (Unix Epoch) 이후 경과된 초 및 마이크로초로 표현됩니다.

<details>

<summary>gettimeofday()</summary>

```c
#include <sys/time.h>
#include <stdio.h>

int main() {
  struct timeval current_time;
  gettimeofday(&current_time, NULL);
  printf("seconds : %ld\nmicro seconds : %ld",
    current_time.tv_sec, current_time.tv_usec);

  return 0;
}
```

첫 번째 매개변수는 `timeval` 구조체를 가리키는 Pointer입니다. `timeval` 구조체는 `<sys/time.h>` 헤더 파일에 다음과 같이 정의되어 있습니다.

```c
struct    timeval  {
  time_t        tv_sec ;   // 초를 위해 사용됩니다.
  suseconds_t       tv_usec ;   // 마이크로초를 위해 사용됩니다.
}
```

두 번째 인수는 **항상** `NULL`로 설정되어야 합니다. 이 두 번째 인수는 더 이상 사용되지 않으며 하위 호환성을 위해서만 존재합니다.

</details>

**gettimeofday()**에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man2/settimeofday.2.html)에서 찾을 수 있습니다.

### pthread\_create()

```c
int pthread_create(pthread_t *restrict thread,
       const pthread_attr_t *restrict attr,
       void *(*start_routine)(void *),
       void *restrict arg);
```

`pthread_create()` 함수는 호출하는 프로세스 내에서 새로운 스레드(thread)를 시작합니다. 새 스레드는 `start_routine()`을 호출하여 실행을 시작하며, `arg`는 `start_routine()`의 유일한 인수로 전달됩니다.

`pthread_create()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_create.3.html)에서 찾을 수 있습니다.

### pthread\_join()

```c
int pthread_join(pthread_t thread, void **retval);
```

```
       pthread_join() 함수는 thread에 의해 지정된
       스레드가 종료될 때까지 기다립니다. 만약 해당 스레드가 이미 종료되었다면,
       pthread_join()은 즉시 반환됩니다. thread에 의해 지정된
       스레드는 joinable(결합 가능)해야 합니다.
```

`pthread_join()` 함수는 `thread`에 의해 지정된 스레드가 종료될 때까지 기다립니다. 해당 스레드가 이미 종료되었다면 `pthread_join()`은 즉시 반환됩니다.

<details>

<summary>pthread_create() 및 pthread_join() 예시</summary>

```c
#include <pthread.h>
#include <stdlib.h>
#include <stdio.h>

void *thread(void *arg) {
  char *ret;
  printf("thread() entered with argument '%s'\n", arg);
  if ((ret = (char*) malloc(20)) == NULL) {
    perror("malloc() error");
    exit(2);
  }
  strcpy(ret, "This is a test");
  pthread_exit(ret);
}

main() {
  pthread_t thid;
  void *ret;

  if (pthread_create(&thid, NULL, thread, "thread 1") != 0) {
    perror("pthread_create() error");
    exit(1);
  }

  if (pthread_join(thid, &ret) != 0) {
    perror("pthread_create() error");
    exit(3);
  }

  printf("thread exited with '%s'\n", ret);
}
```

이 예시에서는 `pthread_create()` 함수를 사용하여 `thread()`라는 함수로 시작되는 새로운 스레드를 생성합니다.

그런 다음, 스레드가 종료될 때까지 기다린 후 스레드의 반환 값을 출력합니다.

</details>

`pthread_join()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_join.3.html)에서 찾을 수 있습니다.

### pthread\_mutex\_init()

{% code overflow="wrap" %}
```c
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
```
{% endcode %}

`pthread_mutex_init()` 함수는 `attr`에 의해 지정된 속성을 사용하여 `mutex`에 의해 참조되는 뮤텍스(mutex)를 초기화합니다. `attr`이 `NULL`인 경우, 기본 뮤텍스 속성이 사용됩니다. `mutex`가 성공적으로 초기화되면 뮤텍스 상태는 `initialized` 및 `unlocked`가 됩니다.

<details>

<summary>pthread_mutex_init() 예시</summary>

```c
#include <pthread.h>

int main(void)
{
    pthread_mutex_t mutex;

    // 뮤텍스를 초기화합니다.
    pthread_mutex_init(&mutex, NULL);
}
```

위의 예시는 `pthread_mutex_init()` 함수를 사용하여 우리가 원하는 모든 스레드에서 사용할 수 있는 새로운 `mutex`를 초기화하는 방법을 보여줍니다.

</details>

`pthread_mutex_init()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_mutex_destroy.3p.html)에서 찾을 수 있습니다.

### pthread\_mutex\_destroy()

```c
int pthread_mutex_destroy(pthread_mutex_t *mutex);
```

`pthread_mutex_destroy()` 함수는 `mutex`에 의해 참조되는 뮤텍스 객체를 파괴합니다. 뮤텍스 객체는 초기화되지 않은 상태(uninitialized)가 되며, 필요한 경우 `pthread_mutex_init()`을 사용하여 다시 초기화할 수 있습니다.

<details>

<summary>pthread_mutex_init() 및 pthread_mutex_destroy() 예시</summary>

{% code overflow="wrap" %}
```c
#include <pthread.h>
#incluce <stdio.h>

int main(void)
{
    pthread_mutex_t mutex;
    
    // 뮤텍스를 초기화합니다.
    pthread_mutex_init(&mutex, NULL);
    
    printf("You can use the mutex from now on");
    
    // 뮤텍스를 파괴합니다.
    pthread_mutex_destroy(&mutex);
}
```
{% endcode %}

</details>

`pthread_mutex_destroy()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_mutex_destroy.3p.html)에서 찾을 수 있습니다.

### pthread\_mutex\_lock()

```c
int pthread_mutex_lock(pthread_mutex_t *mutex);
```

`pthread_mutex_lock()` 함수는 `mutex`에 의해 참조되는 뮤텍스를 잠급니다(lock).

`pthread_mutext_lock()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_mutex_lock.3p.html)에서 찾을 수 있습니다.

### pthread\_mutex\_unlock()

```c
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

`pthread_mutex_unlock()` 함수는 `mutex`에 의해 참조되는 뮤텍스의 잠금을 해제합니다(unlock).

<details>

<summary>pthread_mutex_lock() 및 pthread_mutex_unlock() 예시</summary>

```c
#include <pthread.h>
#include <stdio.h>

int main(void)
{
    pthread_mutex_t fork_mutex;
    
    pthread_mutex_init(&fork_mutex, NULL);
    
    pthread_mutex_lock(&fork_mutex);
    set_unavailable(fork);
    pthread_mutex_unlock(&fork_mutex);
    
    pthread_mutex_destroy(&fork_mutex);
    
    return (0);
}
```

</details>

`pthread_mutex_unlock()`에 대한 더 자세한 정보는 [여기](https://man7.org/linux/man-pages/man3/pthread_mutex_lock.3p.html)에서 찾을 수 있습니다.
