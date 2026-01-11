# ▪️ 프로젝트 구축하기

여기서는 프로젝트의 여러 부분에 해당하는 체크리스트를 만들어 드릴 것입니다. 이 체크리스트를 따라 프로젝트를 구축할 수 있습니다. 저의 "Minitalk" 코드는 [Github](https://github.com/thebrisly/42-Cursus-Piscine/tree/master/minitalk)에서 확인하실 수 있습니다. 궁금한 점이 있다면 주저하지 마시고 저(Laura)에게 [연락](../../team.md)해 주십시오. 모든 질문에 기쁘게 답변해 드리겠습니다.

[맨 처음](understand-minitalk.md)에 보여드렸던 차트로 돌아가 보겠습니다.

<figure><img src="../../.gitbook/assets/minitalk_scheme.png" alt=""><figcaption><p>minitalk 설명도</p></figcaption></figure>

### 개요 (Overview)

클라이언트가 서버로 메시지를 전송하려고 합니다. 예시의 메시지, 즉 "Hello"는 서버로 직접 전송될 수 없습니다. 클라이언트는 일련의 Signal을 전송하여 메시지를 암호화해야 하며 (저의 예시에서는 비트, 즉 0과 1을 사용했지만, 다른 방법을 찾으시면 자유롭게 사용하셔도 됩니다), 서버는 이를 화면에 표시하기 전에 복호화/해석해야 합니다.

**왜 메시지를 직접 보낼 수 없습니까?** 앞서 말씀드렸듯이, Signal은 프로세스(process)로 전송되는 알림의 한 종류입니다. 이 프로젝트에서는 `SIGUSR1`과 `SIGUSR2`라는 두 가지 유형의 Signal만 사용할 수 있습니다. 이는 클라이언트가 서버 프로세스에 `SIGUSR1` Signal을 전송할 때, 서버가 특정 작업을 수행한다는 것을 의미합니다. 또한, `SIGUSR2` Signal을 수신할 때는 또 다른 작업을 수행하게 됩니다.

<details>

<summary>메시지를 직접 보낼 수 없는 이유를 이해하기 위한 짧은 예시</summary>

클라이언트가 서버에 "SIGUSR1" Signal을 보낼 때마다 서버는 "Hello World"를 표시해야 합니다. 클라이언트가 서버에 "SIGUSR2" Signal을 보낼 때마다 서버는 난수를 표시해야 합니다. 클라이언트가 "SIGUSR1" 또는 "SIGUSR2"를 서버에 원하는 횟수만큼 보낼 수 있으며, 서버는 항상 동일한 내용, 즉 "Hello World" 또는 숫자(수신된 Signal에 따라)를 표시할 것입니다.

아래 코드는 작동하지 않지만, 제가 말씀드리는 내용을 더 잘 이해하실 수 있도록 돕기 위함입니다.

<pre class="language-c" data-overflow="wrap" data-line-numbers><code class="lang-c">/*client*/

int main()
{
<strong>    kill(server_pid, SIGUSR1) //서버에 SIGUSR1 Signal을 전송합니다
</strong>    kill(server_pid, SIGUSR2) //서버에 SIGUSR2 Signal을 전송합니다
}


/* ---------------------------------------------------- */


/*server/

void	signal_handler(int signum)
{
    if (signum == SIGUSR1) //만약 Signal이 SIGUSR1과 같다면 "Hello World"를 출력합니다
	printf("Hello World);
    else if (signum == SIGUSR2)
         printf (" The random number is: %d", rand()); 
}

int main()
{
    sigaction(SIGUSR1, &#x26;signal_received, NULL); //SIGUSR1 Signal을 받을 때마다 특정 함수(Signal 핸들러)를 적용합니다
    sigaction(SIGUSR2, &#x26;signal_received, NULL); //SIGUSR2 Signal을 받을 때마다 특정 함수(Signal 핸들러)를 적용합니다
}
</code></pre>

여기서 SIGUSR1 Signal은 서버에 "Hello World" 메시지를 보내는 용도로만 사용됩니다. 다른 메시지를 보내려면 불가능합니다. 아니면 서버가 SIGUSR1을 수신할 때마다 출력 값을 다시 변경해야 할 것입니다. 이것은 다소 번거롭습니다. 서버의 출력을 직접 변경하는 것은 별로 의미가 없습니다 (결국, 메시지를 실제로 보내는 것이 아니라 나중에 미리 정의된 내용을 보내는 Signal만 보내는 것이므로, 이는 속임수가 될 수 있습니다).

**목표는 SIGUSR1 및 SIGUSR2 Signal을 사용하여 어떤 메시지든 관계없이 전송할 수 있도록 하는 것입니다!**

따라서 서버가 콘솔에서 입력된 모든 메시지를 표시할 수 있도록 메시지를 복호화하는 방법을 찾아야 합니다.

</details>

다음은 프로젝트를 성공적으로 수행하는 데 도움이 되는 두 가지 체크리스트입니다.

### 클라이언트 체크리스트 (Client Checklist)

"client.c" 파일에서 다음을 수행해야 합니다.

* [ ] 클라이언트가 두 가지 파라미터/인수를 받는 프로그램(main)을 작성합니다.
  * [ ] 메시지를 보낼 서버의 PID
  * [ ] 메시지
* [ ] 메시지를 암호화합니다 (저는 비트를 통해 암호화했습니다).
* [ ] (PID를 통해) 서버로 메시지를 전송합니다.
* [ ] 서버가 메시지 수신을 완료했음을 알 수 있도록 종료 조건을 생성합니다.

### 서버 체크리스트 (Server Checklist)

"server.c" 파일에서 다음을 수행해야 합니다.

* [ ] 자신의 PID를 표시합니다.
* [ ] 서버가 언제든지 Signal을 수신할 수 있도록 무한 루프를 생성합니다.
* [ ] Signal을 수신합니다.
* [ ] Signal을 복호화합니다.
  * [ ] 수신된 각 Signal (SIGUSR1 & SIGUSR2)에 대해 특정 조치를 취해야 합니다.

### 기본 체크리스트 (Basic Checklist)

물론 다음 세 가지 사항도 수행해야 합니다.

* [ ] Makefile (server 및 client 실행 파일을 생성합니다).
* [ ] 오류를 관리하고 모든 leaks를 제거합니다.
* [ ] 성스러운 norminette를 처리합니다.

이것이 전부입니다. 일단 개념을 확실히 이해하면, 이 프로젝트가 매우 간단하다는 것을 알게 될 것입니다! 이제 여러분 스스로 해결책을 찾고 이 문제를 해결해 보십시오... 행운을 빕니다!