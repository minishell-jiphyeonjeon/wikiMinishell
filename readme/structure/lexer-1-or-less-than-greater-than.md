---
description: bash 분석 결과 정리
---

# Lexer: 구문 분석 (1) - |, <, >

### White-space

token을 구분짓는 공백은 한 개 이상이 있어도 한 번만 처리

```c
echo a b c | cat -e
echo         a    b  c          | cat     -e
echo   a     b   c |       cat    -e
```



### Metacharacter: `|`, `<`, `>`

> 유효하지 않은 조합을 만든 구분문자를 syntax error와 함께 출력

* `|`는 뒤에 다른 조합이 와도 유효함
* `<, >`는 같은 문자로 2개까지 올 수 있고, 다른 조합이 올 수 없음
* [bash에서 가능한 redirection 조합](https://zsh.sourceforge.io/Doc/Release/Redirection.html)이라도 minishell에서는 `<, >, >>`만 인정하고, `<<`(heredoc)은 사용할 수 있지만 `>>>`(herestring)은 사용불가

#### unexpected token `|`

![](<../../.gitbook/assets/image (6).png>)

```c
echo <|
echo <<|
echo >| /* bash에서는 clobber로 유효한 표현  */
echo >>|
echo |>>|
echo ||||||||||<<|
```

#### unexpected token `<` `<<`

![](<../../.gitbook/assets/image (5).png>)

![](<../../.gitbook/assets/image (1).png>)

```c
echo <<< /* bash에서는 herestring으로 유효한 표현 */
echo ><
echo >><
echo |<<<

echo <<<< /* bash에서는 herestring 때문에 token이 `<'로 나타남 */
echo <<<<<<<<<<<<<<<<<<<<
echo ><<
echo >><<
echo |<<<<
```

#### unexpected token `>` `>>`

![](<../../.gitbook/assets/image (2).png>)

![](<../../.gitbook/assets/image (3).png>)

```c
echo >>>
echo <> /* bash에서는 유효한 표현 */
echo <><
echo <><>
echo |>>>

echo >>>>
echo <>> /* bash에서는 <>가 유효해서 token이 `>'로 나타남 */
echo >>>>>>>>>>>>>>>>
echo |>>>>
```
