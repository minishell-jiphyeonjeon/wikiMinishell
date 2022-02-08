## Wait

```c
#include <sys/wait.h>
```

#### wait

```c
pid_t wait(int *stat_loc);
```

> 자식프로세스가 종료되거나, 신호가 들어올 때까지 기다리는 함수

* `*stat_loc`: 종료 상태 정보
  *   매크로를 통해 필요한 비트를 확인

      |      **macro**      |                                     |
      | :-----------------: | ----------------------------------- |
      |  WIFEXITED(status)  | 자식프로세스의 정상적인 종료                     |
      | WIFSIGNALED(status) | 시그널에 의한 종료                          |
      |  WIFSTOPPED(status) | 정지 상태 확인 (종료되지 않아 재시작 가능한 경우)       |
      | WEXITSTATUS(status) | 정상적인 종료일 때, exit()의 인자에서 하위 8bit 반환 |
      |   WTERMSIG(status)  | 시그널에 의한 종료일 때, 종료시킨 시그널 반환          |
      |  WCOREDUMP(status)  | 시그널에 의한 종료일 때, 코어 파일의 생성 여부         |
      |   WSTOPSIG(status)  | 정지 상태일 때, 정지시킨 시그널 반환               |

#### waitpid

```c
pid_t waitpid(pid_t pid, int *stat_loc, int options);
```

> pid값에 해당하는 자식프로세스가 종료되거나, 신호가 들어올 때까지 기다리는 함수

*   `pid`: 기다릴 프로세스

    | **pid** |                                       |
    | :-----: | ------------------------------------- |
    |   `>0`  | 지정한 프로세스만 기다림                         |
    |   `0`   | 함수를 호출하는 프로세스와 같은 그룹에 있는 모든 프로세스를 기다림 |
    |   `-1`  | 임의의 모든 프로세스를 기다림 (== wait())          |
    |  `-1>`  | 절대값 프로세스 그룹에 속하는 모든 프로세스를 기다림         |
* `options`
  * WNOHANG: 자식프로세스가 중단되지 않았을 때 기다리지않고 바로 0 반환
  * WUNTRACED: 자식프로세스가 정지했을 때 상태를 반환
* 정상적인 경우 종료된 자식 pid가 반환되고, 오류 시 -1 반환

#### wait3 & wait4

```c
pid_t wait3(int *stat_loc, int options, struct rusage *rusage);
pid_t wait4(pid_t pid, int *stat_loc, int options, struct rusage *rusage);
```

> 자식 프로세스가 종료되는 것을 기다리며, 종료된 프로세스의 상태와 자원 사용량을 알려주는 함수

* wait3는 wait4()의 pid가 -1일 때와 동일
* `rusage`: 자식프로세스의 리소스 사용 정보
  * wait4()에서 rusage가 0이면 waitpid()와 동일
