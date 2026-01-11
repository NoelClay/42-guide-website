---
description: >-
  여기서 작동하는 하나의 솔루션을 제안합니다. 하지만, 솔루션을 보지 않고 파트 3에서 설명한 내용을 먼저 읽어보시기를 권장합니다. 이 부분은 비상시에만 사용하십시오.
---

# ▪️ 주석이 달린 솔루션

아래에서는 저(Simon)의 코드를 사용합니다. 하지만 일부 측면에서 제 코드와는 약간 다른 Laura의 코드도 참고하실 수 있습니다. 이 프로젝트에 대한 저희의 Github 링크는 다음과 같습니다:

* [Simon의 get\_next\_line](https://github.com/Laendrun/42/tree/main/get_next_line)

{% hint style="danger" %}
<mark style="color:red;">**다시 한번 강조합니다. 스스로 해결해 보시고, 정말 최후의 문제가 생겼을 때만 솔루션을 참고하십시오! 그리고 만약 답을 보더라도, 자신이 무엇을 하고 있는지 이해하려고 노력하십시오 ;)**</mark>
{% endhint %}

저는 이 프로젝트를 보너스까지 포함하여 만들었으므로, 두 가지 버전 모두를 제공합니다. 저는 일반 버전부터 시작할 것이며, 보너스를 어떻게 구현할 수 있는지 스스로 찾아보시기를 바랍니다.

{% hint style="info" %}
보너스를 작동시키기 위해 총 20~25자 정도만 추가하면 되었습니다. 이 프로젝트의 보너스는 쉽게 달성할 수 있습니다.
{% endhint %}

{% hint style="warning" %}
LIBFT 함수에는 주석을 달지 않겠습니다. 여러분은 자신만의 LIBFT 함수를 가지고 있으므로, 해당 함수들이 어떻게 작동하는지 알고 계시기를 바랍니다.
{% endhint %}

<details>

<summary>get_next_line.c</summary>

{% code title="get_next_line.c" overflow="wrap" lineNumbers="true" %}
```c
#include "get_next_line.h"

static char    *_fill_line_buffer(int fd, char *left_c, char *buffer);
static char    *_set_line(char *line);
static char    *ft_strchr(char *s, int c);

char    *get_next_line(int fd)
{
    static char *left_c;
    char        *line;
    char        *buffer;
    
    buffer = (char *)malloc((BUFFER_SIZE + 1) * sizeof(char));
    /*
     * fd < 0 : 이는 file descriptor가 유효하지 않음을 의미합니다.
     * BUFFER_SIZE <= 0 : 한 번에 BUFFER_SIZE 개수만큼 문자를 읽으므로,
     * 0개 이하의 문자를 읽을 수는 없습니다.
     * read(fd, 0, 0) < 0 : 이 검사는 파일이 존재하는지, 읽을 내용이 있는지,
     * 또는 파일을 읽기 위해 열 수 있는지 확인하게 해줍니다. 
     * file descriptor가 0보다 크더라도 '쓰기 전용(modify only)'으로
     * 열려 있다면 읽을 수 없습니다.
     */
    if (fd < 0 || BUFFER_SIZE <= 0 || read(fd, 0, 0) < 0)
    {
        free(left_c);
        free(buffer);
        left_c = NULL;
        buffer = NULL;
        return (NULL);
    }
    if (!buffer)
        return (NULL);
    line = _fill_line_buffer(fd, left_c, buffer);
    /* 이 함수에서 나중에 buffer 변수를 사용하지 않을 것이므로 여기서 free해야 합니다.
     * free하는 것은 memory leaks를 방지합니다.
     */
    free(buffer);
    buffer = NULL;
    if (!line)
        return (NULL);
    left_c = _set_line(line);
    return (line);
}

static char *_set_line(char *line_buffer)
{
    char    *left_c;
    ssize_t    i;
    
    i = 0;
    /* 이 루프는 \n 또는 \0를 만났을 때 줄의 끝을 찾을 수 있도록 해줍니다.
     */
    while (line_buffer[i] != '\n' && line_buffer[i] != '\0')
        i++;
    /* 여기서 현재 또는 다음 문자가 \0인지 확인합니다.
     * 만약 그렇다면, 이 줄은 비어 있다는 의미이므로 NULL을 반환합니다.
     * 이는 다음 줄이 없을 경우 NULL을 반환하라는 과제의 요구사항입니다.
     */
    if (line_buffer[i] == 0 || line_buffer[1] == 0)
        return (NULL);
    /* 여기서 줄의 끝부터 전체 line_buffer의 끝까지 substring을 취합니다.
     * 이것이 우리 줄에서 남은 부분입니다.
     */
    left_c = ft_substr(line_buffer, i + 1, ft_strlen(line_buffer) - i);
    if (*left_c == 0)
    {
        free(left_c);
        left_c = NULL;
    }
    /* 줄을 NUL-terminate하기 위해 마지막 문자를 \0로 설정하는 것을 잊지 마십시오.
     */    
    line_buffer[i + 1] = 0;
    return (left_c);
}

static char	*_fill_line_buffer(int fd, char *left_c, char *buffer)
{
        /* ssize_t 타입은 size_t 타입과 비슷하게 작동하지만, 음수일 수 있습니다.
         * size_t 타입은 음수가 될 수 없습니다. 
         * 우리가 사용할 시스템 함수의 대부분은 오류를 나타내기 위해 -1을 반환하므로,
         * 음수를 저장할 수 있는 것은 유용할 수 있습니다.
         */
	ssize_t	b_read;
	char	*tmp;

	b_read = 1;
	while (b_read > 0)
	{
		b_read = read(fd, buffer, BUFFER_SIZE);
		/* 만약 b_read가 -1이라면, file descriptor를 읽는 동안 오류가
		 * 발생했다는 의미이므로, left_c를 free하고 NULL을 반환합니다.
		 */
		if (b_read == -1)
		{
			free(left_c);
			return (NULL);
		}
		/* 만약 b_read가 0이라면, 파일을 모두 읽었음을 의미하므로
		 * 루프에 머무를 필요가 없습니다.
		 * 만약 아무것도 읽지 않았다면, 루프를 빠져나갈 수 있습니다.
		 */
		else if (b_read == 0)
			break ;
		/* 문자열을 NUL-terminate하기 위해 buffer의 마지막 문자를
		 * 0으로 설정하는 것을 잊지 마십시오.
		 */
		buffer[b_read] = 0;
		/* 여기서 left_c static char *가 비어 있는지 확인합니다.
		 * 왜냐하면 비어 있다면 ft_strjoin을 사용할 수 없기 때문입니다.
		 */
		if (!left_c)
			left_c = ft_strdup("");
		tmp = left_c;
		/* left_c를 비어 있도록 설정했거나, 지난번 get_next_line을 호출했을 때
		 * 남은 무언가가 left_c에 있었다면, 방금 읽은 buffer를 left_c에 join할 수 있습니다.
		 */
		left_c = ft_strjoin(tmp, buffer);
		free(tmp);
		tmp = NULL;
		/* 방금 읽은 buffer에서 \n을 읽었는지 여부를 검색합니다.
		 * 만약 그렇다면, 루프를 빠져나갈 수 있습니다.
		 * 그렇지 않다면, 파일에서 더 읽기 위해 루프를 다시 실행합니다.
		 */
		if (ft_strchr(buffer, '\n'))
			break ;
	}
	/* 이 함수의 끝에서 left_c 문자열을 반환합니다.
	 * 이 문자열에는 우리가 읽은 모든 내용이 포함되며,
	 * 그 안에 \0 또는 \n이 있는지 확인합니다.
	 */
	return (left_c);
}

static char	*ft_strchr(char *s, int c)
{
	unsigned int	i;
	char			cc;

	cc = (char) c;
	i = 0;
	while (s[i])
	{
		if (s[i] == cc)
			return ((char *) &s[i]);
		i++;
	}
	if (s[i] == cc)
		return ((char *) &s[i]);
	return (NULL);
}
```
{% endcode %}

</details>

<details>

<summary>get_next_line_utils.c</summary>

{% code title="get_next_line_utils.c" overflow="wrap" lineNumbers="true" %}
```c
#include "get_next_line.h"

char	*ft_strdup(char *s1)
{
	char			*dest;
	unsigned int	i;

	dest = (char *) malloc(ft_strlen(s1) + 1);
	if (!dest)
		return (NULL);
	i = 0;
	while (s1[i])
	{
		dest[i] = s1[i];
		i++;
	}
	dest[i] = 0;
	return (dest);
}

size_t	ft_strlen(char *s)
{
	int	i;

	i = 0;
	while (s[i])
		i++;
	return (i);
}

char	*ft_substr(char *s, unsigned int start, size_t len)
{
	size_t	i;
	char	*str;

	if (!s)
		return (NULL);
	if (start > ft_strlen(s))
		return (malloc(1));
	if (len > ft_strlen(s + start))
		len = ft_strlen(s + start);
	str = malloc((len + 1) * sizeof(char));
	if (!str)
		return (NULL);
	i = 0;
	while (i < len)
	{
		str[i] = s[start + i];
		i++;
	}
	str[i] = 0;
	return (str);
}

char	*ft_strjoin(char *s1, char *s2)
{
	char			*res;

	res = (char *) malloc((ft_strlen(s1) + ft_strlen(s2) + 1) * sizeof(char));
	if (!res)
		return (NULL);
	fill_str(res, s1, s2);
	return (res);
}

void	fill_str(char *res, char *s1, char *s2)
{
	unsigned int	i;
	unsigned int	j;

	i = 0;
	j = 0;
	while (s1[j])
		res[i++] = s1[j++];
	j = 0;
	while (s2[j])
		res[i++] = s2[j++];
	res[i] = '\0';
}
```
{% endcode %}

</details>

<details>

<summary>get_next_line.h</summary>

{% code title="get_next_line.h" overflow="wrap" lineNumbers="true" %}
```c
#ifndef GET_NEXT_LINE_H
# define GET_NEXT_LINE_H
# ifndef BUFFER_SIZE
#  define BUFFER_SIZE 42
# endif
# include <stdlib.h>
# include <unistd.h>
# include <fcntl.h>

char    *get_next_line(int fd);
char    *ft_strdup(char *s);
size_t    ft_strlen(char *s);
char    *ft_substr(char *s, unsigned int start, size_t len);
char    *ft_strjoin(char *s1, char *s2);
void    fill_str(char *res, char *s1, char *s2);

#endif
```
{% endcode %}

</details>

### 보너스 부분

앞서 말씀드렸듯이, 저는 이 프로젝트의 보너스를 만들었으므로, 아래에 보너스 부분의 코드를 첨부합니다.

{% hint style="danger" %}
앞서 말했듯이, 보너스는 쉽게 달성할 수 있으므로, 스스로 보너스를 시도해 보시기를 강력히 권장합니다.
{% endhint %}

<details>

<summary>get_next_line_bonus.c</summary>

{% code title="get_next_line_bonus.c" overflow="wrap" lineNumbers="true" %}
```c
#include "get_next_line.h"

static char		*_fill_line_buffer(int fd, char *left_c, char *buffer);
static char		*_set_line(char *line);
static char		*ft_strchr(char *s, int c);

char	*get_next_line(int fd)
{
	/* 보너스를 작동시키기 위한 최소한의 차이만 있습니다.
	 * 기본적으로 static char * 변수를 char * 배열로 변환하는 것입니다.
	 * 보시다시피 저는 배열의 요소 수를 상수 MAX_FD로 설정했습니다
	 * (MAX_FD가 무엇인지 확인하려면 get_next_line.h를 참조하십시오).
	 */
	static char	*left_c[MAX_FD];
	char		*line;
	char		*buffer;

	buffer = (char *)malloc((BUFFER_SIZE + 1) * sizeof(char));
	if (fd < 0 || BUFFER_SIZE <= 0 || read(fd, 0, 0) < 0)
	{
		/* static char *를 배열로 변경했기 때문에,
		 * 우리가 작업하고자 하는 인덱스를 지정해야 합니다.
		 * 가장 쉬운 방법은 이를 fd로 설정하는 것입니다.
		 */
		free(left_c[fd]);
		free(buffer);
		left_c[fd] = NULL;
		buffer = NULL;
		return (0);
	}
	if (!buffer)
		return (NULL);
	/* 여기에서도 마찬가지로, 남은 문자들을 fd의 인덱스에 있는 배열에 저장하기를 원합니다.
	 * 따라서 다른 fd가 있는 경우, 해당 fd에서 남은 내용을 덮어쓰지 않게 됩니다.
	 */
	line = _fill_line_buffer(fd, left_c[fd], buffer);
	free(buffer);
	buffer = NULL;
	if (!line)
		return (NULL);
	/* 그리고 여기에서도 left_c를 left_c[fd]로 바꿔야 합니다.
	 * 이것이 우리가 바꿔야 할 마지막 부분입니다.
	 * 우리가 left_c를 사용하는 다른 모든 곳(다른 모든 함수에서)에서는,
	 * 이를 문자열로 사용합니다.
	 * 따라서 left_c[fd]를 매개변수로 전달하고 있기 때문에,
	 * 기본적으로 문자열을 매개변수로 전달하는 것이므로 문제될 것이 없으며,
	 * 변경할 필요도 없습니다.
	 */
	left_c[fd] = _set_line(line);
	return (line);
}

static char	*_set_line(char *line_buffer)
{
	char	*left_c;
	ssize_t	i;

	i = 0;
	while (line_buffer[i] != '\n' && line_buffer[i] != '\0')
		i++;
	if (line_buffer[i] == 0 || line_buffer[1] == 0)
		return (0);
	left_c = ft_substr(line_buffer, i + 1, ft_strlen(line_buffer) - i);
	if (*left_c == 0)
	{
		free(left_c);
		left_c = NULL;
	}
	line_buffer[i + 1] = 0;
	return (left_c);
}

static char	*_fill_line_buffer(int fd, char *left_c, char *buffer)
{
	ssize_t	b_read;
	char	*tmp;

	b_read = 1;
	while (b_read > 0)
	{
		b_read = read(fd, buffer, BUFFER_SIZE);
		if (b_read == -1)
		{
			free(left_c);
			return (0);
		}
		else if (b_read == 0)
			break ;
		buffer[b_read] = 0;
		if (!left_c)
			left_c = ft_strdup("");
		tmp = left_c;
		left_c = ft_strjoin(tmp, buffer);
		free(tmp);
		tmp = NULL;
		if (ft_strchr(buffer, '\n'))
			break ;
	}
	return (left_c);
}

static char	*ft_strchr(char *s, int c)
{
	unsigned int	i;
	char			cc;

	cc = (char) c;
	i = 0;
	while (s[i])
	{
		if (s[i] == cc)
			return ((char *) &s[i]);
		i++;
	}
	if (s[i] == cc)
		return ((char *) &s[i]);
	return (NULL);
}
```
{% endcode %}

</details>

<details>

<summary>get_next_line_utils_bonus.c</summary>

{% code title="get_next_line_utils_bonus.c" overflow="wrap" lineNumbers="true" %}
```c
#include "get_next_line.h"

char	*ft_strdup(char *s1)
{
	char			*dest;
	unsigned int	i;

	dest = (char *) malloc(ft_strlen(s1) + 1);
	if (!dest)
		return (NULL);
	i = 0;
	while (s1[i])
	{
		dest[i] = s1[i];
		i++;
	}
	dest[i] = 0;
	return (dest);
}

size_t	ft_strlen(char *s)
{
	int	i;

	i = 0;
	while (s[i])
		i++;
	return (i);
}

char	*ft_substr(char *s, unsigned int start, size_t len)
{
	size_t	i;
	char	*str;

	if (!s)
		return (NULL);
	if (start > ft_strlen(s))
		return (malloc(1));
	if (len > ft_strlen(s + start))
		len = ft_strlen(s + start);
	str = malloc((len + 1) * sizeof(char));
	if (!str)
		return (NULL);
	i = 0;
	while (i < len)
	{
		str[i] = s[start + i];
		i++;
	}
	str[i] = 0;
	return (str);
}

char	*ft_strjoin(char *s1, char *s2)
{
	char			*res;

	res = (char *) malloc((ft_strlen(s1) + ft_strlen(s2) + 1) * sizeof(char));
	if (!res)
		return (NULL);
	fill_str(res, s1, s2);
	return (res);
}

void	fill_str(char *res, char *s1, char *s2)
{
	unsigned int	i;
	unsigned int	j;

	i = 0;
	j = 0;
	while (s1[j])
		res[i++] = s1[j++];
	j = 0;
	while (s2[j])
		res[i++] = s2[j++];
	res[i] = '\0';
}
```
{% endcode %}

</details>

<details>

<summary>get_next_line_bonus.h</summary>

{% code title="get_next_line_bonus.h" overflow="wrap" lineNumbers="true" %}
```c
/*
 * MAX_FD 매크로는 현재 OS 및 온라인에서 찾은 정보(MacOS Ventura 13.0)를 기반으로
 * 최대 file descriptor 수에 맞춰 정의되었습니다.
 * (이 정보는 특히 찾기 어렵거나, Google에서 무엇을 검색해야 할지 제가 모를 수도 있습니다.)
 */
#ifndef GET_NEXT_LINE_BONUS_H
# define GET_NEXT_LINE_BONUS_H
# ifndef BUFFER_SIZE
#  define BUFFER_SIZE 15
# endif
# define MAX_FD 10240
# include <stdlib.h>
# include <unistd.h>
# include <fcntl.h>

char	*get_next_line(int fd);

char	*ft_strdup(char *s);
size_t	ft_strlen(char *s);
char	*ft_substr(char *s, unsigned int start, size_t len);
char	*ft_strjoin(char *s1, char *s2);
void	fill_str(char *res, char *s1, char *s2);

#endif
```
{% endcode %}

</details>
