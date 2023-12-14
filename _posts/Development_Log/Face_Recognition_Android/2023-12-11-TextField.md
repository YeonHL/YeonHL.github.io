---
title: "[Android] Compose TextField 생성"
excerpt: "Compose의 TextField Component 생성"

categories:
  - Android Face Recognition 2023
tags:
  - Android Kotlin
  - Android Compose

last_modified_at: 2023-12-11T22:55:00-05:00
---

- Compose를 이용한 설정 화면 구성 중 `TextField`를 사용할 상황이 있었고, 해당 component의 재사용성을 고민했다.
- 다른 Android Compose 구성에도 사용할 `TextField`를 생성하고 이를 활용하여 이번 프로젝트에서 사용할 `TextField`를 생성했다.

## 1. `TextField`

```kotlin
package com.yhl.face.ui.base

@Composable
fun TextField(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    readOnly: Boolean = false,
    textStyle: TextStyle = LocalTextStyle.current,
    label: (@Composable () -> Unit)? = null,
    placeholder: (@Composable () -> Unit)? = null,
    leadingIcon: (@Composable () -> Unit)? = null,
    trailingIcon: (@Composable () -> Unit)? = null,
    prefix: (@Composable () -> Unit)? = null,
    suffix: (@Composable () -> Unit)? = null,
    supportingText: (@Composable () -> Unit)? = null,
    isError: Boolean = false,
    visualTransformation: VisualTransformation = VisualTransformation.None,
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
    keyboardActions: KeyboardActions = KeyboardActions.Default,
    singleLine: Boolean = false,
    maxLines: Int = if (singleLine) 1 else Int.MAX_VALUE,
    minLines: Int = 1,
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    shape: Shape = TextFieldDefaults.shape,
    colors: TextFieldColors = TextFieldDefaults.colors()
): Unit
```

- Compose는 `TextField`는 `String`을 입력 받은 경우와 `TextFieldValue`를 입력 받은 경우로 함수가 나뉜다. `value`와 `onValueChange`가 `TextFieldValue` 대신 `String`을 입력 받는 점 외에는 동일하다.
- 개발자 문서의 내용을 참고했을 때 `TextFieldValue`는 `TextRange` 범위 지정과 같은 기능을 지원하는 것으로 보여서 이를 활용한 `TextField`를 생성했다.

<br>

## 2. 범용 `TextField` 생성

```kotlin
package com.yhl.face.ui.base

import androidx.compose.foundation.interaction.MutableInteractionSource
import androidx.compose.foundation.text.KeyboardActions
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material3.LocalTextStyle
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.Text
import androidx.compose.material3.TextFieldColors
import androidx.compose.material3.TextFieldDefaults
import androidx.compose.runtime.Composable
import androidx.compose.runtime.remember
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Shape
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.input.TextFieldValue
import androidx.compose.ui.text.input.VisualTransformation
import androidx.compose.ui.tooling.preview.Preview


@Composable
fun BaseTextField(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    modifier:Modifier = Modifier,
    enabled: Boolean = true,
    readOnly: Boolean = false,
    textStyle: TextStyle = LocalTextStyle.current,
    label: String = "",
    placeholder: String = "",
    leadingIcon: (@Composable () -> Unit)? = null,
    trailingIcon: (@Composable () -> Unit)? = null,
    prefix:String = "",
    suffix:String = "",
    supportingText:String = "",
    isError: Boolean = false,
    visualTransformation: VisualTransformation = VisualTransformation.None,
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
    keyboardActions: KeyboardActions = KeyboardActions.Default,
    singleLine: Boolean = false,
    maxLines: Int = if (singleLine) 1 else Int.MAX_VALUE,
    minLines: Int = 1,
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    shape: Shape = TextFieldDefaults.shape,
    colors: TextFieldColors = TextFieldDefaults.colors()
) {
    if (supportingText == "") {
        OutlinedTextField(
            value = value,
            onValueChange = onValueChange,
            modifier = modifier,
            enabled = enabled,
            readOnly = readOnly,
            textStyle = textStyle,
            label = { Text(label, fontWeight = FontWeight.SemiBold) },
            placeholder = { Text(placeholder) },
            leadingIcon = leadingIcon,
            trailingIcon = trailingIcon,
            prefix = { Text(prefix) },
            suffix = { Text(suffix) },
            isError = isError,
            visualTransformation = visualTransformation,
            keyboardOptions = keyboardOptions,
            keyboardActions = keyboardActions,
            singleLine = singleLine,
            maxLines = maxLines,
            minLines = minLines,
            interactionSource = interactionSource,
            shape = shape,
            colors = colors
        )
    }
    else {
        OutlinedTextField(
            value = value,
            onValueChange = onValueChange,
            modifier = modifier,
            enabled = enabled,
            readOnly = readOnly,
            textStyle = textStyle,
            label = { Text(label, fontWeight = FontWeight.SemiBold) },
            placeholder = { Text(placeholder) },
            leadingIcon = leadingIcon,
            trailingIcon = trailingIcon,
            prefix = { Text(prefix) },
            suffix = { Text(suffix) },
            supportingText = { Text(supportingText) },
            isError = isError,
            visualTransformation = visualTransformation,
            keyboardOptions = keyboardOptions,
            keyboardActions = keyboardActions,
            singleLine = singleLine,
            maxLines = maxLines,
            minLines = minLines,
            interactionSource = interactionSource,
            shape = shape,
            colors = colors,
        )
    }
}
```

- `TextField`에서 가능한 모든 parameter를 입력 받으면서 `Text`가 입력될 부분은 `String`만 입력 받아서 사용했다.
- `Text`를 생성할 때 `FontWeight`는 직접 사용할 디자인에 맞게 미리 입력했다. 이후 사용하면서 자주 바뀐다면 변수로 입력 받는 것을 고려해야겠다.

<br>

## 3. App에서 사용할 `TextField`

```kotlin
package com.yhl.face.ui.base

import androidx.compose.foundation.layout.fillMaxHeight
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material3.MaterialTheme
import androidx.compose.runtime.Composable
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.runtime.setValue
import androidx.compose.ui.Modifier
import androidx.compose.ui.text.TextRange
import androidx.compose.ui.text.input.TextFieldValue
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.dbinc.uface.ui.base.BaseTextField


@Composable
fun SettingTextField(
    value: TextFieldValue,
    onValueChange: (TextFieldValue) -> Unit,
    prefix:String = "",
    suffix:String = "",
    keyboardOptions: KeyboardOptions = KeyboardOptions.Default,
) {
    BaseTextField(
        value = value,
        onValueChange = onValueChange,
        modifier = Modifier.size(width = 100.dp, height = 50.dp).fillMaxHeight(),
        prefix = prefix,
        suffix = suffix,
        keyboardOptions = keyboardOptions,
        singleLine = true
    )
}
```

- 앞서 생성한 `BaseTextField`를 활용하여 설정 화면 전용 `SettingTextField`를 만들었다.
- 현재 설정 화면에서 사용하려는 parameter만 입력 받았다.
- 현재는 크기를 고정 수치로 입력했다. 반응형 디자인이 아직 미숙하여 주어진 기간 내에는 적용이 어렵다고 생각했지만, 꼭 필요한 기능이므로 이후에 수정할 계획이다.

<br>

## 4. 느낀 점

- 재사용성이 높은 component를 만들고 싶었지만 경험의 부족으로 판단이 어려움을 느꼈다. 계속 사용하면서 부족한 부분을 보완해야겠다고 느꼈다.

<br>

## 참고

[https://developer.android.com](<https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#TextField(kotlin.String,kotlin.Function1,androidx.compose.ui.Modifier,kotlin.Boolean,kotlin.Boolean,androidx.compose.ui.text.TextStyle,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Boolean,androidx.compose.ui.text.input.VisualTransformation,androidx.compose.foundation.text.KeyboardOptions,androidx.compose.foundation.text.KeyboardActions,kotlin.Boolean,kotlin.Int,kotlin.Int,androidx.compose.foundation.interaction.MutableInteractionSource,androidx.compose.ui.graphics.Shape,androidx.compose.material3.TextFieldColors)>)
