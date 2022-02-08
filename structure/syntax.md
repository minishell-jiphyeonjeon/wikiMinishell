## 문자열 구문 분석

```bash
❯ echo 'i say "$USER says hello"' "['$USER name is' $USER]"
# i say "$USER says hello" ['youkim name is' youkim]
```

### `'`, `"`, 공백 순으로 나누기

```bash
❯ echo
❯ 'i say "$USER says hello"'
❯ "['$USER name is' $USER]"
```

### 문자열 `"`

- 공백 단위로 나누기

```bash
❯ ['$USER
❯ name
❯ is
❯ $USER]
```
- 내부의 `$`를 치환, 범위는 알파벳/숫자가 아닐 때까지

```bash
❯ ['$USER
❯ name
❯ is
❯ $USER]
```

- 공백을 붙여 다시 합침
```bash
❯ 'USER wanna go' USER
```

3. 프로그램 탐색

```bash
❯ echo
❯ 'i say "$USER says hello"'
❯ "i 'wanna go' /Users/foo"
```

찾기 순서:

    1. 빌트인 함수
    2. 환경 변수
    3. 절대경로로

1. 프로그램 실행

## here doc

[stack overflow](https://stackoverflow.com/questions/7046381/multiline-syntax-for-piping-a-heredoc-is-this-portable)