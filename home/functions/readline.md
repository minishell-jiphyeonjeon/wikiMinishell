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