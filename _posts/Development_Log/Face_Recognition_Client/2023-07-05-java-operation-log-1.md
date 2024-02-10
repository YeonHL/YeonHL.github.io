---
title: "[Android/Java] 서비스 운영 일지 1"
excerpt: "서비스를 운영하면서 시도했던 내용들에 대한 글"

categories:
  - Face Recognition Android Client
tags:
  - Android Java
  - Android Notification

last_modified_at: 2023-07-05T22:55:00-05:00
---
## 관련 글
- [[RPI/Python] 서비스 운영 일지 1](https://yeonhl.github.io/face%20recognition%20android%20client/rpi-python-operation-log-1/)

## 서론
- 개발 이후 서비스를 운영하면서 개선해야 할 부분들이 보였고, 사용자로부터 요청 사항도 받았다. 시도했던 내용들을 정리하여 하나의 글로 작성하였다.

## 1. 인식 상태 알림
- 무선 환경이라 신호가 약해서 통신이 실패하는 경우가 있었다.
- 하지만 알림 메시지가 따로 없어서 Face Recognition 로직을 시작 했는지, 서버 상태가 불안정한 것인지 확인이 불가능한 문제가 있었다.
- 서버와의 통신에 실패했을 경우 Toast로 알림을 발생시키고, 통신이 이루어졌을 경우 인식 중인지, 인식 성공인지를 표시하는 기능을 추가했다.

```java
public void makeToast(final String message, int fontSize, int duration) {
    mainHandler.post(new Runnable() {
        @Override
        public void run() {
            if (toast != null) {
                toast.cancel(); // Cancel the existing Toast
            }

            // Create and display the new Toast
            toast = Toast.makeText(getApplicationContext(), message, duration);

            if (fontSize != 0) {
                ViewGroup group = (ViewGroup) toast.getView();
                if (group != null && group.getChildCount() > 0) {
                    TextView messageTextView = (TextView) group.getChildAt(0);
                    if (messageTextView != null) {
                        messageTextView.setTextSize(TypedValue.COMPLEX_UNIT_DIP, fontSize);
                    }
                }
            }

            toast.show();
        }
    });
}
```
- 알림을 발생시켜야 하는 다른 경우에도 사용하기 위해 함수로 구현했다.
- `private final Handler mainHandler = new Handler(Looper.getMainLooper());`를 상단에서 선언하여 사용했다.
- 메시지와 폰트 크기, Toast 유지 시간을 입력 받았으며 Handler를 사용하여 표시하도록 구현했다.
- Toast가 존재할 때 기존 Toast를 취소하지 않으면 겹쳐서 표시되는 문제가 있었다. `toast != null` 조건을 추가하여 `toast.cancel()`을 실행하는 것으로 이를 해결했다.

## 2. 인증 번호 입력 실패 시 기록 저장
- 허가 받지 않은 인원이 임의의 인증 번호 입력을 시도하는 경우를 대비하여 특정 횟수 동안 인증 실패 시 당시 화면을 저장하고, 특정 시간 동안 입력을 받지 말라는 요청을 받았다. 

```java
if (errCnt == 3 && errTimer > 0) {
    errFlag = true;
    setMsg("인증 오류 횟수 초과되었습니다. \n" + errTimer + "초 후 재시도 해 주세요", 0);
} 
else if (errTimer == 0) {
    errFlag = false;
    errCnt = 0;
    errTimer = initErrTime;
}

```
- 첫 실패 시 시간 카운트를 시작하여, 그 시간 동안 정해진 횟수를 실패할 경우 Flag를 True로 전환하여 인증 번호 입력을 막는 로직을 구현했다.
- 정해진 횟수 만큼 실패할 경우 사진을 저장하는 엔드 포인트에 이미지 데이터를 전송했다.

## 3. 인증 번호 입력 중 인식 중지

```java
currentTime = System.currentTimeMillis();
if (currentTime - ToastStartTime >= 10000) {
    makeToast(getString(R.string.recognition_stop), 20 , Toast.LENGTH_SHORT);
    ToastStartTime = currentTime;
}
```
- 인증 번호를 사용할 인원들은 등록된 인원이 아닌 상황이라 Face Recognition 로직을 수행할 필요가 없었다.
- 불필요한 트래픽 발생을 막기 위해 Flag를 생성하여 번호 입력 중에는 인식이 중단되도록 구현했다.

## 기타
- WebSocket 적용: 딜레이로 인한 문제나 인식 일시 정지와 같은 기능이 안정적으로 동작하도록 WebSocket을 여는 구조를 고려했다. 하지만 보안팀에서 불가능하다는 답변을 받아서 시도하지 않았다.

## 느낀 점
- 코드를 받을 당시 클래스 분리나 디자인 패턴이 적용되지 않고 하나의 파일로 구성되어 유지 보수에 불편함을 겪었다. 시간이 있을 때 리팩토링을 한 번 진행할 필요가 있음을 느꼈다.