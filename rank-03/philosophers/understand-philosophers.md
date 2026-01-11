# ▪️ 철학자 이해하기

{% hint style="warning" %}
저희는 보너스 과제를 수행하지 않았으므로, 이에 대해 설명하지 않을 것입니다. 카뎃 여러분이 직접 조사해 보시기를 권장합니다 :)
{% endhint %}

### Process란 무엇입니까?

> Process는 컴퓨터에서 실행 중인 프로그램입니다. 이는 맞춤법 검사기나 시스템 이벤트 핸들러와 같은 작은 백그라운드 작업부터 Internet Explorer 또는 Microsoft Word와 같은 완전한 애플리케이션에 이르기까지 모든 것이 될 수 있습니다. 모든 Process는 하나 이상의 [Thread](https://techterms.com/definition/thread)로 구성되어 있습니다.

Process가 무엇인지에 대한 더 정확한 예시를 원하시면, [\[여기\]](https://42-cursus.gitbook.io/guide/rank-02/minitalk/understand-minitalk#processes-and-signals)를 클릭해 주십시오. Laura가 훌륭한 예시를 만들어 보여주었습니다.

> 대부분의 [운영 체제](https://techterms.com/definition/operating_system)에는 많은 백그라운드 작업이 실행되고 있으므로, 카뎃 여러분의 컴퓨터는 실제 프로그램보다 훨씬 더 많은 Process가 실행되고 있을 가능성이 높습니다. 예를 들어, 실행 중인 프로그램이 세 개만 있을 수 있지만, 활성화된 Process는 스무 개가 있을 수 있습니다. Windows에서는 작업 관리자(Ctrl-Alt-Delete를 누르고 작업 관리자 클릭)를 열어 활성 Process를 볼 수 있습니다. Mac에서는 활동 모니터(응용 프로그램→유틸리티 폴더 내)를 열어 활성 Process를 확인할 수 있습니다.

출처 : [techterms.com](https://techterms.com/definition/process)

### Thread란 무엇입니까?

> 티셔츠와 컴퓨터 프로그램의 공통점은 무엇일까요? 둘 다 많은 Thread로 구성되어 있다는 것입니다! 티셔츠의 Thread가 셔츠를 함께 잡아주는 것처럼, 컴퓨터 프로그램의 Thread는 프로그램이 순차적인 동작이나 여러 동작을 동시에 실행할 수 있도록 허용합니다. 프로그램의 각 Thread는 프로그램이 요청할 때 실행되는 Process를 식별합니다 (룸메이트에게 설거지를 요청할 때와는 다릅니다).
>
> Thread에는 일반적으로 특정 우선순위가 부여되며, 이는 일부 Thread가 다른 Thread보다 선행됨을 의미합니다. CPU는 한 Thread 처리를 마치면, 대기 중인 다음 Thread를 실행할 수 있습니다. 하지만 Thread가 크리스마스 전 토요일 Target 계산대에서 줄을 서서 기다려야 하는 것과 같지는 않습니다. Thread는 실행되기까지 몇 밀리초 이상 대기하는 경우가 거의 없습니다. "Multi-threading"을 구현하는 컴퓨터 프로그램은 여러 Thread를 동시에 실행할 수 있습니다. 대부분의 최신 운영 체제는 시스템 수준에서 Multi-threading을 지원합니다. 이는 한 프로그램이 모든 CPU 리소스를 차지하려고 할 때에도, 여전히 다른 프로그램으로 전환하여 CPU를 독점하는 프로그램이 프로세서를 조금씩 공유하도록 강제할 수 있음을 의미합니다.

출처: [techterms.com](https://techterms.com/definition/thread)

### Thread와 Process의 차이점

Process와 Thread는 모두 독립적인 실행 시퀀스입니다. 일반적인 차이점은 (동일한 Process에 속한) Thread는 공유 메모리 공간에서 실행되는 반면, Process는 별도의 메모리 공간에서 실행된다는 것입니다. (출처: [stackoverflow](https://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread))

다음은 제가 조사를 통해 주목한 몇 가지 사항입니다.

#### Process

*   여러 Thread를 가질 수 있습니다.
*   `fork()`를 사용할 때 모든 것을 복제합니다.
    *   이는 변수가 단순히 복사된다는 의미이며, 한 Process에서 변수를 수정하더라도 다른 Process에서는 수정되지 않습니다.

#### Thread

*   메모리 공간을 공유합니다.
    *   어딘가에 변수를 선언하고 Thread 내부에서 수정하면, 다른 모든 Thread에서도 해당 변수가 변경됩니다.
*   "Memory Race"라고 불리는 상황에서 실행될 수 있습니다.
    *   메모리가 Thread 간에 공유되기 때문에, 여러 Thread가 동시에 동일한 변수에 접근하려고 시도하면 이를 "Memory Race"라고 합니다. 이때 '가장 빠른' Thread가 변수 값을 먼저 수정하고, 그다음 Thread가 수정하게 됩니다. 하지만 첫 번째 수정의 값은 어딘가에 유용했을 수 있습니다.
    *   "Memory Leak"에 대비하는 것처럼, 우리는 "Memory Race"로부터 프로그램을 보호해야 합니다.

{% embed url="https://www.youtube.com/watch?v=FY9livorrJI" %}

### Mutex란 무엇입니까? (pthread\_mutex)

Mutex는 기본적으로 잠금(Lock)입니다. 변수를 잠가서 한 번에 하나의 Thread만 해당 변수에 접근할 수 있도록 할 수 있습니다. 첫 번째 Thread가 변수에 대한 작업을 마치면, 다른 Thread가 해당 변수에 접근할 수 있도록 Mutex의 잠금을 해제합니다.

{% embed url="https://www.youtube.com/watch?feature=youtu.be&v=oq29KUy29iQ" %}