---
title:  "[Java] 변수와 상수 (Variable & Constant)"
excerpt: "Java의 변수와 상수에 대해 설명한다."

categories:
  - Java Language
tags:
  - Java Variable

last_modified_at: 2023-10-10T00:23:00-05:00
---
## 1. Variable
```java
// 변수 선언
<type> <name>;

// 변수 초기화 (처음으로 값 저장)
<type> <name> = <value>;
```
- 단 하나의 값을 저장할 수 있는 메모리 공간
- 변수 초기화를 진행하지 않으면 알 수 없는 값 (garbage value)이 남아있을 수 있다.
- **지역 변수는 반드시 초기화** 해야하며 **클래스 변수와 인스턴스 변수는 초기화를 생략할 수 있다.**

<br>

## 2. Constant
```java
# 상수 선언
final <type> <name> = <value>;
```
- 한번 저장한 값은 변경되지 않는 메모리 공간
- 선언할 때 변수 타입 앞에 **`final`을 붙여서 선언**한다.
- 선언할 때 초기화해야 하며 이후 값 변경이 허용되지 않는다.

<br>

```java
final int WIDTH = 20; // 길이
final int HEIGHT = 10; // 높이

int triangleArea = (WIDTH * HEIGHT) / 2; // 삼각형 면적
int rectangleArea = WIDTH * HEIGHT; // 사각형 면적
```
- 그 자체로 값을 의미하는 것을 **Literal**이라 한다. 기존 수학에서의 '상수'를 의미한다.
- 단순 숫자가 아니라 의미 있는 단어를 사용하여 코드의 이해와 수정을 돕는다.
