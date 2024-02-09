---
title: "[RPI/Python] 이미지 수신"
excerpt: "RPI 카메라 모듈 대신 Android 카메라 화면을 수신하기"

categories:
  - Face Recognition Server
tags:
  - Python Flask
  - Python OpenCV

last_modified_at: 2023-06-09T22:55:00-05:00
---
## 구현 이유
- 기존 구현 방식은 라즈베리파이에 카메라 모듈을 연결하여 인식을 진행했다.
- 라즈베리파이에서 인식을 진행하고 그 결과를 웹으로 보여주는 방식을 사용했지만, 다음의 문제점이 있었다.

  1. 카메라 화면이 아니라 처리 이후의 결과 이미지를 보여주기에 사용자 입장에서는 영상의 딜레이가 존재했다.
  2. 카메라 모듈의 해상도가 낮아 인식율이 떨어졌다.

- 결과를 보여줄 때 Android 태블릿을 사용하고 있었기에, 태블릿의 카메라 화면을 사용자에게 보여주고 이를 인식에 사용하여 문제를 개선하고자 하였다.


## 구현 과정
### Flask: Endpoint 설정
```python
from flask import request

from service import FaceService

app = Flask(__name__)
face_service = FaceService()


@app.route('/image', methods=['POST'])
def image():
    return face_service.recognition(request.get_data())
```
- Android에서 송신한 이미지를 처리하기 위해 Endpoint를 새로 생성했다.
- `flask`의 `request`로 수신한 데이터를 불러왔다. 데이터는 Android에서 전송한 Mat 객체의 png `bytes`이다.
- 이미지 처리는 `service.py` 파일에 `FaceService` Class를 생성하고, Instance를 생성하여 사용했다.

### OpenCV: 이미지 입력
```python
import cv2
import numpy as np

class FaceService():
    # ...
    
    def recognition(self, frame_data):
        frame = np.frombuffer(frame_data, np.uint8)        
        # Face recognition 수행
```
- 수신한 `bytes`를 다시 `ndarray`로 변환하여 Face Recognition을 수행했다.
- 처리 결과에 따라 서버 비즈니스 로직을 수행하고, 사용자에게 결과를 제공하기 위해 필요한 정보를 반환했다.

## 결과 및 느낀 점
- Android에서 카메라 화면을 전송하고, 라즈베리파이의 Python 서버에서 Face recognition을 진행 후 결과에 따라 비즈니스 로직을 수행하는 구조로 구현했다.
- 네트워크 통신이 추가되면서 카메라 모듈을 사용했을 때보다 같은 시간에 처리할 수 있는 프레임의 수는 줄었지만, 사진의 해상도가 커지면서 하나의 프레임에 대한 인식율이 상승하여 더 빠른 속도를 얻을 수 있었다.
- 라즈베리파이의 스펙이 태블릿보다 낮은 만큼 이후에는 태블릿에서 더 많은 부분을 처리하여 라즈베리파이의 부하를 줄이는 것을 목표로 해야겠다.