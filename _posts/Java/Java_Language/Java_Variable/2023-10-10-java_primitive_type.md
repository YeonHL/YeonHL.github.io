---
title: "[Java] 데이터 타입 (Primitive Type)"
excerpt: "Java의 데이터 타입에 대해 설명한다."

categories:
  - Java Language
tags:
  - Java Variable

last_modified_at: 2023-10-10T00:23:00-05:00
---

- 실제 값을 저장하는 타입이다.
- boolean 외에는 다른 타입과 연산이 가능하다. 문자형인 char는 Unicode로 저장하므로 연산이 가능하다.
- 접미사의 경우 소문자와 대문자를 구분하지 않는다. 정수형과 실수형에서는 접미사를 사용한다.

<br>

## 1. 논리형

### 1) boolean

```java
boolean bitFlag = true
```

- true와 false 중 하나를 가진다.
- 조건식 및 대답이나 스위치와 같은 논리적 계산에 사용한다.

<br>

## 2. 문자형

### 1) char

```java
char ch = 'J';
```

- 문자를 저장할 때 사용, unicode로 저장한다.
- 작은따옴표 `''`로 나타낸다. 변수 당 하나의 문자만 저장 가능하다.
- Unicode 출력을 원할 경우 `(int)`로 Casting(형변환)하여 출력할 수 있다.

<br>

**Escape Code**

| 특수 문자             | 문자 리터럴                        |
| --------------------- | ---------------------------------- |
| tab                   | `\t`                               |
| backspace             | `\b`                               |
| form feed             | `\f`                               |
| new line              | `\n`                               |
| carriage return       | `\r`                               |
| 역슬래쉬(\)           | `\\`                               |
| 작은따옴표            | `\'`                               |
| 큰따옴표              | `\"`                               |
| 유니코드(16진수) 문자 | `\u유니코드` (ex) char a=' \u0041' |

<br>

### 2) String

```java
String myString = new String("Hello, World!");
String name = "Java";
String blank = "";
```

- 두 글자 이상은 '문자열 리터럴'로 `""`로 나타낸다.
- 문자열 리터럴을 `String`이라 하며 Class를 통해 구현하여 `new`를 사용해야 하지만 편의를 위해 바로 할당하는 방법도 허용된다.
- String은 공백을 허용하지만 문자 리터럴은 반드시 문자를 포함해야 한다.

<br>

```java
String name = "Ja" + "va"; // name에 "Java" 할당
String mixed = "abc" + 123 // mixed에 "abc123" 할당
```

- 덧셈 연산자를 이용하여 문자열을 결합할 수 있다.
- 하나라도 String이 포함된 덧셈이라면 **나머지 변수도 String으로 변환**하여 덧셈을 진행한다.

<br>

## 3. 정수형

### 1) byte, short, int, long

```java
byte byteNum = 1;
short shortNum = 2;
int intNum = 4;
long longNum = 8;
```

- 정수 값을 저장할 때 사용한다.
- **Overflow가 발생하면 최대값 혹은 최소값**으로 돌아간다.
- byte와 short가 메모리를 절약할 수는 있지만 저장 가능 범위가 작아서 초과할 가능성이 크고, JVM은 피연산자를 4 byte 단위로 저장하므로 **int 사용을 권장**한다.
- 주로 int와 long을 사용한다. 그 중 int가 CPU를 효율적으로 처리할 수 있어서 많이 사용하고 **int 범위를 넘으면 long**을 사용한다.
- **long의 범위를 넘으면 실수형이나 `BigInteger`를 사용**한다.
- **byte는 이진 데이터**를 다룰 때 사용한다.
- **short는 C언어와의 호환**을 위해 추가됐다.

<br>

```java
int octNum = 010; // 8진수, 0을 붙인다. 8
int hexNum = 0x10; // 16진수, 0x를 붙인다. 16
int binNum = 0b10; // 2진수, 0b을 붙인다. 2
```

- 10진수 외에 2,8,16진수는 **접두사를 사용**하여 표현한다.

<br>

```java
long big = 100_000_000_000L;
```

- JDK 1.7부터는 **정수형 리터럴의 중간에 `_`를 사용하여 큰 숫자의 자리수를 구분**하여 나타낼 수 있다.
- **long 타입의 경우 접미사 `L`**를 붙인다.

<br>

## 4. 실수형

### 1) float, double

```java
float pi = 3.14f;
double rate = 1.618d;
```

- 실수를 저장할 때 사용한다.
- **높은 정밀도를 위해 주로 double을 사용**한다.
- **연산 속도 향상이나 메모리 절약을 원할 경우 float를 고려**할 수 있다.
- **Overflow가 발생하면 변수 값이 Infinity**로 설정된다.
- 정수형과 달리 **Underflow가 존재**한다. 실수형으로 표현할 수 없는 아주 작은 값 (양의 최소값보다 작은 값)일 때 발생하며 **변수 값은 0으로 설정**된다.
- float에는 `f`, double에는 `d` 접미사가 붙는다.
- **double이 기본형**이므로 `d`는 생략 가능

<br>

```java
float f = 3.14e3f; // 3140.0f를 의미
double d = 1e-3 // 0.001을 의미
```

- 10의 제곱은 접미사 `e`를 사용하여 표현할 수 있다.

<br>

```java
int hex1 = 0x12p3 // 144를 의미, 18*8
```

- 16진수에서 2의 제곱은 접미사 `p`를 사용하여 표현할 수 있다. 자주 사용되진 않는다.

> float는 부호 1비트, 지수 8비트, 가수 23비트로 이루어지며 double은 부호 1비트, 지수 11비트, 가수 52비트로 이루어진다.
>
> 지수의 경우 나타낼 수 있는 범위의 최대값을 양의 무한대, 최소값을 음의 무한대로 사용한다.
>
> 가수는 실제 값을 저장하는 부분으로 2진수 23자리가 7자리 정도의 10진수를 저장할 수 있고 52자리가 15자리 정도의 10진수를 저장할 수 있어서 정밀도의 차이가 나타난다.

<br>

## 5. 타입 별 byte

|        | 1 byte  | 2 byte | 4 byte | 8 byte |
| ------ | ------- | ------ | ------ | ------ |
| 논리형 | boolean |        |        |        |
| 문자형 |         | char   |        |        |
| 정수형 | byte    | short  | int    | long   |
| 실수형 |         |        | float  | double |

- 정수형의 1byte(8bit)당 2의 8제곱까지 나타낼 수 있음을 나타낸다.
- 실수형에서 float는 7자리까지, double은 15자리까지 오차 없이 저장할 수 있다. (그보다 큰 값도 저장은 가능하지만 오차가 발생한다.)

<br>

```java
int i = 'A'; // 'A'의 Unicode 65 할당
long l = 123; // long에 int 변수 할당
double d = 3.14f; // double에 float 할당
short s = 0x1234; // 저장 가능한 범위의 int 할당

int i2 = 123L; // 나타낼 수 있는 범위더라도 long 타입이므로 불가능
```

- 저장 범위가 넓은 타입에 좁은 타입을 갖는 변수를 할당하는 것은 허용되지만 넓은 타입을 가진 변수를 좁은 타입 변수에 할당하는 것은 불가하다.
- byte와 short 타입은 접미사가 따로 없으므로 int 타입의 literal을 사용한다. 범위만 준수하면 할당 가능하다.

<br>

> ## 6. Reference Type (참조형)
>
> - 값이 저장된 주소를 값으로 저장한다.
> - C언어와 달리 참조형 변수 간 연산이 불가능하다.
> - Class는 `new`로 생성한 객체를 참조형으로 선언하여 사용하는 대표적인 사례이다.
>
> ```java
> <Class name> new <Variable name>
> ```
