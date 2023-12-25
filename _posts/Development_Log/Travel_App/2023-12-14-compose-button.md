---
title: "[Android] Button 구현"
excerpt: "Button을 구현하며 겪은 과정을 작성했다."

categories:
  - Android Travel App
tags:
  - Android Kotlin
  - Android Compose

last_modified_at: 2023-12-14T23:36:00-05:00
---

- 앱에서 사용할 버튼의 composable function을 생성하고 정렬을 적용했다.
- 정렬까지 적용하면서 layout 내에 배치하는 방법을 배울 수 있었다.

<br>

## 1. App에서 사용할 Button

```kotlin
@Composable
fun AppElevatedButton(label:String, onClick: () -> Unit) {
    ElevatedButton(onClick = { onClick() }) {
        SubtitleText(label)
    }
}
```

- 버튼 디자인의 통일을 위해 composable function을 생성했다.
- 텍스트는 이전에 생성한 Text Component를 사용했다.

<br>

## 2. Button 배치하기

```kotlin
Scaffold(
        bottomBar = { JourneyBottomNavigation(navController = navController)}
    ) {
        Box(
            modifier = Modifier.padding(it).fillMaxSize(),
            contentAlignment = Alignment.Center){

            AppElevatedButton(
                label = stringResource(R.string.add_journey),
                onClick = {// TODO: 화면 생성 후 연결하기}
            )

            JourneyNavigationGraph(navController = navController)
        }
    }
```

- 기존의 `Scaffold`의 `content`에 `AppElevatedButton`을 추가했다.
- `Box` 내부에 추가했으며, 가운데에 배치하는 것이 목표이므로 `contentAlignment = Alignment.Center`를 추가했다.
- `Modifier`에 `.fillMaxSize()`을 설정하여 `Box`가 화면 전체를 차지하도록 구성함으로써 `contentAlignment`가 올바르게 적용되도록 수정했다.

<br>

## 3. 결과

<img src="https://i.ibb.co/MDq28Ck/button-result.png" style="width: 300px" />
- Android Developers 사이트에서 공부한 내용을 더 깊게 이해하기 위해서라도 앱의 나머지 부분을 빠르게 완성해야겠다.
