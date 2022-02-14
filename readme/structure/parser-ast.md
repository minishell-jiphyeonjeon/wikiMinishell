# Parser: AST 구조

![bash-parser의 결과를 AST로 나타낸 그림](<../../.gitbook/assets/image (4).png>)

### type: Script

> commands 배열

* tokens 내에 `;`(COMMAND)가 있다면 parsing 시작지점부터 `;`전 까지의 token을 parsing
* parsing이 끝나면 시작지점을 다음으로 넘김

### type: Pipeline

> commands 배열

* tokens 내에 `|`(PIPELINE)이 있다면 parsing 시작지점부터 `|`전 까지의 token을 한 세트, \
  `|` 이후부터 `;`전까지 혹은 끝까지를 한 세트로 묶어 parsing

### type: Command

> name\
> suffix 배열

* meta character가 아닌 parsing 구간의 첫 번째 token은 반드시 name에 해당
* 만약 구간의 첫 번째 tokentype이 REDIRECT라면 name은 빈 문자열이 됨
* 나머지 token은 전부 suffix

### type: Word

> text\
> expansion 배열

* token의 text를 그대로 가져옴
*   만약 text가 `“$”` 구조라면 `$`개수만큼 expansion 생성

    #### type: ParameterExpansion

    > parameter\
    > location 구조체

    *   환경변수로 가져와야하는 변수이름을 parameter에 저장

        `ex)”hello $USER” → parameter = USER`
    * 환경변수 값을 가져와 치환할 위치의 start와 end index를 저장

### type: Redirect

> op 구조체\
> file 구조체

* `>`: great / `>>`: dgreat / `<`: less / `<<`: dless (text: type)
* file의 이름 Word 구조체로 정의
