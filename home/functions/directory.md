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