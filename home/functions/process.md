
## Process

```c
#include <unistd.h>
```

#### fork

```c
pid_t fork(void);
```

> 현재 프로세스에 대해 자식 프로세스를 생성하는 함수

* 자식 프로세스는 부모 프로세스의 메모리를 그대로 복사
* 그 후에는 각자의 메모리를 사용하여 실행
* 프로세스 생성이 성공하면 자식 프로세스에는 pid\_t 값으로 0을 반환하고, 부모 프로세스에는 자식 프로세스의 ID를 반환
* 실패하면 부모 프로세스에 -1을 반환

#### pipe

```c
int pipe(int fildes[2]);
```

> 프로세스 사이의 통신을 위한 pipe와 file descriptor를 생성하는 함수

* 커널에 단방향 파이프 생성
* fildes\[0]: 읽기 전용
* fildes\[1]: 쓰기 전용

#### execve

```c
int execve(const char *path, char *const argv[], char *const envp[]);
```

> 새로운 프로세스로 전환하는 함수

* `*path`: 실행할 프로세스의 경로
  * 실행가능한 파일 혹은 interpreter 파일
* `argv[]`: 새 프로세스에서 사용할 인자 배열
  * 마지막은 NULL (int argc가 따로 없기 때문)
* `envp[]`: 환경변수 문자열 배열
  * 설정된 환경변수 그대로 이용하려면 `environ` 사용

#### exit

```c
void exit(int status);
```

> 프로세스 종료

1. 모든 열린 출력 스트림에 출력을 끝냄
2. 모든 열린 출력 스트림을 닫음

* 할당한 메모리 반환