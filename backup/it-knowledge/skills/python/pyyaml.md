---
title: PyYAML
description: PyYAML은 Python을 위한 YAML 파서와 이미터입니다.
date: 2025-04-23 21:00:00+0900
lastmod: 2025-04-23
slug: pyyaml
comments: true
math: false
categories:
  - PyYAML
tags:
  - Python
  - YAML
keywords:
  - PyYAML
  - Python
  - YAML
---

## 설치 (Installation)

간단한 설치:

```
pip install pyyaml
```

소스에서 설치하려면, 소스 패키지 PyYAML-5.1.tar.gz를 다운로드하고 압축을 풉니다. PyYAML-5.1 디렉토리로 이동하여 다음을 실행합니다:

```
$ python setup.py install
```

순수 Python 버전보다 훨씬 빠른 LibYAML 바인딩을 사용하고 싶다면, [LibYAML](https://pyyaml.org/wiki/LibYAML)을 다운로드하고 설치해야 합니다. 그런 다음 다음을 실행하여 바인딩을 빌드하고 설치할 수 있습니다

```
$ python setup.py --with-libyaml install
```

[LibYAML](https://pyyaml.org/wiki/LibYAML) 기반 파서와 이미터를 사용하려면, `CParser`와 `CEmitter` 클래스를 사용하십시오. 예를 들어,

```python
from yaml import load, dump
try:
    from yaml import CLoader as Loader, CDumper as Dumper
except ImportError:
    from yaml import Loader, Dumper

# ...

data = load(stream, Loader=Loader)

# ...

output = dump(data, Dumper=Dumper)
```

순수 Python과 [LibYAML](https://pyyaml.org/wiki/LibYAML) 기반 파서 및 이미터 간에는 미묘한(그러나 그다지 중요하지 않은) 차이가 있다는 점에 유의하십시오.

## 파일 입출력 (File I/O)

`yaml` 패키지를 임포트하는 것으로 시작하세요.

```python
>>> import yaml
```

### YAML 로딩 (Loading YAML)

**경고**: 신뢰할 수 없는 소스에서 받은 데이터로 `yaml.load`를 호출하는 것은 안전하지 않습니다! `yaml.load`는 `pickle.load`만큼 강력하므로 모든 Python 함수를 호출할 수 있습니다. 그러나 `yaml.safe_load` 함수를 확인해보세요.

`yaml.load` 함수는 YAML 문서를 Python 객체로 변환합니다.

```python
>>> yaml.load("""
... - Hesperiidae
... - Papilionidae
... - Apatelodidae
... - Epiplemidae
... """)

['Hesperiidae', 'Papilionidae', 'Apatelodidae', 'Epiplemidae']
```

`yaml.load`는 바이트 문자열, 유니코드 문자열, 열려있는 바이너리 파일 객체 또는 열려있는 텍스트 파일 객체를 허용합니다. 바이트 문자열이나 파일은 utf-8, utf-16-be 또는 utf-16-le 인코딩으로 인코딩되어야 합니다. `yaml.load`는 문자열/파일 시작 부분의 BOM(바이트 순서 표시) 시퀀스를 확인하여 인코딩을 감지합니다. BOM이 없으면 utf-8 인코딩이 가정됩니다.

`yaml.load`는 Python 객체를 반환합니다.

```python
>>> yaml.load(u"""
... hello: Привет!
... """)    # Python 3에서는 'u' 접두사를 사용하지 마세요

{'hello': u'\u041f\u0440\u0438\u0432\u0435\u0442!'}

>>> stream = file('document.yaml', 'r')    # 'document.yaml'는 단일 YAML 문서를 포함합니다.
>>> yaml.load(stream)
[...]    # 문서에 해당하는 Python 객체.
```

문자열이나 파일에 여러 문서가 포함되어 있다면, `yaml.load_all` 함수로 모두 로드할 수 있습니다.

```python
>>> documents = """
... ---
... name: The Set of Gauntlets 'Pauraegen'
... description: >
...     A set of handgear with sparks that crackle
...     across its knuckleguards.
... ---
... name: The Set of Gauntlets 'Paurnen'
... description: >
...   A set of gauntlets that gives off a foul,
...   acrid odour yet remains untarnished.
... ---
... name: The Set of Gauntlets 'Paurnimmen'
... description: >
...   A set of handgear, freezing with unnatural cold.
... """

>>> for data in yaml.load_all(documents):
...     print data

{'description': 'A set of handgear with sparks that crackle across its knuckleguards.\n',
'name': "The Set of Gauntlets 'Pauraegen'"}
{'description': 'A set of gauntlets that gives off a foul, acrid odour yet remains untarnished.\n',
'name': "The Set of Gauntlets 'Paurnen'"}
{'description': 'A set of handgear, freezing with unnatural cold.\n',
'name': "The Set of Gauntlets 'Paurnimmen'"}
```

PyYAML을 사용하면 모든 유형의 Python 객체를 구성할 수 있습니다.

```python
>>> yaml.load("""
... none: [~, null]
... bool: [true, false, on, off]
... int: 42
... float: 3.14159
... list: [LITE, RES_ACID, SUS_DEXT]
... dict: {hp: 13, sp: 5}
... """)

{'none': [None, None], 'int': 42, 'float': 3.1415899999999999,
'list': ['LITE', 'RES_ACID', 'SUS_DEXT'], 'dict': {'hp': 13, 'sp': 5},
'bool': [True, False, True, False]}
```

`!!python/object` 태그를 사용하여 Python 클래스의 인스턴스도 구성할 수 있습니다.

```python
>>> class Hero:
...     def __init__(self, name, hp, sp):
...         self.name = name
...         self.hp = hp
...         self.sp = sp
...     def __repr__(self):
...         return "%s(name=%r, hp=%r, sp=%r)" % (
...             self.__class__.__name__, self.name, self.hp, self.sp)

>>> yaml.load("""
... !!python/object:__main__.Hero
... name: Welthyr Syxgon
... hp: 1200
... sp: 0
... """)

Hero(name='Welthyr Syxgon', hp=1200, sp=0)
```

임의의 Python 객체를 구성하는 능력은 인터넷과 같은 신뢰할 수 없는 소스에서 YAML 문서를 받는 경우 위험할 수 있다는 점에 유의하세요. `yaml.safe_load` 함수는 이 능력을 정수나 리스트와 같은 간단한 Python 객체로 제한합니다.

Python 객체는 안전한 것으로 표시되어 `yaml.safe_load`에 의해 인식될 수 있습니다. 이렇게 하려면, `yaml.YAMLObject`에서 파생시키고(Constructor, representers, resolvers 섹션에 설명된 대로) 클래스 속성 `yaml_loader`를 명시적으로 `yaml.SafeLoader`로 설정하세요.

### YAML 덤핑 (Dumping YAML)

`yaml.dump` 함수는 Python 객체를 받아들이고 YAML 문서를 생성합니다.

```python
>>> print yaml.dump({'name': 'Silenthand Olleander', 'race': 'Human',
... 'traits': ['ONE_HAND', 'ONE_EYE']})

name: Silenthand Olleander
race: Human
traits: [ONE_HAND, ONE_EYE]
```

`yaml.dump`는 열려있는 텍스트 또는 바이너리 파일이어야 하는 두 번째 선택적 인수를 받아들입니다. 이 경우, `yaml.dump`는 생성된 YAML 문서를 파일에 쓸 것입니다. 그렇지 않으면, `yaml.dump`는 생성된 문서를 반환합니다.

```python
>>> stream = file('document.yaml', 'w')
>>> yaml.dump(data, stream)    # YAML 표현을 'document.yaml'에 씁니다.
>>> print yaml.dump(data)      # 문서를 화면에 출력합니다.
```

단일 스트림에 여러 YAML 문서를 덤프해야 한다면, `yaml.dump_all` 함수를 사용하세요. `yaml.dump_all`은 YAML 문서로 직렬화될 Python 객체를 생성하는 리스트나 제너레이터를 받아들입니다. 두 번째 선택적 인수는 열려있는 파일입니다.

```python
>>> print yaml.dump([1,2,3], explicit_start=True)
--- [1, 2, 3]

>>> print yaml.dump_all([1,2,3], explicit_start=True)
--- 1
--- 2
--- 3
```

Python 클래스의 인스턴스도 덤프할 수 있습니다.

```python
>>> class Hero:
...     def __init__(self, name, hp, sp):
...         self.name = name
...         self.hp = hp
...         self.sp = sp
...     def __repr__(self):
...         return "%s(name=%r, hp=%r, sp=%r)" % (
...             self.__class__.__name__, self.name, self.hp, self.sp)

>>> print yaml.dump(Hero("Galain Ysseleg", hp=-3, sp=2))

!!python/object:__main__.Hero {hp: -3, name: Galain Ysseleg, sp: 2}
```

`yaml.dump`는 이미터의 형식 세부 정보를 지정하는 여러 키워드 인자를 지원합니다. 예를 들어, 들여쓰기와 너비를 설정하거나, 표준 YAML 형식을 사용하거나, 스칼라와 컬렉션에 대한 선호 스타일을 강제할 수 있습니다.

```python
>>> print yaml.dump(range(50))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22,
  23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42,
  43, 44, 45, 46, 47, 48, 49]

>>> print yaml.dump(range(50), width=50, indent=4)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15,
    16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27,
    28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39,
    40, 41, 42, 43, 44, 45, 46, 47, 48, 49]

>>> print yaml.dump(range(5), canonical=True)
---
!!seq [
  !!int "0",
  !!int "1",
  !!int "2",
  !!int "3",
  !!int "4",
]

>>> print yaml.dump(range(5), default_flow_style=False)
- 0
- 1
- 2
- 3
- 4

>>> print yaml.dump(range(5), default_flow_style=True, default_style='"')
[!!int "0", !!int "1", !!int "2", !!int "3", !!int "4"]
```

### 생성자, 표현자, 리졸버 (Constructors, representers, resolvers)

애플리케이션별 태그를 정의할 수 있습니다. 가장 쉬운 방법은 `yaml.YAMLObject`의 하위 클래스를 정의하는 것입니다:

```python
>>> class Monster(yaml.YAMLObject):
...     yaml_tag = u'!Monster'
...     def __init__(self, name, hp, ac, attacks):
...         self.name = name
...         self.hp = hp
...         self.ac = ac
...         self.attacks = attacks
...     def __repr__(self):
...         return "%s(name=%r, hp=%r, ac=%r, attacks=%r)" % (
...             self.__class__.__name__, self.name, self.hp, self.ac, self.attacks)
```

위의 정의만으로도 `Monster` 객체를 자동으로 로드하고 덤프할 수 있습니다:

```python
>>> yaml.load("""
... --- !Monster
... name: Cave spider
... hp: [2,6]    # 2d6
... ac: 16
... attacks: [BITE, HURT]
... """)

Monster(name='Cave spider', hp=[2, 6], ac=16, attacks=['BITE', 'HURT'])

>>> print yaml.dump(Monster(
...     name='Cave lizard', hp=[3,6], ac=16, attacks=['BITE','HURT']))

!Monster
ac: 16
attacks: [BITE, HURT]
hp: [3, 6]
name: Cave lizard
```

`yaml.YAMLObject`는 메타클래스 매직을 사용하여 YAML 노드를 클래스 인스턴스로 변환하는 생성자와 클래스 인스턴스를 YAML 노드로 직렬화하는 표현자를 등록합니다.

메타클래스를 사용하고 싶지 않다면 `yaml.add_constructor`와 `yaml.add_representer` 함수를 사용하여 생성자와 표현자를 등록할 수 있습니다. 예를 들어, 다음 `Dice` 클래스에 대한 생성자와 표현자를 추가할 수 있습니다:

```python
>>> class Dice(tuple):
...     def __new__(cls, a, b):
...         return tuple.__new__(cls, [a, b])
...     def __repr__(self):
...         return "Dice(%s,%s)" % self

>>> print Dice(3,6)
Dice(3,6)
```

`Dice` 객체에 대한 기본 표현은 좋지 않습니다:

```python
>>> print yaml.dump(Dice(3,6))

!!python/object/new:__main__.Dice
- !!python/tuple [3, 6]
```

`Dice` 객체를 YAML에서 `AdB`로 표현하고 싶다면:

```python
>>> print yaml.dump(Dice(3,6))

3d6
```

먼저 주사위 객체를 `!dice` 태그가 있는 스칼라 노드로 변환하는 표현자를 정의한 다음 등록합니다.

```python
>>> def dice_representer(dumper, data):
...     return dumper.represent_scalar(u'!dice', u'%sd%s' % data)

>>> yaml.add_representer(Dice, dice_representer)
```

이제 `Dice` 객체의 인스턴스를 덤프할 수 있습니다:

```python
>>> print yaml.dump({'gold': Dice(10,6)})
{gold: !dice '10d6'}
```

Dice 객체를 생성하는 코드를 추가해 봅시다:

```python
>>> def dice_constructor(loader, node):
...     value = loader.construct_scalar(node)
...     a, b = map(int, value.split('d'))
...     return Dice(a, b)

>>> yaml.add_constructor(u'!dice', dice_constructor)
```

이제 `Dice` 객체도 로드할 수 있습니다:

```python
>>> print yaml.load("""
... initial hit points: !dice 8d4
... """)

{'initial hit points': Dice(8,4)}
```

어디에서나 `!dice` 태그를 지정하고 싶지 않을 수 있습니다. XdY처럼 보이는 태그가 없는 일반 스칼라에는 암시적 태그 `!dice`가 있다고 PyYAML에 알려주는 방법이 있습니다. `add_implicit_resolver`를 사용하세요:

```python
>>> import re
>>> pattern = re.compile(r'^\d+d\d+$')
>>> yaml.add_implicit_resolver(u'!dice', pattern)
```

이제 `Dice` 객체를 정의하기 위해 태그를 지정할 필요가 없습니다:

```python
>>> print yaml.dump({'treasure': Dice(10,20)})

{treasure: 10d20}

>>> print yaml.load("""
... damage: 5d10
... """)

{'damage': Dice(5,10)}
```

## YAML 구문 (YAML syntax)

YAML 구문에 대한 좋은 소개는 [YAML 사양의 2장](http://yaml.org/spec/1.1/#id857168)입니다.

[YAML 요리책](https://yaml.org/YAML_for_ruby.html)도 확인할 수 있습니다. 이는 Ruby 구현에 중점을 두고 있으며 오래된 YAML 1.0 구문을 사용합니다.

여기서는 해당하는 Python 객체와 함께 가장 일반적인 YAML 구성을 소개합니다.

### 문서 (Documents)

YAML 스트림은 0개 이상의 문서 모음입니다. 빈 스트림에는 문서가 없습니다. 문서는 `---`로 구분됩니다. 문서는 선택적으로 `...`로 끝날 수 있습니다. 단일 문서는 `---`로 표시될 수도 있고 그렇지 않을 수도 있습니다.

암시적 문서의 예:

```
- Multimedia
- Internet
- Education
```

명시적 문서의 예:

```
---
- Afterstep
- CTWM
- Oroborus
...
```

동일한 스트림에 여러 문서가 있는 예:

```
---
- Ada
- APL
- ASP

- Assembly
- Awk
---
- Basic
---
- C
- C#    # 주석은 ' #'(공백 다음 #)으로 표시됩니다.
- C++
- Cold Fusion
```

### 블록 시퀀스 (Block sequences)

블록 컨텍스트에서 시퀀스 항목은 `-`(대시 다음 공백)로 표시됩니다:

```
# YAML
- The Dagger 'Narthanc'
- The Dagger 'Nimthanc'
- The Dagger 'Dethanc'
```

```
# Python
["The Dagger 'Narthanc'", "The Dagger 'Nimthanc'", "The Dagger 'Dethanc'"]
```

블록 시퀀스는 중첩될 수 있습니다:

```
# YAML
-
  - HTML
  - LaTeX
  - SGML
  - VRML
  - XML
  - YAML
-
  - BSD
  - GNU Hurd
  - Linux
```

```
# Python
[['HTML', 'LaTeX', 'SGML', 'VRML', 'XML', 'YAML'], ['BSD', 'GNU Hurd', 'Linux']]
```

중첩된 시퀀스를 새 줄로 시작할 필요는 없습니다:

```
# YAML
- 1.1
- - 2.1
  - 2.2
- - - 3.1
    - 3.2
    - 3.3
```

```
# Python
[1.1, [2.1, 2.2], [[3.1, 3.2, 3.3]]]
```

블록 시퀀스는 블록 매핑에 중첩될 수 있습니다. 이 경우 시퀀스를 들여쓰기할 필요가 없습니다.

```
# YAML
left hand:
- Ring of Teleportation
- Ring of Speed

right hand:
- Ring of Resist Fire
- Ring of Resist Cold
- Ring of Resist Poison
```

```
# Python
{'right hand': ['Ring of Resist Fire', 'Ring of Resist Cold', 'Ring of Resist Poison'],
'left hand': ['Ring of Teleportation', 'Ring of Speed']}
```

### 블록 매핑 (Block mappings)

블록 컨텍스트에서 매핑의 키와 값은 `:`(콜론 다음 공백)으로 구분됩니다:

```
# YAML
base armor class: 0
base damage: [4,4]
plus to-hit: 12
plus to-dam: 16
plus to-ac: 0
```

```
# Python
{'plus to-hit': 12, 'base damage': [4, 4], 'base armor class': 0, 'plus to-ac': 0, 'plus to-dam': 16}
```

복잡한 키는 `?`(물음표 다음 공백)로 표시됩니다:

```
# YAML
? !!python/tuple [0,0]
: The Hero
? !!python/tuple [0,1]
: Treasure
? !!python/tuple [1,0]
: Treasure
? !!python/tuple [1,1]
: The Dragon
```

```
# Python
{(0, 1): 'Treasure', (1, 0): 'Treasure', (0, 0): 'The Hero', (1, 1): 'The Dragon'}
```

블록 매핑은 중첩될 수 있습니다:

```
# YAML
hero:
  hp: 34
  sp: 8
  level: 4
orc:
  hp: 12
  sp: 0
  level: 2
```

```
# Python
{'hero': {'hp': 34, 'sp': 8, 'level': 4}, 'orc': {'hp': 12, 'sp': 0, 'level': 2}}
```

블록 매핑은 블록 시퀀스 내에 중첩될 수 있습니다:

```
# YAML
- name: PyYAML
  status: 4
  license: MIT
  language: Python
- name: PySyck
  status: 5
  license: BSD
  language: Python
```

```
# Python
[{'status': 4, 'language': 'Python', 'name': 'PyYAML', 'license': 'MIT'},
{'status': 5, 'license': 'BSD', 'name': 'PySyck', 'language': 'Python'}]
```

### 흐름 컬렉션 (Flow collections)

YAML의 흐름 컬렉션 구문은 Python의 리스트와 딕셔너리 생성자 구문과 매우 유사합니다:

```
# YAML
{ str: [15, 17], con: [16, 16], dex: [17, 18], wis: [16, 16], int: [10, 13], chr: [5, 8] }
```

```
# Python
{'dex': [17, 18], 'int': [10, 13], 'chr': [5, 8], 'wis': [16, 16], 'str': [15, 17], 'con': [16, 16]}
```

### 스칼라 (Scalars)

YAML에는 5가지 스타일의 스칼라가 있습니다: 일반(plain), 작은따옴표(single-quoted), 큰따옴표(double-quoted), 리터럴(literal), 접힘(folded):

```
# YAML
plain: Scroll of Remove Curse
single-quoted: 'EASY_KNOW'
double-quoted: "?"
literal: |    # Borrowed from http://www.kersbergen.com/flump/religion.html
  by hjw              ___
     __              /.-.\
    /  )_____________\\  Y
   /_ /=== == === === =\ _\_
  ( /)=== == === === == Y   \
   `-------------------(  o  )
                        \___/
folded: >
  It removes all ordinary curses from all equipped items.
  Heavy or permanent curses are unaffected.
```

```
# Python
{'plain': 'Scroll of Remove Curse',
'literal':
    'by hjw              ___\n'
    '   __              /.-.\\\n'
    '  /  )_____________\\\\  Y\n'
    ' /_ /=== == === === =\\ _\\_\n'
    '( /)=== == === === == Y   \\\n'
    ' `-------------------(  o  )\n'
    '                      \\___/\n',
'single-quoted': 'EASY_KNOW',
'double-quoted': '?',
'folded': 'It removes all ordinary curses from all equipped items. Heavy or permanent curses are unaffected.\n'}
```

각 스타일에는 고유한 특성이 있습니다. 일반 스칼라는 시작과 끝을 나타내는 표시자를 사용하지 않으므로 가장 제한적인 스타일입니다. 주로 속성과 매개변수 이름에 사용됩니다.

작은따옴표 스칼라를 사용하면 특수 문자가 포함되지 않은 모든 값을 표현할 수 있습니다. 작은따옴표 스칼라에서는 인접한 따옴표 쌍 `''`이 하나의 작은따옴표 `'`로 대체되는 것을 제외하고는 이스케이프가 발생하지 않습니다.

큰따옴표는 가장 강력한 스타일이며 모든 스칼라 값을 표현할 수 있는 유일한 스타일입니다. 큰따옴표 스칼라는 이스케이프를 허용합니다. 이스케이프 시퀀스 `\x*`와 `\u***`를 사용하여 모든 ASCII 또는 유니코드 문자를 표현할 수 있습니다.

블록 스칼라 스타일에는 리터럴과 접힘 두 가지 종류가 있습니다. 리터럴 스타일은 소스 코드와 같은 대용량 텍스트 블록에 가장 적합한 스타일입니다. 접힘 스타일은 리터럴 스타일과 유사하지만, 인접한 두 개의 비어 있지 않은 줄이 공백 문자로 구분된 단일 줄로 결합됩니다.

### 별칭 (Aliases)

YAML을 사용하면 임의의 그래프 같은 구조의 객체를 표현할 수 있습니다. 문서의 여러 부분에서 동일한 객체를 참조하려면 앵커와 별칭을 사용해야 합니다.

앵커는 `&` 표시기로 표시되고 별칭은 `*`로 표시됩니다. 예를 들어 다음 문서는

```
left hand: &A
  name: The Bastard Sword of Eowyn
  weight: 30
right hand: *A
```

영웅이 양손에 무거운 검을 들고 있다는 개념을 표현합니다.

PyYAML은 이제 재귀적 객체를 완전히 지원합니다. 예를 들어, 다음 문서는

```
&A [ *A ]
```

자기 자신에 대한 참조를 포함하는 리스트 객체를 생성합니다.

### 태그 (Tags)

태그는 YAML 노드의 유형을 나타내는 데 사용됩니다. 표준 YAML 태그는 [http://yaml.org/type/index.html](http://yaml.org/type/index.html)에 정의되어 있습니다.

태그는 암시적일 수 있습니다:

```
boolean: true
integer: 3
float: 3.14
```

```
{'boolean': True, 'integer': 3, 'float': 3.14}
```

또는 명시적일 수 있습니다:

```
boolean: !!bool "true"
integer: !!int "3"
float: !!float "3.14"
```

```
{'boolean': True, 'integer': 3, 'float': 3.14}
```

명시적으로 정의된 태그가 없는 일반 스칼라는 암시적 태그 해결의 대상이 됩니다. 스칼라 값은 정규 표현식 세트에 대해 확인되며, 일치하는 것이 있으면 해당 태그가 스칼라에 할당됩니다. PyYAML을 사용하면 애플리케이션에서 사용자 정의 암시적 태그 리졸버를 추가할 수 있습니다.

## YAML 태그와 Python 타입 (YAML tags and Python types)

다음 표는 다양한 태그를 가진 노드가 Python 객체로 변환되는 방법을 설명합니다.

**표준 YAML 태그**

| YAML 태그           | Python 타입                                 |
| ------------------- | ------------------------------------------- |
| `!!null`            | `None`                                      |
| `!!bool`            | `bool`                                      |
| `!!int`             | `int` 또는 `long` (Python 3에서는 `int`)    |
| `!!float`           | `float`                                     |
| `!!binary`          | `str` (Python 3에서는 `bytes`)              |
| `!!timestamp`       | `datetime.datetime`                         |
| `!!omap`, `!!pairs` | 쌍의 `list`                                 |
| `!!set`             | `set`                                       |
| `!!str`             | `str` 또는 `unicode` (Python 3에서는 `str`) |
| `!!seq`             | `list`                                      |
| `!!map`             | `dict`                                      |

**Python 전용 태그**

| YAML 태그          | Python 타입                      |
| ------------------ | -------------------------------- |
| `!!python/none`    | `None`                           |
| `!!python/bool`    | `bool`                           |
| `!!python/bytes`   | (Python 3에서는 `bytes`)         |
| `!!python/str`     | `str` (Python 3에서는 `str`)     |
| `!!python/unicode` | `unicode` (Python 3에서는 `str`) |
| `!!python/int`     | `int`                            |
| `!!python/long`    | `long` (Python 3에서는 `int`)    |
| `!!python/float`   | `float`                          |
| `!!python/complex` | `complex`                        |
| `!!python/list`    | `list`                           |
| `!!python/tuple`   | `tuple`                          |
| `!!python/dict`    | `dict`                           |

**복합 Python 태그**

| YAML 태그                        | Python 타입           |
| -------------------------------- | --------------------- |
| `!!python/name:module.name`      | `module.name`         |
| `!!python/module:package.module` | `package.module`      |
| `!!python/object:module.cls`     | `module.cls` 인스턴스 |
| `!!python/object/new:module.cls` | `module.cls` 인스턴스 |
| `!!python/object/apply:module.f` | `f(...)` 값           |

### 문자열 변환 (Python 2 전용) (String conversion (Python 2 only))

`str`과 `unicode` 값으로 변환되는 네 가지 태그가 있습니다: `!!str`, `!!binary`, `!!python/str`, `!!python/unicode`.

`!!str` 태그가 붙은 스칼라는 값이 ASCII인 경우 `str` 객체로 변환됩니다. 그렇지 않으면 `unicode`로 변환됩니다. `!!binary` 태그가 붙은 스칼라는 base64 인코딩을 사용하여 디코딩된 값으로 `str` 객체로 변환됩니다. `!!python/str` 스칼라는 utf-8 인코딩으로 인코딩된 `str` 객체로 변환됩니다. `!!python/unicode` 스칼라는 `unicode` 객체로 변환됩니다.

반대로, `str` 객체는 다음과 같이 변환됩니다:

1. 값이 ASCII인 경우 `!!str` 스칼라로 변환
2. 값이 올바른 utf-8 시퀀스인 경우 `!!python/str` 스칼라로 변환
3. 그 외의 경우 `!!binary` 스칼라로 변환

`unicode` 객체는 다음과 같이 변환됩니다:

1. 값이 ASCII인 경우 `!!python/unicode` 스칼라로 변환
2. 그 외의 경우 `!!str` 스칼라로 변환

### 문자열 변환 (Python 3 전용) (String conversion (Python 3 only))

Python 3에서는 `str` 객체가 `!!str` 스칼라로 변환되고 `bytes` 객체는 `!!binary` 스칼라로 변환됩니다. 호환성을 위해 `!!python/str`과 `!!python/unicode` 태그는 여전히 지원되며 `str` 객체로 변환됩니다.

### 이름과 모듈 (Names and modules)

함수나 클래스와 같은 정적 Python 객체를 표현하려면 복합 `!!python/name` 태그를 사용해야 합니다. 예를 들어, `yaml.dump` 함수는 다음과 같이 표현할 수 있습니다:

```
!!python/name:yaml.dump
```

마찬가지로, 모듈은 `!python/module` 태그를 사용하여 표현됩니다:

```
!!python/module:yaml
```

### 객체 (Objects)

피클링 가능한 모든 객체는 `!!python/object` 태그를 사용하여 직렬화할 수 있습니다:

```
!!python/object:module.Class { attribute: value, ... }
```

피클 프로토콜을 지원하기 위해 `!!python/object` 태그의 두 가지 추가 형식이 제공됩니다:

```
!!python/object/new:module.Class
args: [argument, ...]
kwds: {key: value, ...}
state: ...
listitems: [item, ...]
dictitems: [key: value, ...]

!!python/object/apply:module.function
args: [argument, ...]
kwds: {key: value, ...}
state: ...
listitems: [item, ...]
dictitems: [key: value, ...]
```

`args` 필드만 비어 있지 않은 경우, 위의 레코드는 다음과 같이 축약될 수 있습니다:

```
!!python/object/new:module.Class [argument, ...]

!!python/object/apply:module.function [argument, ...]
```

## 참조

### Python 3 지원 (Python 3 support)

3.08 릴리스부터 PyYAML과 LibYAML 바인딩은 Python 3에 대한 완전한 지원을 제공합니다. 이것은 Python 2와 Python 3 버전 간의 PyYAML API의 차이점에 대한 간략한 개요입니다.

Python 2에서:

- `str` 객체는 객체가 ASCII, UTF-8 또는 바이너리 문자열인지에 따라 `!!str`, `!!python/str` 또는 `!binary` 노드로 변환됩니다.
- `unicode` 객체는 객체가 ASCII 문자열인지 여부에 따라 `!!python/unicode` 또는 `!!str` 노드로 변환됩니다.
- `yaml.dump(data)`는 UTF-8로 인코딩된 `str` 객체로 문서를 생성합니다.
- `yaml.dump(data, encoding=('utf-8'|'utf-16-be'|'utf-16-le'))`는 지정된 인코딩으로 `str` 객체를 생성합니다.
- `yaml.dump(data, encoding=None)`는 `unicode` 객체를 생성합니다.

Python 3에서:

- `str` 객체는 `!!str` 노드로 변환됩니다.
- `bytes` 객체는 `!!binary` 노드로 변환됩니다.
- 호환성 이유로, `!!python/str`과 `!python/unicode` 태그는 여전히 지원되며 해당 노드는 `str` 객체로 변환됩니다.
- `yaml.dump(data)`는 `str` 객체로 문서를 생성합니다.
- `yaml.dump(data, encoding=('utf-8'|'utf-16-be'|'utf-16-le'))`는 지정된 인코딩으로 `bytes` 객체를 생성합니다.

### 자주 묻는 질문 (Frequently Asked Questions)

#### 중첩된 컬렉션이 없는 딕셔너리가 올바르게 덤프되지 않습니다

```python
import yaml
document = """
  a: 1
  b:
    c: 3
    d: 4
"""
print yaml.dump(yaml.load(document))
```

```
a: 1
b: {c: 3, d: 4}
```

결과를 주나요(#18, #24 참조)?

중첩된 매핑의 스타일이 다르지만 이는 올바른 출력입니다.

기본적으로 PyYAML은 컬렉션이 중첩된 컬렉션을 가지고 있는지에 따라 컬렉션의 스타일을 선택합니다. 컬렉션에 중첩된 컬렉션이 있다면, 블록 스타일이 할당됩니다. 그렇지 않으면 플로우 스타일을 갖게 됩니다.

컬렉션이 항상 블록 스타일로 직렬화되기를 원한다면, `dump()`의 매개변수 `default_flow_style`를 `False`로 설정하세요. 예를 들어,

```python
>>> print yaml.dump(yaml.load(document), default_flow_style=False)
a: 1
b:
  c: 3
  d: 4
```

> **출처**
>
> - [pyyaml.org/wiki/PyYAMLDocumentation](https://pyyaml.org/wiki/PyYAMLDocumentation)
