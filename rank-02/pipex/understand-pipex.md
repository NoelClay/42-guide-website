# ▪️ pipex 이해하기

### 목표

<details>

<summary>프로젝트별 지침</summary>

* 여러분의 프로그램은 다음과 같이 실행될 것입니다. `./pipex file1 cmd1 cmd2 file2`
* `file1`과 `file2`는 파일 이름입니다.
* `cmd1`과 `cmd2`는 인자(Argument)를 가진 Shell Command입니다.
* 여러분의 프로그램은 다음 Shell Command와 완전히 동일하게 동작해야 합니다.
  * `$> < file1 cmd1 | cmd2 > file2`
* 여러분의 프로그램은 어떠한 Memory Leak도 발생시키지 않아야 합니다!

</details>

pipex의 목표는 Shell에서의 Pipe를 시뮬레이션하는 프로그램을 개발하는 것입니다.

이 프로그램에서는 이전에 사용해 보지 않은 많은 새로운 함수들을 사용하게 될 것이므로, 다음 섹션에서 이 모든 함수들을 다뤄보도록 하겠습니다.

과제에서 제공된 예시를 보면, 여러분의 프로그램은 다음 Shell Command를 정확히 재현해야 합니다.

```shell
< file1 cmd1 | cmd2 > file2
```

더 나아가기 전에, 이 Command가 정확히 무엇을 하는지 명확하게 이해해야 합니다.

Command 예시를 하나 들어 왼쪽에서 오른쪽으로 살펴보겠습니다.

```shell
< infile grep a1 | wc -w > outfile
```

### < 기호

`<` 기호는 "Input Redirection(입력 재지정)" 기호입니다.

이 경우, `<` 기호는 `infile`의 내용을 **Standard Input**으로 리디렉션(redirection)합니다. 따라서 `grep`이 **Standard Input**에서 읽을 때 `infile`의 내용을 가져오게 됩니다.

동일하게 작동하지만 이해하기 쉬운 구문을 사용해 보겠습니다.

```shell
grep a1 < infile | wc -w > outfile
```

이렇게 하면 `infile`의 내용이 `grep` Command에 의해 사용된다는 것을 더 잘 알 수 있습니다.

<figure><img src="../../.gitbook/assets/Screenshot 2023-01-02 at 10.13.18.png" alt=""><figcaption></figcaption></figure>

### | 기호

`|` (**Pipe**) 기호는 왼쪽 Command의 **Output**을 오른쪽 Command의 **Input**으로 리디렉션합니다.

이 경우, `|` 기호는 `grep` Command의 **Output**을 `wc` Command의 **Input**으로 리디렉션합니다.

<figure><img src="../../.gitbook/assets/Screenshot 2023-01-02 at 10.07.28.png" alt=""><figcaption><p><code>|</code> 기호가 없을 때</p></figcaption></figure>

**Pipe** 기호가 없으면 `grep` Command의 Output은 **Standard Output**에 직접 작성됩니다. 이제 **Pipe** 기호를 추가해 보겠습니다.

<figure><img src="../../.gitbook/assets/Screenshot 2023-01-02 at 10.08.50.png" alt=""><figcaption><p>Pipe 기호를 사용하여 <code>grep</code> 결과를 <code>wc</code>로 리디렉션하는 모습</p></figcaption></figure>

보시다시피, 이제 출력은 `wc` Command의 결과이지만, 이는 `grep` Command의 결과에 대한 `wc` Command의 결과입니다. 따라서 20을 보게 되는 것입니다. `grep`만 실행했을 때 이전에 가지고 있던 단어의 수를 세어보면 20개입니다.

### > 기호

`>` 기호는 "Output Redirection(출력 재지정)" 기호입니다.

이 경우, `>` 기호는 `wc` Command의 **Output**을 `outfile`에 작성합니다.

<figure><img src="../../.gitbook/assets/pipex_gt_without_gt_example.png" alt=""><figcaption><p>이것은 <code>&gt;</code> 기호가 없는 Command의 결과입니다.</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/pipex_gt_example.png" alt=""><figcaption><p><code>&gt;</code> 기호가 있는 동일한 Command입니다. Standard Output에서는 아무것도 볼 수 없습니다.</p></figcaption></figure>

실제로 두 Command는 동일한 결과를 제공합니다. 둘 사이에서 `infile`은 변경되지 않았지만, 두 번째 Command의 Output은 `outfile` 파일에 작성되었습니다. 한번 살펴보겠습니다.

<figure><img src="../../.gitbook/assets/pipex_gt_outfile.png" alt=""><figcaption></figcaption></figure>

### >> 기호

`>>` 기호는 `>` 기호와 거의 동일하게 작동합니다. `>` 기호는 오른쪽 파일의 내용을 왼쪽 Command의 Output으로 대체하지만, `>>` 기호는 왼쪽 Command의 Output을 파일 끝에 추가(append)합니다.

<figure><img src="../../.gitbook/assets/pipex_gt_gt_example.png" alt=""><figcaption><p><code>&gt;&gt;</code> 예시</p></figcaption></figure>

이 두 Command 다음에 `outfile`이 어떻게 보이는지 살펴보겠습니다.

<figure><img src="../../.gitbook/assets/pipex_gt_gt_outfile.png" alt=""><figcaption></figcaption></figure>

### << 기호

Bonus 과제를 위해 `<<` 기호도 있습니다.

`<<` 기호는 Input "Redirection" 기호입니다. 이 기호는 Shell이 **Standard Input**에서 특정 `LIMITER`만 단독으로 만날 때까지 Standard Input으로부터 읽도록 만듭니다. 과제의 예시를 살펴보겠습니다.

```shell
cmd << LIMITER | cmd1 >> file
```

<figure><img src="../../.gitbook/assets/pipex_gt_gt_example.png" alt=""><figcaption><p><code>&lt;&lt;</code> 예시</p></figcaption></figure>

보시다시피, 첫 번째 `cat` Command는 제가 Standard Input에 `LIM`을 **단독으로** 작성할 때까지 기다린 후에야 다음 단계로 진행했습니다. 여러분의 `pipex`도 동일하게 동작해야 합니다.

<figure><img src="../../.gitbook/assets/pipex_here_doc.png" alt=""><figcaption></figcaption></figure>
