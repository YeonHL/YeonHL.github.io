---
title: (Python) 기본 규칙
description: 파이썬의 주요 타입과 주석 작성 방법입니다.
date: 2025-04-23 19:00:00+0900
lastmod: 2025-04-23
slug: basic-rules
comments: true
math: false
categories:
  - Python
tags: 
keywords:
  - Python
---

## 데이터 나타내기

### 숫자 (Numbers)

**산술 연산자 (Arithmetic Operator)**

| 산술 연산자 | 의미   |
| ----------- | ------ |
| `+`         | 덧셈   |
| `-`         | 뺄셈   |
| `*`         | 곱셈   |
| `**`        | 제곱   |
| `/`         | 나눗셈 |
| `//`        | 몫     |
| `%`         | 나머지 |

**대입 연산자 (Assignment Operator)**

- 변수를 선언하고 값을 할당할 때 사용합니다.

| 연산자 | 내용                                     |
| ------ | ---------------------------------------- |
| `=`    | 좌변에 우변 값 할당                      |
| `+=`   | 좌변 값에 우변 값을 더하여 할당          |
| `-=`   | 좌변 값에 우변 값을 빼서 할당            |
| `*=`   | 좌변 값에 우변 값을 곱해서 할당          |
| `/=`   | 좌변 값에 우변 값을 나눠서 할당          |
| `%=`   | 좌변 값을 우변 값으로 나눈 나머지 할당   |
| `//=`  | 좌변 값을 우변 값으로 나눈 몫 할당       |
| `**=`  | 좌변 값을 우변 값으로 지수 연산하여 할당 |

인터프리터는 간단한 계산기처럼 작동합니다: 표현식을 입력하면 값을 출력합니다. 표현식 구문은 간단합니다: 연산자 `+`, `-`, `*`, `/`를 사용하여 산술 연산을 수행할 수 있습니다; 괄호(`()`)를 사용하여 그룹화할 수 있습니다. 예를 들어:

```python
>>> 2 + 2
4
>>> 50 - 5*6
20
>>> (50 - 5*6) / 4
5.0
>>> 8 / 5  # division always returns a floating-point number
1.6
```

정수 (예를 들어 `2`, `4`, `20`)는 `int` 형입니다. 소수부가 있는 것들 (예를 들어 `5.0`, `1.6`)은 `float` 형입니다. 이 자습서 뒤에서 숫자 형들에 관해 더 자세히 살펴볼 예정입니다.

나눗셈(`/`)은 항상 float를 반환합니다. 정수 결과를 얻기 위해 바닥 나눗셈을 하려면 `//` 연산자를 사용할 수 있습니다; 나머지를 계산하려면 `%`를 사용할 수 있습니다:

```python
>>> 17 / 3  # classic division returns a float
5.666666666666667
>>>
>>> 17 // 3  # floor division discards the fractional part
5
>>> 17 % 3  # the % operator returns the remainder of the division
2
>>> 5 * 3 + 2  # floored quotient * divisor + remainder
17
```

파이썬에서는 거듭제곱을 계산할 때 `**` 연산자를 사용합니다 [1]:

```python
>>> 5 ** 2  # 5 squared
25
>>> 2 ** 7  # 2 to the power of 7
128
```

변수에 값을 대입할 때는 등호(`=`)를 사용합니다. 이 경우 다음 대화형 프롬프트 전에 표시되는 출력은 없습니다:

```python
>>> width = 20
>>> height = 5 * 9
>>> width * height
900
```

변수가 "정의되어" 있지 않을 때 (값을 대입하지 않았을 때) 사용하려고 시도하는 것은 에러를 일으킵니다:

```python
>>> n  # try to access an undefined variable
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'n' is not defined
```

실수를 본격적으로 지원합니다; 서로 다른 형의 피연산자를 갖는 연산자는 정수 피연산자를 실수로 변환합니다:

```python
>>> 4 * 3.75 - 1
14.0
```

대화형 모드에서는, 마지막에 인쇄된 표현식은 변수 `_` 에 대입됩니다. 이것은 파이썬을 탁상용 계산기로 사용할 때, 계산을 이어 가기가 좀 더 쉬워짐을 의미합니다. 예를 들어:

```python
>>> tax = 12.5 / 100
>>> price = 100.50
>>> price * tax
12.5625
>>> price + _
113.0625
>>> round(_, 2)
113.06
```

이 변수는 사용자로서는 읽기만 가능한 것처럼 취급되어야 합니다. 값을 직접 대입하지 마세요 — 만약 그렇게 한다면 같은 이름의 지역 변수를 새로 만드는 것이 되는데, 내장 변수의 마술 같은 동작을 차단하는 결과를 낳습니다.

`int`와 `float`에 더해, 파이썬은 `Decimal`이나 `Fraction` 등의 다른 형의 숫자들도 지원합니다. 파이썬은 복소수에 대한 지원도 내장하고 있는데, 허수부를 가리키는데 `j` 나 `J` 접미사를 사용합니다 (예를 들어 `3+5j`).

### 논리 (Boolean)

```python
b = True
print(b) # 결과: True
```

참과 거짓을 표현하는 데이터 타입, `bool` 로 나타냅니다. 위 연산자의 결과에 따라 `True` 와 `False` 로 반환합니다. `is`, `is not` 의 경우 단순히 값이 같은 것 뿐만 아니라 같은 메모리 주소에 할당된 같은 객체인지 확인합니다.

> [!NOTE] 비교 연산자
>
> | 비교 연산자  | 의미                           |
> | ------------ | ------------------------------ |
> | `x > y`      | x 가 y 보다 큰지 여부          |
> | `x >= y`     | x 가 y 보다 크거나 같은지 여부 |
> | `x < y`      | x 가 y 보다 작은지 여부        |
> | `x <= y`     | x 가 y 보다 작거나 같은지 여부 |
> | `x == y`     | x 와 y 가 같은지 여부          |
> | `x!= y`      | x 와 y 가 다른지 여부          |
> | `x is y`     | x 와 y 가 같은 객체인지 여부   |
> | `x is not y` | x 와 y 가 다른 객체인지 여부   |

> [!NOTE] 논리 연산자
>
> | 논리 연산자  | 의미                                                       |
> | ------------ | ---------------------------------------------------------- |
> | `x or y`     | x 또는 y 가 `True` 인가?                                   |
> | `x and y`    | x 와 y 모두 `True` 인가?                                   |
> | `not x`      | x 가 `True` 라면 `False`, `False` 라면 `True` 를 반환한다. |
> | `x in A`     | x 가 A 의 요소로 있으면 참                                 |
> | `x not in A` | x 가 A 의 요소가 아니면 참                                 |

### 텍스트 (Text)

파이썬은 숫자뿐만 아니라 텍스트(타입 `str`로 표현됨, 소위 "문자열"이라고 함)도 조작할 수 있습니다. 여기에는 문자 "`!`", 단어 "`rabbit`", 이름 "`Paris`", 문장 "`Got your back.`" 등이 포함됩니다. "`Yay! :)`". 이들은 작은따옴표(`'…'`)나 큰따옴표(`"…"`)로 둘러싸여 같은 결과를 낼 수 있습니다 [2].

```python
>>> 'spam eggs'  # single quotes
'spam eggs'
>>> "Paris rabbit got your back :)! Yay!"  # double quotes
'Paris rabbit got your back :)! Yay!'
>>> '1975'  # digits and numerals enclosed in quotes are also strings
'1975'
```

따옴표를 인용하려면 `\`로 "이스케이프"해야 합니다. 또는 다른 유형의 따옴표를 사용할 수 있습니다:

```python
>>> 'doesn\'t'  # use \' to escape the single quote…
"doesn't"
>>> "doesn't"  # …or use double quotes instead
"doesn't"
>>> '"Yes," they said.'
'"Yes," they said.'
>>> "\"Yes,\" they said."
'"Yes," they said.'
>>> '"Isn\'t," they said.'
'"Isn\'t," they said.'
```

파이썬 셸에서 문자열 정의와 출력 문자열은 다르게 보일 수 있습니다. `print()` 함수는 둘러싸는 따옴표를 생략하고 이스케이프된 특수 문자를 출력하여 더 읽기 쉬운 출력을 생성합니다:

```python
>>> s = 'First line.\nSecond line.'  # \n means newline
>>> s  # without print(), special characters are included in the string
'First line.\nSecond line.'
>>> print(s)  # with print(), special characters are interpreted, so \n produces new line
First line.
Second line.
```

`\` 뒤에 나오는 문자가 특수 문자로 취급되게 하고 싶지 않다면, 첫 따옴표 앞에 `r` 을 붙여서 날 문자열 (raw string)을 만들 수 있습니다:

```python
>>> print('C:\some\name')  # here \n means newline!
C:\some
ame
>>> print(r'C:\some\name')  # note the r before the quote
C:\some\name
```

날 문자열에는 한 가지 미묘한 측면이 있습니다: 날 문자열은 홀수 개의 `\` 문자로 끝날 수 없습니다; 자세한 정보와 해결 방법은 FAQ 항목을 참조하십시오.

문자열 리터럴은 여러 줄에 걸쳐 있을 수 있습니다. 한 가지 방법은 삼중 따옴표를 사용하는 것입니다: `"""…"""` 또는 `'''…'''`. 줄 끝 문자는 자동으로 문자열에 포함되지만, 줄 끝에 `\`를 추가하여 이를 방지할 수 있습니다. 다음 예에서는 초기 줄바꿈이 포함되지 않습니다:

```python
>>> print("""\
… Usage: thingy [OPTIONS]
…      -h                        Display this usage message
…      -H hostname               Hostname to connect to
… """)
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
```

문자열은 `+` 연산자로 이어붙이고, `*` 연산자로 반복시킬 수 있습니다:

```python
>>> # 3 times 'un', followed by 'ium'
>>> 3 * 'un' + 'ium'
'unununium'
```

두 개 이상의 문자열 리터럴(즉, 따옴표로 둘러싸인 것들)가 연속해서 나타나면 자동으로 이어 붙여집니다.

```python
>>> 'Py' 'thon'
'Python'
```

이 기능은 긴 문자열을 쪼개고자 할 때 특별히 쓸모 있습니다:

```python
>>> text = ('Put several strings within parentheses '
…         'to have them joined together.')
>>> text
'Put several strings within parentheses to have them joined together.'
```

이것은 오직 두 개의 리터럴에만 적용될 뿐 변수나 표현식에는 해당하지 않습니다:

```python
>>> prefix = 'Py'
>>> prefix 'thon'  # can't concatenate a variable and a string literal
  File "<stdin>", line 1
    prefix 'thon'
           ^^^^^^
SyntaxError: invalid syntax
>>> ('un' * 3) 'ium'
  File "<stdin>", line 1
    ('un' * 3) 'ium'
               ^^^^^
SyntaxError: invalid syntax
```

변수들끼리 혹은 변수와 문자열 리터럴을 이어붙이려면 `+` 를 사용해야 합니다

```python
>>> prefix + 'thon'
'Python'
```

문자열은 인덱스(서브 스크립트)될 수 있습니다. 첫 번째 문자가 인덱스 0에 대응됩니다. 문자를 위한 별도의 형은 없습니다; 단순히 길이가 1인 문자열입니다:

```python
>>> word = 'Python'
>>> word[0]  # character in position 0
'P'
>>> word[5]  # character in position 5
'n'
```

인덱스는 음수가 될 수도 있는데, 끝에서부터 셉니다:

```python
>>> word[-1]  # last character
'n'
>>> word[-2]  # second-last character
'o'
>>> word[-6]
'P'
```

-0은 0과 같으므로, 음의 인덱스는 -1에서 시작한다는 것에 주목하세요.

인덱싱 외에도, 슬라이싱도 지원됩니다. 인덱싱이 개별 문자를 얻는 데 사용되는 반면, 슬라이싱은 부분 문자열을 얻을 수 있게 해줍니다:

```python
>>> word[0:2]  # characters from position 0 (included) to 2 (excluded)
'Py'
>>> word[2:5]  # characters from position 2 (included) to 5 (excluded)
'tho'
```

슬라이스 인덱스는 편리한 기본값을 갖고 있습니다; 첫 번째 인덱스를 생략하면 기본값 0 이 사용되고, 두 번째 인덱스가 생략되면 기본값으로 슬라이싱 되는 문자열의 길이가 사용됩니다.

```python
>>> word[:2]   # character from the beginning to position 2 (excluded)
'Py'
>>> word[4:]   # characters from position 4 (included) to the end
'on'
>>> word[-2:]  # characters from the second-last (included) to the end
'on'
```

시작 위치의 문자는 항상 포함되는 반면, 종료 위치의 문자는 항상 포함되지 않는 것에 주의하세요. 이 때문에 `s[:i] + s[i:]` 는 항상 `s` 와 같아집니다

```python
>>> word[:2] + word[2:]
'Python'
>>> word[:4] + word[4:]
'Python'
```

슬라이스가 동작하는 방법을 기억하는 한 가지 방법은 인덱스가 문자들 사이의 위치를 가리킨다고 생각하는 것입니다. 첫 번째 문자의 왼쪽 경계가 0입니다. n 개의 문자들로 구성된 문자열의 오른쪽 끝 경계는 인덱스 n 이 됩니다, 예를 들어:

```python
+---+---+---+---+---+---+ | P | y | t | h | o | n | +---+---+---+---+---+---+ 0 1 2 3 4 5 6 -6 -5 -4 -3 -2 -1
```

첫 번째 숫자 행은 인덱스 0…6 의 위치를 보여주고; 두 번째 행은 대응하는 음의 인덱스들을 보여줍니다. i 에서 j 범위의 슬라이스는 i 와 j 로 번호 붙여진 경계 사이의 문자들로 구성됩니다.

음이 아닌 인덱스들의 경우, 두 인덱스 모두 범위 내에 있다면 슬라이스의 길이는 인덱스 간의 차입니다. 예를 들어 `word[1:3]` 의 길이는 2입니다.

너무 큰 값을 인덱스로 사용하는 것은 에러입니다:

```python
>>> word[42]  # the word only has 6 characters
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
```

하지만, 범위를 벗어나는 슬라이스 인덱스는 슬라이싱할 때 부드럽게 처리됩니다:

```python
>>> word[4:42]
'on'
>>> word[42:]
''
```

파이썬 문자열은 변경할 수 없다 — 불변이라고 합니다. 그래서 문자열의 인덱스로 참조한 위치에 대입하려고 하면 에러를 일으킵니다:

```python
>>> word[0] = 'J'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>> word[2:] = 'py'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```

다른 문자열이 필요하면, 새로 만들어야 합니다:

```python
>>> 'J' + word[1:]
'Jython'
>>> word[:2] + 'py'
'Pypy'
```

내장 함수 `len()` 은 문자열의 길이를 돌려줍니다:

```python
>>> s = 'supercalifragilisticexpialidocious'
>>> len(s)
34
```

> [!seealso] > [텍스트 시퀀스 형 — str](https://docs.python.org/ko/3.13/library/stdtypes.html#textseq)
>
> 문자열은 *시퀀스 형* 의 일종이고, 시퀀스가 지원하는 공통 연산들이 지원됩니다.
>
> [문자열 메서드](https://docs.python.org/ko/3.13/library/stdtypes.html#string-methods)
>
> 문자열은 기본적인 변환과 검색을 위한 여러 가지 메서드들을 지원합니다.
>
> [f-strings](https://docs.python.org/ko/3.13/reference/lexical_analysis.html#f-strings)
>
> 내장된 표현식을 갖는 문자열 리터럴
>
> [포맷 문자열 문법](https://docs.python.org/ko/3.13/library/string.html#formatstrings)
>
> [`str.format()`](https://docs.python.org/ko/3.13/library/stdtypes.html#str.format "str.format") 으로 문자열을 포맷하는 방법에 대한 정보.
>
> [printf 스타일 문자열 포매팅](https://docs.python.org/ko/3.13/library/stdtypes.html#old-string-formatting)
>
> 이곳에서 문자열을 `%` 연산자 왼쪽에 사용하는 예전 방식의 포매팅에 관해 좀 더 상세하게 설명하고 있습니다.

### 리스트 (List)

파이썬은 다른 값들을 덩어리로 묶는데 사용되는 여러 가지 컴파운드 (compound) 자료 형을 알고 있습니다. 가장 융통성이 있는 것은 리스트인데, 대괄호 사이에 쉼표로 구분된 값(항목)들의 목록으로 표현될 수 있습니다. 리스트는 서로 다른 형의 항목들을 포함할 수 있지만, 항목들이 모두 같은 형인 경우가 많습니다.

```python
>>> squares = [1, 4, 9, 16, 25]
>>> squares
[1, 4, 9, 16, 25]
```

문자열(그리고, 다른 모든 내장 시퀀스 형들)처럼 리스트는 인덱싱하고 슬라이싱할 수 있습니다:

```python
>>> squares[0]  # indexing returns the item
1
>>> squares[-1]
25
>>> squares[-3:]  # slicing returns a new list
[9, 16, 25]
```

리스트는 이어붙이기 같은 연산도 지원합니다:

```python
>>> squares + [36, 49, 64, 81, 100]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

불변인 문자열과는 달리, 리스트는 가변입니다. 즉 내용을 변경할 수 있습니다:

```python
>>> cubes = [1, 8, 27, 65, 125]  # something's wrong here
>>> 4 ** 3  # the cube of 4 is 64, not 65!
64
>>> cubes[3] = 64  # replace the wrong value
>>> cubes
[1, 8, 27, 64, 125]
```

> [!NOTE] 리스트 메서드
>
> | 메서드         | 설명                                                                                                                               |
> | -------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
> | `append(x)`    | 리스트 마지막 요소 뒤에 값 `x` 추가                                                                                                |
> | `clear()`      | 리스트 모든 항목 삭제                                                                                                              |
> | `copy()`       | 리스트 복사                                                                                                                        |
> | `count(x)`     | 리스트의 `x` 항목 개수 반환                                                                                                        |
> | `extend(i)`    | 리스트 마지막에 컬렉션 자료형 `i`를 추가                                                                                           |
> | `index(x)`     | `x` 항목의 인덱스 번호 반환<br>같은 값이 여러 개 있을 경우 가장 앞에 있는 인덱스 반환<br>값이 존재하지 않을 경우 `ValueError` 발생 |
> | `insert(i, x)` | 리스트의 `i` 인덱스에 `x` 삽입                                                                                                     |
> | `pop()`        | 리스트 마지막 항목을 삭제하고 반환                                                                                                 |
> | `remove(x)`    | 리스트에서 `x`를 제거<br>같은 값이 여러 개 있을 경우 가장 앞에 있는 값 제거                                                        |
> | `reverse()`    | 리스트의 위치를 역순으로 변경                                                                                                      |
> | `sort()`       | 리스트 항목들을 정렬                                                                                                               |

리스트 메서드 `list.append()`를 사용하여 리스트 끝에 새 항목을 추가할 수도 있습니다(메서드에 대해서는 나중에 더 알아보겠습니다):

```python
>>> cubes.append(216)  # add the cube of 6
>>> cubes.append(7 ** 3)  # and the cube of 7
>>> cubes
[1, 8, 27, 64, 125, 216, 343]
```

파이썬에서 단순 할당은 데이터를 복사하지 않습니다. 변수에 리스트를 할당할 때, 변수는 기존 리스트를 참조합니다. 한 변수를 통해 리스트를 변경하면 해당 리스트를 참조하는 모든 다른 변수에서도 변경 사항이 보입니다:

```python
>>> rgb = ["Red", "Green", "Blue"]
>>> rgba = rgb
>>> id(rgb) == id(rgba)  # they reference the same object
True
>>> rgba.append("Alph")
>>> rgb
["Red", "Green", "Blue", "Alph"]
```

모든 슬라이스 연산은 요청한 항목들을 포함하는 새 리스트를 돌려줍니다. 이는 다음과 같은 슬라이스가 리스트의 새로운 얕은 복사본을 돌려준다는 뜻입니다:

```python
>>> correct_rgba = rgba[:]
>>> correct_rgba[-1] = "Alpha"
>>> correct_rgba
["Red", "Green", "Blue", "Alpha"]
>>> rgba
["Red", "Green", "Blue", "Alph"]
```

슬라이스에 대입하는 것도 가능한데, 리스트의 길이를 변경할 수 있고, 모든 항목을 삭제할 수조차 있습니다:

```python
>>> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> # replace some values
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['a', 'b', 'C', 'D', 'E', 'f', 'g']
>>> # now remove them
>>> letters[2:5] = []
>>> letters
['a', 'b', 'f', 'g']
>>> # clear the list by replacing all the elements with an empty list
>>> letters[:] = []
>>> letters
[]
```

내장 함수 `len()` 은 리스트에도 적용됩니다:

```python
>>> letters = ['a', 'b', 'c', 'd']
>>> len(letters)
4
```

리스트를 중첩할 수도 있습니다. (다른 리스트를 포함하는 리스트를 만듭니다). 예를 들어:

```python
>>> a = ['a', 'b', 'c']
>>> n = [1, 2, 3]
>>> x = [a, n]
>>> x
[['a', 'b', 'c'], [1, 2, 3]]
>>> x[0]
['a', 'b', 'c']
>>> x[0][1]
'b'
```

## 파이썬 코드 규칙

```python
# 이 문장은 주석입니다.
```

`#` 기호를 사용하여 주석을 작성할 수 있습니다.

물론, 파이썬으로 2와 2를 더하는 것보다 더 복잡한 작업을 수행할 수 있습니다. 예를 들어, 다음과 같이 피보나치 수열의 초기 부분 수열을 작성할 수 있습니다:

```python
>>> # Fibonacci series:
>>> # the sum of two elements defines the next
>>> a, b = 0, 1
>>> while a < 10:
…     print(a)
…     a, b = b, a+b
…
0
1
1
2
3
5
8
```

이 예는 몇 가지 새로운 기능을 소개하고 있습니다.

- 첫 줄은 다중 대입을 포함하고 있습니다: 변수 `a` 와 `b` 에 동시에 값 0과1이 대입됩니다. 마지막 줄에서 다시 사용되는데, 대입이 어느 하나라도 이루어지기 전에 우변의 표현식들이 모두 계산됩니다. 우변의 표현식은 왼쪽부터 오른쪽으로 가면서 순서대로 계산됩니다.
- `while` 루프는 조건(여기서는: `a < 10`)이 참인 동안 실행됩니다. C와 마찬가지로 파이썬에서 0 이 아닌 모든 정수는 참이고, 0은 거짓입니다. 조건은 문자열이나 리스트 (사실 모든 종류의 시퀀스)가 될 수도 있는데 길이가 0 이 아닌 것은 모두 참이고, 빈 시퀀스는 거짓입니다. 이 예에서 사용한 검사는 간단한 비교입니다. 표준 비교 연산자는 C와 같은 방식으로 표현됩니다: `<` (작다), `>` (크다), `==` (같다), `<=` (작거나 같다), `>=` (크거나 같다), `!=` (다르다).
- 루프의 바디 (body)는 들여쓰기 됩니다. 들여쓰기는 파이썬에서 문장을 덩어리로 묶는 방법입니다. 대화형 프롬프트에서 각각 들여 쓰는 줄에서 탭(tab)이나 공백(space)을 입력해야 합니다. 실제적으로는 텍스트 편집기를 사용해서 좀 더 복잡한 파이썬 코드를 준비하게 됩니다; 웬만한 텍스트 편집기들은 자동 들여쓰기 기능을 제공합니다. 복합문을 대화형으로 입력할 때는 끝을 알리기 위해 빈 줄을 입력해야 합니다. (해석기가 언제 마지막 줄을 입력할지 짐작할 수 없기 때문입니다.) 같은 블록에 포함되는 모든 줄은 같은 양만큼 들여쓰기 되어야 함에 주의하세요.

- `print()` 함수는 주어진 인자(들)의 값을 출력합니다. 이는 단순히 원하는 표현식을 쓰는 것(계산기 예제에서 했던 것처럼)과는 다른데, 여러 인자, 부동소수점 수량, 문자열을 처리하는 방식에서 차이가 있습니다. 문자열은 따옴표 없이 출력되며, 항목 사이에 공백이 삽입되므로 다음과 같이 멋지게 형식을 지정할 수 있습니다:

```python
>>> i = 256*256
>>> print('The value of i is', i)
The value of i is 65536
```

키워드 인자 end는 출력 끝에 포함되는 개행문자를 제거하거나 출력을 다른 문자열로 끝나게 하고 싶을 때 사용됩니다:

```python
>>> a, b = 0, 1
>>> while a < 1000:
…     print(a, end=',')
…     a, b = b, a+b
…
0,1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987,
```

> **출처**
>
> - [파이썬의 간략한 소개 — Python 3.13.3 문서](https://docs.python.org/ko/3.13/tutorial/introduction.html)
