# 코드 작성 규칙

### 폴더 구성
```
/root
├── include
├── src
|   ├── 범주별로 폴더화
|   └── ...
└── Makefile
```

---

### .h
> minishell.h
- src의 .c 파일들이 불러올 헤더파일
- 사용하는 모든 헤더 파일을 `#include`

> src 폴더별 .h
- 분류된 폴더별 전용 헤더파일
- 해당 폴더에서만 사용하는 상수나 구조체 정의
- 폴더별 static 함수를 제외한 함수의 프로토타입 선언
- minishell.h에서 include를 담당하기에 정말 특별한 케이스(오로지 여기에서만 쓰는 헤더가 있다?!)가 아니라면 include는 지양

> type.h
- 공통으로 사용하는 상수나 구조체 정의

---

### 접두사 | 접미사
- 동적할당과 관련된 경우 `new_`와 `del_`로 시작
- bool 함수의 경우 `is_`로 시작
- libft에 속하는 함수는 모두 `ft_`로 시작
    - 단, 이 안에서도 동적할당, bool 함수의 경우 위의 규칙을 따름
- 함수포인터는 `_f`로 종료

### 함수명 | 변수명
- libft는 기존에 있는 함수를 모방한 것들이 대부분이라 단어 사이의 `_`가 생략되어있지만 그 외의 함수명은 단어 사이에 `_`를 사용하여 작성 `ex) logo_print()`
- 동사가 뒤에 오도록 작성 `ex) struct_init(), num_count()`
    - 단, `new_`, `del_`, `is_`는 그대로 앞에 사용 
- 단어는 최대한 그대로 사용하고, 줄여서 사용할 단어는 협의해서 사용
    - sh == shell
    - num == number
    - str == string
    - arr == array
    - len == length
    - res == result
    - i, idx == index

