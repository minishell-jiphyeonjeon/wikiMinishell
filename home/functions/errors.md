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
