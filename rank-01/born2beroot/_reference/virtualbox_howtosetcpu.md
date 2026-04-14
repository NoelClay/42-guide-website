일단 나름의 추상화가 존재하는 개념인줄 알았음. 왜냐하면 실제 물리적 cpu보다 더 많은 32개의 스레드까지도 할당할수 있게끔 gui가 표시되었기 때문. 실제로 열어보면 앎. 그냥 32개까지 바로 다이렉트로 매겨버릴수 있음.

하지만 실제 물리적 cpu의 스레드와 그냥 1대1 매칭이었음. 만약 이것을 오버스펙으로 할당하면 위험하다고 경고까지함.
[경고: You should not configure virtual machines to use more CPU cores than are available physically. This includes real cores, with no hyperthreads.](https://www.virtualbox.org/manual/ch03.html#settings-processor)
[1대1매칭 지원: Processor(s): Sets the number of virtual CPU cores the guest OSes can see. Oracle VM VirtualBox supports symmetrical multiprocessing (SMP) and can present up to 32 virtual CPU cores to each virtual machine.](https://www.virtualbox.org/manual/ch03.html#settings-processor)

1대1 매칭을 유추할수 있는건 smp라는 문구를 보고서 알았는데. smp는 결국 그냥 오늘날의 프로세스들이 큐에 들어가있다가 시분할로 여러 프로세서들에게 동등한 레벨로 스케쥴링되어서 멀티작업이 일어나는개념 그 자체를 의미하는건데. VM 이다보니, 전용 하드웨어 공간이 있는거 마냥 processor도 전용 프로세서를 떼어주고 분리해서 운용될수도 있었으나, 최적화를 통해서 그냥 일반적인 같은 os의 여러 프로세서들 작업하는거마냥 잘 돌아가도록 지원한다는걸 의미한다. 그래서 결국 smp를 지원하니, 우리 vm이 얼만큼의 cpu 프로세서들과 작업하게 할지를 그냥 정해주는것이다. 따라서 초과할당만 아니면 무조건 좋은거 아닌가? 라고 생각했다.
[smp란? SMP systems are tightly coupled multiprocessor systems with a pool](https://en.wikipedia.org/wiki/Symmetric_multiprocessing)

결론적으로는 완전 반대였는데 요약을 하자면 vcpu는 1대1로 물리적cpu 이하 pcpu라고 말하자면 pcpu와 1대1로 매칭시키는게 맞다. 그게 최적화는 맞다. 하지만, 하이퍼바이저는 항상 지금 속여온 OS에게 항상 진실만을 전달해야된다. 이 말이 오히려 쉽다. 내가 이해하기로는 내가 만들어낸 말이여서그런지는 몰라도 제일 쉽다. vm 당연히 os를 속이는것 아닌가. 실제 물리적으로는 램이 64기가여도 너는 8기가야 라고 속이는것 아닌가. 마찬가지로 속여야한다. 가짜로 속이는게 아니라 진짜로 속여야된다. 메모리는 공간이 크고 유휴(비어있는) 메모리가 매우 자주 나오며 실제로 ssd로 가상메모리를 사용할수도 있다. 하지만 CPU는 아니다. 절대로 가상 CPU를 대체시켜줄 장치는 없으며, 엄밀하게 CPU 스케쥴링에의해서 작업이 일어나야된다. 그런 스케쥴링은 os가 어떤 프로세스를 자기가 가진 CPU한에서 어떻게 사용할수 있을지에 따라 스케쥴링이 일어나는것이다. 방금까지 위에 내용까지 전부 이해했다면, VM OS는 할당받은 CPU가 물리적으로 실제로 있어야지만, 자기의 스케쥴링을 정상적으로 돌릴 수 있다는 것이다. 여기서 이제 동기화 내용이 어려운데, 만약 VM에게 너느 2코어 4스레드 CPU를 가지고 있어. 스케쥴링 잘해서 서버를 돌려봐. 라고 명령을 내릴것이다. 실제로 그렇다. 그런데 만약 VM에게 줄 온전한 2코어 4스레드 CPU가 없이 1.5코어 3스레드 밖에 여유공간이 안나고 어쩌다가 10초에 한번정도 2코어 4스레드가 나온다면. VM은 개멍청할수도 있지만, 아니 오히려 에러없이 완벽하게 알잘딱하게 2코어 4스레드가 발생할때만 원래 스케쥴링된 작업을 수행하고 적절하게 빠지고 다시 9초동안 멍청해진 상태를 만들어버린다. 이것은 완벽한 병목이다. 성능을 높이려고 코어수를 늘렸는데 실제로는 주지도 못할 코어를 주고서 애를 멍청이로 만들어버리는것이다. 그만큼 CPU와 OS간의 스케쥴링은 매우 정교하기 때문에 그냥 적당 1코어남으면 알아서 1코어만 알잘딱하게 쓰고 그러면 되지 뭘 2코어가 될때까지 쳐 기다리고 있어 라는 식의 생각을 할수도 있지만, 그것은 이제 더이상 현존하는 리눅스, 윈도우, mac OS가 아닌, 마치 실제 컴퓨터에서도 CPU를 줬다 뺐다 물리적으로 망치로 부러뜨렸다가 다시 납땜해서 고쳐도 알잘딱 돌아가는 OS를 구현했다는 거나 마찬가지이므로, 오히려 비현실적이라고 생각할수 있다.
[어렵다 vcpu](https://lonesysadmin.net/2008/04/22/why-my-two-vcpu-vm-is-slow/)
[매우어렵다 vcpu](https://www.linkedin.com/pulse/more-less-vcpus-can-make-your-vm-slower-d-travis-brandel/)

그럼 지금 내가 하려는 세팅에서 가장 많은 병목이 일어나는 시나리오는 미친 트래픽 몰림이다. 이걸 처리하는걸 php worker를 5개를 배치할거다. 근데 vcpu 5개는 너무 과한거 아닌가? -> 항상 그런건 아니지만 최악의 미친 트래픽을 가정하면 vcpu의 개수와 worker 개수를 맞추는게 맞다 -> 따라서 5개이므로 os외 다른 작업도 돌아가게 하기위해 6개이므로 3개만 배치해볼 예정
[vcpu와 phpworker: PHP Is Single-Threaded | What happens if I have more PHP workers than CPU cores?](https://instawp.com/php-workers-vs-cpu-cores/)
