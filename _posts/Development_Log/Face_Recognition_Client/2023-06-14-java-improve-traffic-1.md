---
title: "[Android/Java] 프레임 전송 트래픽 개선 1"
excerpt: "불필요한 프레임 전송 줄이기"

categories:
  - Face Recognition Android Client
tags:
  - Android Java
  - Android Async
  - Android OpenCV
last_modified_at: 2023-06-14T22:55:00-05:00
---
## 관련 글
- [[RPI/Python] 프레임 전송 트래픽 개선](https://yeonhl.github.io/face%20recognition%20server/rpi-python-improve-traffic-1/)

## 구현 이유
- 불필요한 프레임 전송 요청 및 짧은 시간 내 너무 많은 요청을 전송하여 비즈니스 로직 성능에 악영향을 주는 문제가 있었다.
- 필요한 상황에서만 처리 가능한 수준으로 프레임을 전송하여 문제를 해결하고자 구현했다.

## 구현 방법
### 1. 전송 간격 설정
```java
// ...
currentTime = System.currentTimeMillis();
if (currentTime - lastExecutionTime >= 150) {
    lastExecutionTime = currentTime;
// ...
```
- 기존에는 서버에 요청을 보내는 간격을 지정하지 않았다. 이로 인해 서버가 하나의 요청을 처리할 수 있는 시간보다 더 빠르게 요청하여 서버에 쌓이면서 발생하는 딜레이 문제가 있었다.
- 서버의 처리 속도를 고려하여 전송 간격을 설정함으로써 요청이 쌓이는 것을 방지했다.

### 2. 요청 일시 중지
```java
// ...
recogFlag = false;

recogFlagThread = new Thread(new Runnable() {
    @Override
    public void run() {
        try {
            Thread.sleep(2500);
            recogFlag = true;
        } 
        // ...
    }
});
recogFlagThread.start();
// ...
```
- 비즈니스 로직이 성공한 후에도 요청을 보내는 것을 따로 멈추지 않아 불필요한 요청이 전송되고 처리되는 문제가 있었다.
- 로직 실행 Flag를 설정하고 서버에서 처리 완료 응답을 보내면 잠시 Flag를 `false`로 전환하여 요청을 중단하는 방법을 사용했다.

## 3. 프레임 해상도 조절
```java
if (cameraWidth >= 1280 && cameraHeight >= 720) {
    Rect roi = new Rect((cameraWidth/2)-640,(cameraHeight/2)-360,1280,720);
    save_frame = new Mat(save_frame, roi);
}
```
- 해상도를 따로 지정하지 않고 사용했으나, Android OS 업데이트 이후 해상도가 1920x1080으로 전송되는 변경점이 있었다.
- 기존에 전송하던 것처럼 해상도를 1280x720으로 조절했다.


## 결과 및 느낀 점
- 적용 후에는 딜레이로 인해 같은 요청 건에 대해 비즈니스 로직이 여러 번 처리되거나 이후 요청을 받지 못하는 문제를 해결할 수 있었다.
- 또한 프레임 해상도를 다시 조절하여 하나의 프레임을 처리하는데 소요되는 시간이 길어지는 문제를 해결할 수 있었다. 얼굴 주변 프레임만 전송하는 것이 가장 이상적이므로 문제가 지속된다면 적용을 고려해야 겠다고 생각했다.
