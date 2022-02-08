
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

    | **option** |                                                               |
    | :--------: | ------------------------------------------------------------- |
    |  TCSANOW   | 변경을 바로 적용                                              |
    | TCSADRAIN  | 모든 출력이 끝난 뒤에 적용, 출력에 관한 속성을 변경할 때 사용 |
    | TCSAFLUSH  | 모든 출력이 끝난 뒤에 적용, 읽지 않은 입력은 폐기             |
    |  TCSASOFT  | c\_cflag, c\_ispeed, c\_ospeed 필드의 값을 무시               |

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
