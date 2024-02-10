---
title: "[RPI/Python] 프레임 전송 트래픽 개선"
excerpt: "불필요한 프레임 전송 줄이기"

categories:
  - Face Recognition Server
tags:
  - Python Flask

last_modified_at: 2023-06-14T22:55:00-05:00
---
## 관련 글
- [[Android/Java] 프레임 전송 트래픽 개선](https://yeonhl.github.io/face%20recognition%20android%20client/java-improve-traffic/)


## 구현 이유
- 불필요한 프레임 전송 요청 및 짧은 시간 내 너무 많은 요청을 전송하여 비즈니스 로직 성능에 악영향을 주는 문제가 있었다.
- 이미 처리한 요청에 대해서는 다시 처리하지 않음으로써 딜레이와 동일 요청에 대한 비즈니스 로직 실행을 방지하고자 하였다. 

## 구현 방법
### 일시 정지 설정
```python
global success_time

    if time.time() - success_time < 2:
        return ""
```
- 비즈니스 로직이 성공했을 경우 특정 시간 동안에는 모든 요청을 처리하지 않고 빈 문자열을 반환하도록 구현했다.
- 로직을 실행하지 않고 바로 반환함으로써 밀린 요청을 처리하고, 비즈니스 로직이 여러 번 실행되는 것을 방지하였다.

## 결과 및 느낀 점
- 적용 후에는 딜레이로 인해 같은 요청 건에 대해 비즈니스 로직이 여러 번 처리되거나 이후 요청을 받지 못하는 문제를 해결할 수 있었다.
- 서버가 비동기로 처리하지 못해서 적은 요청에도 딜레이가 발생하는 것이 아닐까 생각했다. 다음에는 비동기 구조를 적용해봐야 겠다.
