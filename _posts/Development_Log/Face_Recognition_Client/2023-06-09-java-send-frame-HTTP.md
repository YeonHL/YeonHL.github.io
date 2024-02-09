---
title: "[Android/Java] HTTP 프레임 전송 구현"
excerpt: "Android 카메라 화면을 HTTP로 전송하기"

categories:
  - Face Recognition Android Client
tags:
  - Android Java
  - Android HTTP

last_modified_at: 2023-06-09T22:55:00-05:00
---
## 구현 이유
- 기존 구현 방식은 라즈베리파이에 카메라 모듈을 연결하여 인식을 진행했다.
- 라즈베리파이에서 인식을 진행하고 그 결과를 웹으로 보여주는 방식을 사용했지만, 다음의 문제점이 있었다.

  1. 카메라 화면이 아니라 처리 이후의 결과 이미지를 보여주기에 사용자 입장에서는 영상의 딜레이가 존재했다.
  2. 카메라 모듈의 해상도가 낮아 인식율이 떨어졌다.

- 결과를 보여줄 때 Android 태블릿을 사용하고 있었기에, 태블릿의 카메라 화면을 사용자에게 보여주고 이를 인식에 사용하여 문제를 개선하고자 하였다.


## 구현 과정
### OpenCV: 프레임 처리
```java
import org.opencv.android.CameraBridgeViewBase.CvCameraViewFrame;
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.Size;


public Mat onCameraFrame(CvCameraViewFrame inputFrame) {
    // 화면 표시, Android에서 CV를 처리할 Mat 객체 생성
    mRgba = inputFrame.rgba();
    mGray = inputFrame.gray();
    
    // 전송할 프레임 객체 생성
    save_frame = mRgba.clone();

    // 해상도 처리 (임시)
    Size orig = mRgba.size();
    int cameraWidth = (int) orig.width;
    int cameraHeight = (int) orig.height;

    if (cameraWidth >= 640 && cameraHeight >= 480) {
        Rect roi = new Rect((cameraWidth/2)-320,(cameraHeight/2)-240,640,480);
        save_frame = new Mat(save_frame, roi);
    }
}
```
- 프레임 처리에는 OpenCV를 사용했다.
- 영상 처리를 적용하기 전 전송할 프레임을 `save_frame`으로 따로 저장했다.
- 기존 라즈베리파이 카메라 모듈의 해상도가 640x480이었기에, 해상도의 변화로 인한 인식 성능 차이를 방지하고자 640x480 해상도로 고정하였다.
- 문제가 없다는 것을 확인한 후에는 코드를 제거하여 사용자에게 제공되는 카메라 화면과 동일한 해상도로 지정하였다.
- 사용자에게 보여주는 화면에는 전송 없이 Android 자체에서 Face Detection을 적용한 화면을 보여줬다.

### OpenCV: 프레임 변환
```java
import org.opencv.core.Mat;
import org.opencv.core.MatOfByte;
import org.opencv.imgcodecs.Imgcodecs;


private byte[] matToPngByte (Mat frame) {
    MatOfByte buffer = new MatOfByte();
    Imgcodecs.imencode(IMAGE_TYPE, frame, buffer);
    byte[] imageData = buffer.toArray();

    return imageData;
}
```
- 프레임을 전송하기 위해 `Mat` 객체를 png 이미지의 바이트로 변환하는 함수를 생성했다.
- `IMAGE_TYPE`은 변경할 때를 고려하여 상단에서 `".png"`로 따로 선언하여 사용했다.


### OKHttp3: 프레임 전송
```java
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;

// ..
    
    private void sendFrameToServer(byte[] imageData) {
        String SERVER_URI = SERVER_URL + "/image";
        
        RequestBody requestBody = RequestBody.create(IMAGE_MEDIA_TYPE, imageData);
        Request request = new Request.Builder()
                .url(SERVER_URI)
                .post(requestBody)
                .build();
    }
```
- 프레임 전송에는 OKHttp3을 사용했다.
- `byte[] imageData`를 입력 받아서 HTTP로 전송하고 `void`로 반환하는 함수의 일부분이다.
- `SERVER_URL`은 서버의 IP를 상단에서 선언하여 사용했다. `/image`는 서버에서 지정한 엔드포인트이다.
- `RequestBody`에 프레임을 포함하고 이미지 타입을 명시하여 생성했다. 
- 전송 이미지 확장자 IMAGE_MEDIA_TYPE은 변경할 때를 고려하여 상단에서 `MediaType.parse("image/png");`으로 따로 선언했다.
- 이후 실제로 전송하기 위한 `Request`를 생성했다.

```java
import okhttp3.Call;
import okhttp3.Callback;
import okhttp3.Response;

// ...

        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
                ...
            }
        
            @Override
            public void onResponse(Call call, Response response) throws IOException {
                try (Response response = client.newCall(request).execute()) {
                    if (response.isSuccessful()) {
                        String responseData = response.body().string();
                        // ...
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
```
- `client`는 OkHttpClient 객체이다. 요청마다 설정을 바꿀 필요가 없어서 하나의 전역 변수를 재사용했다.
- 위 코드에서 `client.newCall(request).enqueue`로 비동기 환경에서 서버에 프레임을 전송하고, 그 결과에 따라 지정된 코드를 실행한다.
- `onFailure`은 서버와의 통신이 실패하거나 오류가 발생하면 실행되는 코드이다.
- `onResponse`는 서버와의 통신이 성공했을 때 실행된다. `response`에 서버의 응답 정보를 담으며, 응답 코드가 200~299 범위 내에 있으면 `response.isSuccessful()`의 코드가 실행된다.
- 요청 후에는 자원을 해제하여 메모리를 누수를 방지해야 한다. `try (Response response = client.newCall(request).execute())`와 같이 `try-with-resources` 블록을 사용하였다. 혹은 `response.close()`를 사용할 수도 있다.

> #### OkHttpClient
> ```java
> OkHttpClient client = new OkHttpClient.Builder()
>                                       .connectTimeout(0, TimeUnit.SECONDS)
>                                       .writeTimeout(0, TimeUnit.SECONDS)
>                                       .readTimeout(0, TimeUnit.SECONDS)
>                                       .autoClose(true)
>                                       .build();
> ```
> - `connectTimeout`은 연결 타임아웃, `writeTimeout`은 송신 타임아웃, `readTimeout`은 수신 타임아웃을 설정할 수 있다. `0`으로 설정하면 제한 시간을 설정하지 않는다.
> - OkHttp 3.12 이상부터는 `autoClose`를 설정하면 자동으로 객체가 닫힌다.


## 결과 및 느낀 점
- Android에서 카메라 화면을 전송하고, 라즈베리파이의 Python 서버에서 Face recognition을 진행 후 결과에 따라 비즈니스 로직을 수행하는 구조로 구현했다.
- 정상적으로 작동하는 것을 확인한 후에는 1280x720의 해상도로 전송하여 서비스를 구성했다. 높은 해상도에서 얼굴 주변 이미지만 추출하여 전송한다면 더 나은 결과를 기대할 수 있을 것이다.
- 네트워크 통신이 추가되면서 카메라 모듈을 사용했을 때보다 같은 시간에 처리할 수 있는 프레임의 수는 줄었지만, 사진의 해상도가 커지면서 하나의 프레임에 대한 인식율이 상승하여 더 빠른 속도를 얻을 수 있었다. 
- 또한 사용자에게 보여지는 화면의 딜레이 문제를 해결하여 사용자가 올바르게 사용함으로써 결과적으로 큰 속도 개선을 얻을 수 있었다.
- 라즈베리파이의 스펙이 태블릿보다 낮은 만큼 이후에는 태블릿에서 더 많은 부분을 처리하여 라즈베리파이의 부하를 줄이는 것을 목표로 해야겠다.