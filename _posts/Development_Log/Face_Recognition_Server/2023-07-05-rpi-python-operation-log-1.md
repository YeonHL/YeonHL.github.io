---
title: "[RPI/Python] 서비스 운영 일지 1"
excerpt: "서비스를 운영하면서 시도했던 내용들"

categories:
  - Face Recognition Server
tags:
  - Python FastAPI
  - Python OpenCV

last_modified_at: 2023-07-05T22:55:00-05:00
---
## 관련 글
- [[Android/Java] 서비스 운영 일지 1](https://yeonhl.github.io/face%20recognition%20android%20client/java-operation-log-1/)

## 서론
- 개발 이후 서비스를 운영하면서 개선해야 할 부분들이 보였고, 사용자로부터 요청 사항도 받았다. 시도했던 내용들을 정리하여 하나의 글로 작성하였다.

## 1. 인식 상태 알림
```python
result = face_service.recognition(request.get_data())

return result
```
- 무선 환경이라 약한 신호로 인해 통신이 실패하는 경우가 있었다.
- 하지만 알림 메시지가 따로 없어서 인식 로직을 시작 했는지, 서버 상태가 불안정한 것인지 확인이 불가능한 문제가 있었다.
- 서버와의 통신이 이루어졌을 경우 그 결과를 전송하여 Android에서 처리할 수 있도록 구현했다.

## 2. 인증 번호 입력 실패 시 기록 저장
```python
@app.route('/save', methods=['POST'])
def save():
  frame_data = base64.b64decode(request.get_data())
  face_service.save_image(frame_data)
```
- 프레임을 입력 받아서 이미지 저장 함수를 호출했다.


## 3. 비동기 적용 시도 (FastAPI)
```python
from fastapi import FastAPI

app = FastAPI()

@app.route('/image', methods=['POST'])
async def image():
    return await face_service.recognition(request.get_data())
```
- 기존 구조에서 Flask만 FastAPI로 변경하여 테스트를 진행했다. `async`나 `await` 키워드를 사용하여 비동기 함수로 변경했다.
- 반응 속도는 확실히 빨랐지만 처리 후 일시정지와 같은 기능이 작동하지 않아서 예상치 못한 버그들이 발생하여 이전의 Flask로 되돌렸다.


## 4. 인식 전 변수 초기화
```python
def reset_recognition(self):
    self.variable1 = []
    self.variable2 = []
    self.variable3 = []
    self.variable4 = None

    self.variable5 = "Unknown"
    self.variable6 = "Unknown"
```
- 요청을 처리하던 중 이전 요청에 사용된 변수가 초기화되지 않아 한참 뒤에 새로 들어온 요청에도 인식을 새로 수행하지 않고 이전 결과를 그대로 반환하여 출입문이 열리는 문제가 있었다.
- 변수를 초기화할 수 있도록 `reset_recognition` 함수를 만들어서 매 호출 때마다 사용하는 방법을 적용했다.
- 모든 요청이 아니라 일부 요청에서만 발생한 문제였기에 원인을 파악하기 어려웠다. 로그를 남기는 코드가 없어서 추가로 구현할 필요를 느꼈다.


## 5. 출입문 개방 엔드 포인트 분리 시도
```python
@app.route('/open', methods=['GET'])
def open():
  face_service.door_open()
```
- 인식 성공 시 출입문 개방하고 Android에게 결과를 반환하면 개방 이후에 성공 메시지가 뒤늦게 출력되어 사용자에게 알리는 것이 무의미한 문제가 있었다.
- 출입문의 개방도 Android가 요청하는 방식으로 수정하여 인터페이스를 사용자에게 보여주려고 했으나, 개방 속도가 너무 늦어서 사용자가 오히려 불편한 것 같아서 다시 이전 형태로 되돌렸다.


## 느낀 점
- 코드를 받을 당시 클래스 분리나 디자인 패턴이 적용되지 않고 하나의 파일로 구성되어 유지 보수에 불편함을 겪었다. 시간이 있을 때 리팩토링을 한 번 진행할 필요가 있음을 느꼈다.
- 비동기 구조를 제대로 적용하면 입력된 프레임을 한 번에 처리하여 빠른 속도를 얻을 수 있을 것으로 기대하고 있다. FastAPI를 더 공부해서 다시 도전하고 싶다.
- 출입문 개방 메시지 문제는 네트워크 환경을 개선하는 방법으로 시도해야겠다고 생각했다.
- 운영 중에 발생한 문제들에 대응하면서 로그의 필요성을 느꼈다. 가장 우선적으로 추가해야겠다.