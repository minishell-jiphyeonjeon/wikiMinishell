# 허용-함수

## 전체 허용 함수 목록

```
readline, rl_on_new_line, rl_replace_line,
rl_redisplay, add_history, printf, malloc, free,
write, open, read, close, fork, wait, waitpid,
wait3, wait4, signal, kill, exit, getcwd, chdir,
stat, lstat, fstat, unlink, execve, dup, dup2,
pipe, opendir, readdir, closedir, strerror, errno,
isatty, ttyname, ttyslot, ioctl, getenv, tcsetattr,
tcgetattr, tgetent, tgetflag, tgetnum, tgetstr,
tgoto, tputs
```

## Readline

* [readline](https://junselee.tistory.com/3)
* [mit](https://web.mit.edu/gnu/doc/html/rlman\_2.html)
* [man](https://man7.org/linux/man-pages/man3/readline.3.html)
* [스터디](https://github.com/Minishell-study/allowed\_function\_study/wiki/history-%EA%B4%80%EB%A6%AC-%ED%95%A8%EC%88%98-\(readline%2C-rl\_on\_new\_line%2C-rl\_replace\_line%2C-rl\_redisplay%2C-add\_history\))

#### readline

```c
# include <readline/readline.h>
# include <readline/history.h>
# include <stdio.h>
# include <stdlib.h>
# include <stdbool.h>

int main(void)
{
    char *str;
    while(true)
    {
        str = readline(" > ");
        if (str)
            printf("%s\n", str);
        else
            break ;
        add_history(str);
        free(str);
    }
    return(0);
}
```

> 프롬프트에서 읽어온 줄을 문자열로 **동적 할당**해 반환

#### rl\_on\_new\_line

```c
int rl_on_new_line(void);
```

> 줄바꿈 했다는 것을 프롬프트에 알림

#### rl\_replace\_line

```c
void rl_replace_line (const char *text, int clear_undo);
```

> `rl_line_buffer`를 `text`로 변환

* **brew 필요**

#### rl\_redisplay

```c
int rl_redisplay(void);
```

> 프롬프트를 `rl_line_buffer`로 새로고침

* **brew 필요**

#### add\_history

```c
int	add_history(const char *string);
```

> 프롬프트 기록에 `string` 추가

* 위, 아래 화살표 키로 확인 가능

***

## File Descriptor

> fd \~= `바로가기`

* [이 부분을 복붙한 곳](https://www.maribel.dev/posts/linux/fd/)
* [inode](https://geek-university.com/linux/inode/)
* [inode #2](https://rocksea.tistory.com/20)
* [파일 테이블/파일 식별자 테이블](https://ehpub.co.kr/%EB%A6%AC%EB%88%85%EC%8A%A4-%EC%8B%9C%EC%8A%A4%ED%85%9C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-3-5-%ED%8C%8C%EC%9D%BC-%ED%85%8C%EC%9D%B4%EB%B8%94%EA%B3%BC-%ED%8C%8C%EC%9D%BC-%EB%94%94/)
* [파일 디스크립터](https://www.computerhope.com/jargon/f/file-descriptor.htm)
* [디렉토리도 파일인데 cat이 안 되는 이유](https://www.reddit.com/r/linuxquestions/comments/2tk2p1/if\_everything\_is\_a\_file\_why\_cant\_i\_do\_cat/)
* [ext4 파일시스템 구조](https://rrhh234cm.tistory.com/184)

#### open

```c
int open(const char *pathname, int flags)
int open(const char *pathname, int flags, mode_t mode)
```

파일을 열고(아니면 만들고) 그 파일을 가리키는 `바로가기` 반환

#### close

```c
int close(int fd);
```

주어진 `바로가기`를 삭제해서 같은 이름의 다른 `바로가기`를 생성할 수 있게 한다

가령 `인터넷->인터넷 익스플로러` 이라는 바로가기가 있을 때 close(인터넷) 을 하면 `인터넷->크롬`이라는 새로운 `바로가기`를 만들 수 있게 된다

#### dup

```c
int dup(int oldfd)

oldfd: 복사할 바로가기
반환값: 새로 만들어진 바로가기
```

`바로가기`를 복사해서 반환한다. 가령 `마인크래프트.exe` 게임의 바로가기 `마인크래프트 - 바로가기`가 있다고 해보자. 이 바로가기를 복사하면 어떻게 될까? `마인크래프트.exe` 에는 변화가 없고 `마인크래프트 - 바로가기(2)`가 생길 것이다.

마찬가지로 `dup()`을 실행하면 oldfd `바로가기`를 복사해 새로운 `바로가기`를 반환한다. 이때 새 바로가기는 남는 번호중 가장 낮은 번호가 나온다. 어떤 이야기냐면 바로가기를 계속 만들면

* `마인크래프트 - 바로가기(3)`
* `마인크래프트 - 바로가기(4)`
* ... 가 생기듯 4, 5, 6... 같은 식으로 다음에 남는 번호의 바로가기를 만들어 준다는 것이다.

```c
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main() {
  int foo바로가기 = open("foo.txt", O_CREAT | O_WRONLY | O_APPEND, 0644);
  int foo바로가기2 = dup(foo바로가기);

  printf("foo바로가기: %d, foo바로가기2: %d\n", foo바로가기, foo바로가기2);
  write(foo바로가기, "안녕 세상! ~ foo.txt - 바로가기\n", 41);
  write(foo바로가기2, "난 안녕 못한데 ~ foo.txt - 바로가기(2)\n", 50);
  close(foo바로가기);
  close(foo바로가기2);
  return 0;
}
❯ cat foo.txt
안녕 세상! ~ foo.txt - 바로가기
난 안녕 못한데 ~ foo.txt - 바로가기(2)
```

`foo바로가기`, `foo바로가기2` 모두 같은 `foo.txt` 파일을 가리키고 있다.

#### dup2

```c
int dup2(int oldfd, int newfd)
```

> newfd가 가리키는 파일을 oldfd가 가리키는 파일로 바꿈

* newfd `바로가기`가 이미 열려 있을 때: 닫은 후 새로 열게 된다
* 반환값: newfd
* **예제 1**

```c
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main() {
  int bar바로가기 = open("bar.txt", O_CREAT | O_WRONLY | O_APPEND, 0644);
  int 반환값 = dup2(bar바로가기, STDOUT_FILENO);

	printf("반환값: %d, bar바로가기: %d\n", 반환값, bar바로가기);
  printf("HAYO!\n");
  return 0;
}
❯ ./"dup"
❯ cat bar.txt
반환값: 1, bar바로가기: 3
HAYO!
```

`printf`를 했는데 터미널에 출력이 뜨지 않고 `bar.txt`에 그 내용이 기록되었다. 어째서일까? 우선 `STDOUT_FILENO`는 `표준 출력 바로가기`와 같다. 지금까지

* bar바로가기 -> "bar.txt"
* 표준 출력 바로가기\` -> 표준 출력

였다면 dup2로 표준 출력 바로가기가 가리키는 파일을 bar바로가기와 똑같이 바꿔줘서

* bar바로가기 -> "bar.txt"
* (좀 전까지)표준 출력 바로가기\` -> "bar.txt"

가 된 것이다. printf는 `표준 출력 바로가기`가 가리키는 파일에 내용을 출력하는데 이건 이제 "bar.txt"를 가리키는 `바로가기`가 되었으니 "bar.txt"에 출력 내용이 써지게 된 것이다.

* **예제 2**

```c
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main() {
  int bar바로가기 = open("bar.txt", O_CREAT | O_WRONLY | O_APPEND, 0644);
  int spam바로가기 = open("spam.txt", O_CREAT | O_WRONLY | O_APPEND, 0644);
  int 반환값 = dup2(bar바로가기, spam바로가기);

  printf("반환값: %d, bar바로가기: %d, spam바로가기: %d\n", 반환값, bar바로가기,
         spam바로가기);
	write(bar바로가기, "애옹\n", 8);
  write(spam바로가기, "꿈틀꿈틀\n", 14);
  return 0;
}
❯ ./"dup"
반환값: 4, bar바로가기: 3, spam바로가기: 4
❯ cat bar.txt
애옹
꿈틀꿈틀
```

* bar바로가기 -> "bar.txt"
* spam바로가기 -> "spam.txt"

dup2로 spam바로가기가 bar바로가기와 같은 파일을 가리키게 바꾸면

* bar바로가기 -> "bar.txt"
* spam바로가기 -> "bar.txt"

#### write

#### read

```c
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t count)
"바로가기가 가리키는 파일을 바이트로 읽기"

fd: 바로가기
buf: 읽은 바이트를 저장할 버퍼 포인터
count: 읽을 양
반환값: 읽은 바이트 수
```

```c
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

int main(void) {
  int 바로가기;
  char 읽은내용[8];

  // 읽기 전용 & 추가 모드로 파일 열기
  바로가기 = open("a.txt", O_RDWR | O_CREAT | O_APPEND, 0644);
  if (바로가기 < 0) {
    perror("파일 열던 중 오류 발생\n");
    exit(0);
  }
  for (int i = 0; i < 3; i++) {
    memset(읽은내용, 0, sizeof(읽은내용));
    read(바로가기, 읽은내용, sizeof(읽은내용));
    printf("[인덱스 %d] %s\n", i, 읽은내용);
  }
  close(바로가기);
}
```

예제 코드에서 변수 `바로가기`는 "a.txt"에 대한 바로가기를 담고 있다.

***

## Directory

### `dirent`, `DIR`

> 디렉토리 엔트리 (다윈 64비트 기준) DIR은 내부 구현이 숨겨짐 (모든 멤버 변수 앞에 `__`가 붙음): 라이브러리 함수를 사용

* [d\_reclen을 쓰는 이유](https://www.reddit.com/r/linuxquestions/comments/9w18fp/what\_is\_the\_d\_reclen\_member\_of\_the\_dirent/)
* [디렉토리 구조체](https://mymanual.tistory.com/1)

```c
64비트에서: /* _DARWIN_FEATURE_64_BIT_INODE가 정의되었을 시 */
struct dirent {
        ino_t      d_fileno;           /* 엔트리의 파일 번호 */
        __uint64_t d_seekoff;          /* 찾기 오프셋 (선택 가능, 서버에서 사용) */
        __uint16_t d_reclen;           /* 이 dirent 구조체 객체의 크기 */
        __uint16_t d_namlen;           /* 파일 이름(d_name) 길이 */
        __uint8_t  d_type;             /* 파일 타입 */
        char    d_name[1023 + 1];      /* 파일 이름 */
};
```

#### opendir

```c
DIR* opendir(const char *filename);
```

> 주어진 파일명의 디렉토리를 열어 디렉토리 스트림을 반환

* 다른 라이브러리 함수에 인자로 넘겨줌
* 디렉토리 스트림은 **동적 할당**되었으므로 `closedir()`로 할당 해제
* 오류 시 `NULL` 반환

#### readdir

```c
struct dirent* readdir(DIR *dirp);
```

> 다음 디렉토리 엔트리를 향한 포인터를 반환

```c
#include <dirent.h>
#include <stdio.h>
#include <errno.h>
#include <string.h>
#include <stdbool.h>

int main(void){
	DIR* dirp = opendir(".");
	if (dirp == NULL){
		printf("%s\n", strerror(errno));
		return 1;
	}

	while (true) {
		struct dirent* dp = readdir(dirp);
		if (!dp)
			break;
		if (errno != 0) {
			printf("%s\n", strerror(errno));
			return 1;
		}
		printf("%s\n", dp->d_name);
	}
	closedir(dirp);
	return (0);
}
.
..
learnReadline.c
makefile
test
test.c
```

* [errno 반환 시 유의 사항](https://stackoverflow.com/questions/16841590/why-readdir-returns-null-and-i-o-error-the-next-call-to-readdir-after-first-call)

#### closedir

```c
int closedir(DIR *dirp);
```

> 주어진 디렉토리 스트림을 닫음

* 오류 시 `-1` 반환

#### getcwd

> 현재 작업 디렉토리를 문자열로 반환

* 버퍼: 버퍼와 크기가 주어질 시 버퍼에 기록
  * 최대 경로 길이는 리눅스 커널의 경우 `linux/limits.h`에 정의되어 있음
    * `NAME_MAX`: 디렉토리 이름, 255자 (255 + '/' = 256)
    * `PATH_MAX`: 총 경로 길이, 4096자 (`NULL` 포함)
* **동적 할당**: 버퍼가 `NULL`일 때
* 오류 시 `NULL` 반환

#### chdir

```c
int chdir(const char *path);
```

> `path`로 작업 영역 변경

* 오류 시 `-1` 반환

***

## File

* [stat, fstat, lstat](https://bodamnury.tistory.com/37)

### struct stat

```c
64비트에서: /* _DARWIN_FEATURE_64_BIT_INODE가 정의되었을 시 */
struct stat {
    dev_t           st_dev;           /* ID of device containing file */
    mode_t          st_mode;          /* 파일 모드 (drwxrwxrwx) */
    nlink_t         st_nlink;         /* 하드 링크 수 */
    ino_t           st_ino;           /* 파일  */
    uid_t           st_uid;           /* User ID of the file */
    gid_t           st_gid;           /* Group ID of the file */
    dev_t           st_rdev;          /* Device ID */
    struct timespec st_atimespec;     /* time of last access */
    struct timespec st_mtimespec;     /* time of last data modification */
    struct timespec st_ctimespec;     /* time of last status change */
    struct timespec st_birthtimespec; /* time of file creation(birth) */
    off_t           st_size;          /* file size, in bytes */
    blkcnt_t        st_blocks;        /* blocks allocated for file */
    blksize_t       st_blksize;       /* optimal blocksize for I/O */
    uint32_t        st_flags;         /* user defined flags for file */
    uint32_t        st_gen;           /* file generation number */
    int32_t         st_lspare;        /* 예약됨: 쓰지 말 것! */
    int64_t         st_qspare[2];     /* 예약됨: 쓰지 말 것! */
};
```

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/stat.h>
#include <sys/types.h>

typedef int (*statfunc)(const char *path, struct stat *buf);

int do_stat(char *path, statfunc statf){
  struct stat fileStat;
  if(statf(path, &fileStat) < 0)
    return 1;
  printf("---------------------\n");
  printf("파일 이름: \t%s\n", path);
  printf("파일 크기: \t%lld 바이트\n", fileStat.st_size);
  printf("하드 링크 수: \t%d\n", fileStat.st_nlink);
  printf("파일 아이노드: \t%llu\n", fileStat.st_ino);

  printf("파일 권한: \t");
  printf( (S_ISDIR(fileStat.st_mode)) ? "d" : "-");
  printf( (fileStat.st_mode & S_IRUSR) ? "r" : "-");
  printf( (fileStat.st_mode & S_IWUSR) ? "w" : "-");
  printf( (fileStat.st_mode & S_IXUSR) ? "x" : "-");
  printf( (fileStat.st_mode & S_IRGRP) ? "r" : "-");
  printf( (fileStat.st_mode & S_IWGRP) ? "w" : "-");
  printf( (fileStat.st_mode & S_IXGRP) ? "x" : "-");
  printf( (fileStat.st_mode & S_IROTH) ? "r" : "-");
  printf( (fileStat.st_mode & S_IWOTH) ? "w" : "-");
  printf( (fileStat.st_mode & S_IXOTH) ? "x" : "-");
  printf("심볼릭 링크%s\n", (S_ISLNK(fileStat.st_mode)) ? "임" : "가 아님");

  printf("---------------------\n\n");
  return (0);
}
❯ echo Hello World! > foo.txt
❯ ln -s foo.txt sym
```

#### stat

```c
int stat(const char *restrict path, struct stat *restrict buf);
```

> 파일에 대한 정보를 `stat` 구조체에 기록. 파일이 링크면 **원본**에 대한 정보를 기록

```c
❯ ./"test"
---------------------
파일 이름:      foo.txt
파일 크기:      13 바이트
하드 링크 수:   1
파일 아이노드:   5202623
파일 권한:      -rw-r--r--
심볼릭 링크가 아님
---------------------

---------------------
파일 이름:      sym /* 실제로는 foo.txt에 대한 정보임 */
파일 크기:      13 바이트
하드 링크 수:   1
파일 아이노드:     5202623
파일 권한:      -rw-r--r--
심볼릭 링크가 아님
---------------------
```

#### lstat

> `stat()`과 동일, 파일이 링크면 **링크**에 대한 정보를 기록

```c
---------------------
파일 이름:      foo.txt
파일 크기:      13 바이트
하드 링크 수:   1
파일 아이노드:  5202623
파일 권한:      -rw-r--r--
심볼릭 링크가 아님
---------------------

---------------------
파일 이름:      sym
파일 크기:      7 바이트
하드 링크 수:   1
파일 아이노드:  5202635
파일 권한:      -rwxr-xr-x
심볼릭 링크임
---------------------
```

#### fstat

```c
fstat(int fildes, struct stat *buf);
```

> `stat()`과 동일하나 `fd` (바로가기)를 입력으로 받음

#### unlink

```c
int unlink(const char *path);
```

> 파일을 삭제

* 내부적으로는
  * 파일에 연결된 하드 링크 수 -1
  * 하드 링크가 0개이고 그 파일을 `open`한 프로세스가 없을 시 디스크에서 지움

***

## Errors

#### strerror

```c
#include <string.h>

char *strerror(int errnum);
```

> 오류 번호에 해당하는 메세지를 반환하는 함수

#### errno

```c
#include <sys/errno.h>
```

> 오류 번호

* 오류가 발생했을 때 설정되는 값
  * 설정되면 다음 오류가 발생하기 전까지 유지
* 과제 진행하면서 사용하는 errno에 대해서만 추가할 예정

***

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

***

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

***

## Signal

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

#### signal

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

#### kill

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
* `sig`: 전달할 신호 (참고: [신호란?](undefined.md#Signal))

***

## Terminal

### TTY

> Teletypewriter

* tty0 : 컴퓨터와 직접 연결
* tty1 \~ tty6: 가상 콘솔
  * 시스템 관리시 콘솔 1개로 여러 작업이 불편할 수 있어 가상의 화면 6개를 제공
* 즉, terminal type device를 의미

```c
#include <unistd.h>
```

#### isatty

```c
int isatty(int fd);
```

> fd가 유효한 터미널에 연결되어 있는지 확인하는 함수

* 유효할 경우 1을 반환, 오류 시 0을 반환

#### ttyname

```c
char *ttyname(int fd);
```

> fd가 연결된 유효한 터미널의 이름을 반환하는 함수

* 정적 버퍼에 저장된 이름을 반환
* 유효하지 않으면 NULL을 반환

#### ttyslot

```c
int ttyslot(void);
```

> (legacy function) 현재 사용중인 제어 터미널의 인덱스를 반환하는 함수

* 보통 /etc/utmp 파일의 현재 사용자에 대한 엔트리의 인덱스와 동일

#### example

```c
#include <unistd.h>
#include <stdio.h>

int main(void)
{
    int fd;

    fd = ttyslot();
    printf("fd(%d)", fd);
    if (isatty(fd))
    {
        printf(" is a tty.\n");
        printf("ttyname = %s\n", ttyname(fd));
    }
    else
        printf(" is not a tty.\n");
    return (0);
}

----------a.out----------
fd(0) is a tty.
ttyname = /dev/ttys003 <- 터미널에 따라 달라짐
```

\


### Termios Struct

> 터미널 속성구조체

```c
#include <termios.h>

struct termios {
	tcflag_t        c_iflag;        /* input flags */
	tcflag_t        c_oflag;        /* output flags */
	tcflag_t        c_cflag;        /* control flags */
	tcflag_t        c_lflag;        /* local flags */
	cc_t            c_cc[NCCS];     /* control chars */
	speed_t         c_ispeed;       /* input speed */
	speed_t         c_ospeed;       /* output speed */
};
```

<details>

<summary>터미널 속성값</summary>

* `c_iflag`
* `c_oflag`
* `c_cflag`
* `c_lflag`
* `c_cc[NCCS]`
* `c_ispeed`
* `c_ospeed`

</details>

#### tcsetattr

```c
int tcsetattr(int fildes, int optional_actions, const struct termios *termios_p);
```

> termios 구조체를 통해 fildes의 터미널 속성을 설정하는 함수

*   `optional_actions`: 동작 선택

    | **option** |                                          |
    | :--------: | ---------------------------------------- |
    |   TCSANOW  | 변경을 바로 적용                                |
    |  TCSADRAIN | 모든 출력이 끝난 뒤에 적용, 출력에 관한 속성을 변경할 때 사용     |
    |  TCSAFLUSH | 모든 출력이 끝난 뒤에 적용, 읽지 않은 입력은 폐기            |
    |  TCSASOFT  | c\_cflag, c\_ispeed, c\_ospeed 필드의 값을 무시 |

#### tcgetattr

```c
int tcgetattr(int fildes, struct termios *termios_p);
```

> termios 구조체를 통해 fildes의 터미널 속성을 가져오는 함수

\


### Curses Termcap

> termcap 라이브러리를 위한 curses(cursor motion optimization) 인터페이스

* 터미널 화면 조작을 위한 도구
  * 각각의 단말기의 특성을 기록해 두는 database(terminfo) 사용

```c
#include <curses.h>
#include <term.h>
```

#### tgetent

```c
int tgetent(char *bp, const char *name);
```

> 이름 항목을 가져오는 함수

* 성공 시 1, 없으면 0, terminfo를 찾을 수 없을 시 -1 반환

#### tgetflag

```c
int tgetflag(char *id);
```

> id의 사용가능 여부를 확인하는 함수

* id 앞에서 2개의 문자값을 이용해 검색
* 사용가능 시 1, 사용불가 시 0 반환

#### tgetnum

```c
int tgetnum(char *id);
```

> id의 숫자값을 확인하는 함수

* 사용불가 시 -1 반환

#### tgetstr

```c
char *tgetstr(char *id, char **area);
```

> id의 문자열을 확인하는 함수

* 사용불가 시 NULL 반환
* `area`(buffer)에도 복사됨

#### tgoto

```c
char *togo(const char *cap, int col, int row);
```

> 주어진 cap을 따라 매개변수들을 인스턴스화 시키는 함수

* 출력값은 tputs 함수로 전달됨

#### tputs

```c
int tputs(const char *str, int affcnt, int (*putc)(int));
```

> padding 정보를 str로 적용해 출력하는 함수

* `str`: tparm, tgetstr, tgoto의 반환 값
* `affcnt`: 영향을 받을 줄 수
* `putc`: 문자를 한 번에 하나씩 받는 putchar와 같은 함수

***

#### ioctl

```c
#include <sys/ioctl.h>

int ioctl(int fildes, unsigned log request, ...);
```

> 하드웨어 제어 및 상태 확인

* 오류:
  * EBADF: `fildes`가 유효하지 않음
  * EINVAL: `request`나 가변 인자가 올바르지 않음
  * ENOTTY: `fildes`가 [character special device](https://www.ibm.com/docs/en/zos/2.3.0?topic=csf-character-special-files) 파일에 해당하지 않음 (터미널, fd, 콘솔...)
  * ENOTTY: `request`가 `filedes`에 맞는 요청이 아님
* [#1](https://wogh8732.tistory.com/306)

#### getenv

```c
#include <stdlib.h>

char *getenv(const char *name);
```

> 환경 변수`name` 에 대한 값 반환

* 오류 EINVAL: `name`이 `NULL` / 빈 문자열 / `=`를 포함하는 문자열
