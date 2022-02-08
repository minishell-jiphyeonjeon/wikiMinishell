# Signal

```c
#include <signal.h>
```

<details>

<summary>신호(signal)란?</summary>

* 해당 도메인 외부에서 프로세스를 조작하거나 프로세스가 자기 자신이나 자식 프로세스를 조작할 수 있도록 하는 것
* 일반적으로 프로세스를 종료시키는 신호와 그 외의 것 크게 두 가지로 분류
  * 종료신호는 복구할 수 없는 오류 혹은 사용자가 입력한 'interrupt' 문자를 입력한 결과
  * 프로세스가 백그라운드에 있는 동안 터미널에 접근하기 위해 중단될 때
* 프로세스가 중단된 후 다시 시작될 때, 자식 프로세스의 상태가 변경될 때, 터미널에서 입력이 준비될 때 선택적으로 생성됨
* 대부분의 신호는 프로세스의 수신을 종료시키고, 어떤 동작도 없는 경우에 일부 신호는 프로세스의 수신을 멈추거나 프로세스가 다른 요청을 하지 않은 경우 폐기됨
* SIGKILL과 SIGSTOP 신호를 제외하고 signal()함수를 통해 신호를 잡거나, 신호를 무시하거나, interrupt를 생성할 수 있음
*   \<signal.h>에 정의된 신호들 목록 (cluster Mac 기준)

    | **No** | Name      | Default Action    | Description                                     |
    | :----: | --------- | ----------------- | ----------------------------------------------- |
    |    1   | SIGHUP    | terminate process | terminal line hangup                            |
    |    2   | SIGINT    | terminate process | interrupt program                               |
    |    3   | SIGQUIT   | create core image | quit program                                    |
    |    4   | SIGILL    | create core image | illegal instruction                             |
    |    5   | SIGTRAP   | create core image | trace trap                                      |
    |    6   | SIGABRT   | create core image | abort program (formerly SIGIOT)                 |
    |    7   | SIGEMT    | create core image | emulate instruction executed                    |
    |    8   | SIGFPE    | create core image | floating-point exception                        |
    |    9   | SIGKILL   | terminate process | kill program                                    |
    |   10   | SIGBUS    | create core image | bus error                                       |
    |   11   | SIGSEGV   | create core image | segmentation violation                          |
    |   12   | SIGSYS    | create core image | non-existent system call invoked                |
    |   13   | SIGPIPE   | terminate process | write on a pipe with no reader                  |
    |   14   | SIGALRM   | terminate process | real-time timer expired                         |
    |   15   | SIGTERM   | terminate process | software termination signal                     |
    |   16   | SIGURG    | discard signal    | urgent condition present on socket              |
    |   17   | SIGSTOP   | stop process      | stop (cannot be caught or ignored)              |
    |   18   | SIGTSTP   | stop process      | stop signal generated from keyboard             |
    |   19   | SIGCONT   | discard signal    | continue after stop                             |
    |   20   | SIGCHLD   | discard signal    | child status has changed                        |
    |   21   | SIGTTIN   | stop process      | background read attempted from control terminal |
    |   22   | SIGTTOU   | stop process      | background write attempted to control terminal  |
    |   23   | SIGIO     | discard signal    | I/O is possible on a descriptor (see fcntl(2))  |
    |   24   | SIGXCPU   | terminate process | cpu time limit exceeded (see setrlimit(2))      |
    |   25   | SIGXFSZ   | terminate process | file size limit exceeded (see setrlimit(2))     |
    |   26   | SIGVTALRM | terminate process | virtual time alarm (see setitimer(2))           |
    |   27   | SIGPROF   | terminate process | profiling timer alarm (see setitimer(2))        |
    |   28   | SIGWINCH  | discard signal    | Window size change                              |
    |   29   | SIGINFO   | discard signal    | status request from keyboard                    |
    |   30   | SIGUSR1   | terminate process | User defined signal 1                           |
    |   31   | SIGUSR2   | terminate process | User defined signal 2                           |

</details>

### signal

```c
sig_t signal(int sig, sig_t func);
```

> 신호를 다루는 함수. sigaction()의 단순화된 함수

* `int sig`: 수신된 신호를 지정하는 변수
* `sin_t func`: 신호를 수신할 때의 동작
  * SIG\_DFL : 표에 언급된 대로 기본 동작을 재설정
  * SIG\_IGN : 신호를 무시
    * 신호의 후속 인스턴스가 무시되고 보류중인 인스턴스도 삭제됨
    * 사용하지 않으면 추가 신호가 차단되고 `func` 호출
* handler signal은 해당 함수가 리턴될 때 unblock되고 멈췄던 위치에서 프로세스가 계속됨
  * 다른 신호들과 다르게 `handler func()`는 신호 전달 후에도 남아있음
* 일부 system call의 경우, call이 실행되는 동안 신호가 잡히고 call이 일찍 종료되면 해당 호출은 자동적으로 재시작
* `signal()`에 설치된 handler는 SA\_RESTART 플래그가 설정되는데 이는 신호가 수신되었을 때 재시작 가능한 systemp call이 반환되지 않는다는 것을 의미
  * 영향을 받는 system call: `read(), write(), sendto(), recvfrom(), sendmsg(), recvmsg(), ioctl(), wait()`
  * 이미 커밋된 call의 경에는 재시작하지 않고 부분성공을 반환
  * `siginterrupt()`를 이용해 변경가능
* signal handler가 설치된 프로세스가 fork할 때 자식 프로세스에게도 신호가 상속
  * 수신된 모든 신호는 `execve()`함수를 호출하는 것으로 기본 동작으로 리셋 가능
  * 무시된 신호는 무시된 상태로 유지
* 프로세스가 SIGCHLD에 대한 동작으로 SIG\_IGN을 명시하지 않은 경우, 호출한 프로세스의 자식 프로세스가 종료될 때 좀비 프로세스가 만들어지지 않음
  * 시스템이 자식 프로세스의 종료 상태를 취소
  * 이후 호출 프로세스가 wait()이나 비슷한 함수를 호출한 경우 자식 프로세스가 종료될 때까지 차단하고 errono ECHILD와 함께 -1을 반환
* signal handler에서 안전하게 사용할 수 있는 함수들의 목록은 `sigaction()` 설명을 참고

### kill

```c
int kill(pid_t pid, int sig);
```

> 프로세스에 신호를 전달하는 함수

*   `pid`: 신호를 전달받을 프로세스

    | **pid** |                                            |
    | :-----: | ------------------------------------------ |
    |   `>0`  | 지정한 프로세스에만 전달                              |
    |   `0`   | 함수를 호출하는 프로세스와 같은 그룹에 있는 모든 프로세스에 전달       |
    |   `-1`  | 함수를 호출하는 프로세스가 전송할 수 있는 권한을 가진 모든 프로세스에 전달 |
    |  `-1>`  | 절대값 프로세스 그룹에 속하는 모든 프로세스에 전달               |
* `sig`: 전달할 신호 (참고: [신호란?](../../functions/undefined.md#Signal))
