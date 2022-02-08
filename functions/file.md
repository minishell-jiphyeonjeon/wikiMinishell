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
