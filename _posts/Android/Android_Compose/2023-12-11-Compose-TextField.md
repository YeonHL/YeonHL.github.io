---
title: [Android] Compose TextField
excerpt: Compose의 TextField에 대해 알아보자

categories:
  - Android Compose
tags:
  - Android Compose Input

last_modified_at: 2023-12-11T22:55:00-05:00
---

- `String`을 입력 받기 위한 Component
- 이를 변환하여 숫자 등으로 사용하면 된다.

## 1. String

```kotlin
@Composable
fun TextField(
    value: String,
    onValueChange: (String) -> Unit,
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

- `label`: 텍스트 입력 전에는 크게 표시되며 클릭하면 상단에 작게 표시된다.
- `placeholder`: 입력이 없을 때 표시될 예시
- `singleLine`: 입력을 한 줄만 입력 받을지 결정한다. (`maxLines`에 조건으로 입력된다.)
- `prefix`: 입력의 앞에 고정할 값을 지정한다.
- `suffix`: 입력의 뒤에 고정할 값을 지정한다.
- `supportingText`: 입력 창 아래에 사용자에게 입력 관련 정보를 제공할 수 있다.

<br>

## 2. TextFieldValue

```kotlin
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

### 1) [TextFieldValue](https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/TextFieldValue)

```kotlin
TextFieldValue(
    annotatedString: AnnotatedString,
    selection: TextRange,
    composition: TextRange?
)
```

- State를 편집하기 위한 정보를 갖는 Class이다.
- 입력 서비스가 업데이트한 텍스트 선택, 커서, 텍스트 및 텍스트 구성을 나타내며 텍스트 편집 composable에서 해당 값의 변경 사항을 관찰할 수 있다.
- 편집 버퍼의 입력 상태 스냅샷을 저장하고 `getTextBeforeCursor`, `getSelectedText`와 같은 IME 요청에 응답하기 위한 유틸리티 함수를 제공한다.
- 입력 서비스 composition은 IME에서 생성된 텍스트의 인스턴스로 composition의 시각적 예는 현재 작성된 단어가 밑줄 또는 텍스트 배경을 통해 다른 단어와 시각적으로 구분되는 것이다.
- [W3C IME Composition](https://www.w3.org/TR/ime-api/#ime-composition).은 [composition](<https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/TextFieldValue#composition()>)과 함수에 의해 정의된다. Composition이 null인 TextFieldValue를 텍스트 필드에 전달할 때 텍스트에 활성 composition이 있으면 변경 사항이 적용된다.

```kotlin
fun copy(
    annotatedString: AnnotatedString = this.annotatedString,
    selection: TextRange = this.selection,
    composition: TextRange? = this.composition
): TextFieldValue
```

- Composition을 적용하면 IME에서 작성 중이던 변경 사항이 적용된다. 진행 중인 IME 컴포지션을 의도적으로 적용하지 않으려면 [copy](<https://developer.android.com/reference/kotlin/androidx/compose/ui/text/input/TextFieldValue#copy(androidx.compose.ui.text.AnnotatedString,androidx.compose.ui.text.TextRange,androidx.compose.ui.text.TextRange)>) 함수를 사용해야 한다. `TextFieldValue`를 반환한다.

```kotlin
// 예시
var text by rememberSaveable(stateSaver = TextFieldValue.Saver) {
    mutableStateOf(TextFieldValue("example", TextRange(0, 7)))
}
```

### 2) 적용

![[textfield_textfieldvalue.png]]

```kotlin
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.text.TextRange
import androidx.compose.ui.text.input.TextFieldValue

var text by rememberSaveable(stateSaver = TextFieldValue.Saver) {
    mutableStateOf(TextFieldValue("example", TextRange(0, 7)))
}

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Label") }
)
```

<br>

## 예제

### 1) SingleLine

![[textfield_single.png]]

```kotlin
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

var text by rememberSaveable { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Label") },
    singleLine = true
)
```

### 2) Placeholder

![[textfield_placeholder.png]]

```kotlin
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

var text by rememberSaveable { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Email") },
    placeholder = { Text("example@gmail.com") }
)
```

### 3) Icon

![[textfield_icon.png]]

```kotlin
import androidx.compose.material3.Icon
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

var text by rememberSaveable { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Label") },
    leadingIcon = { Icon(painter = painterResource(R.drawable.ic_round_door), contentDescription = "Localized description") },
    trailingIcon = { Icon(painter = painterResource(R.drawable.ic_round_timer), contentDescription = "Localized description") }
)
```

### 4) Prefix & Suffix

![[textfield_fix.png]]

```kotlin
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

var text by rememberSaveable { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    singleLine = true,
    label = { Text("Label") },
    prefix = { Text("www.") },
    suffix = { Text(".com") },
    placeholder = { Text("google") },
)
```

### 5) Error

![[textfield_error.png]]

```kotlin
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.foundation.text.KeyboardActions
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.semantics.error
import androidx.compose.ui.semantics.semantics

var text by rememberSaveable { mutableStateOf("") }

val errorMessage = "Text input too long"
var isError by rememberSaveable { mutableStateOf(false) }
val charLimit = 10

fun validate(text: String) {
	isError = text.length > charLimit
}

TextField(
	value = text,
	onValueChange = {
		text = it
		validate(text)
	},
	singleLine = true,
	label = { Text(if (isError) "Username*" else "Username") },
	supportingText = {
		Text(
			modifier = Modifier.fillMaxWidth(),
			text = "Limit: ${text.length}/$charLimit",
			textAlign = TextAlign.End,
		)
	},
	isError = isError,
	keyboardActions = KeyboardActions { validate(text) },
	modifier = Modifier.semantics {
		// Provide localized description of the error
		if (isError) error(errorMessage)
	}
)
```

### 6) SupportingText

![[textfield_supporting_text.png]]

```kotlin
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

var text by rememberSaveable { mutableStateOf("") }

TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Label") },
    supportingText = {
        Text("Supporting text that is long and perhaps goes onto another line.")
    },
)
```

### 7) VisualTransformation (Password)

![[textfield_visual_transformation.png]]

```kotlin
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material3.Icon
import androidx.compose.material3.IconButton
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable
import androidx.compose.ui.text.input.PasswordVisualTransformation

var password by rememberSaveable { mutableStateOf("") }
var passwordHidden by rememberSaveable { mutableStateOf(true) }
TextField(
    value = password,
    onValueChange = { password = it },
    singleLine = true,
    label = { Text("Enter password") },
    visualTransformation =
    if (passwordHidden) PasswordVisualTransformation() else VisualTransformation.None,
    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Password),
    trailingIcon = {
        IconButton(onClick = { passwordHidden = !passwordHidden }) {
            val visibilityIcon =
                if (passwordHidden) Icons.Filled.Visibility else Icons.Filled.VisibilityOff
            // Please provide localized description for accessibility services
            val description = if (passwordHidden) "Show password" else "Hide password"
            Icon(imageVector = visibilityIcon, contentDescription = description)
        }
    }
)
```

### 8) Keyboard Hiding

```kotlin
import androidx.compose.foundation.text.KeyboardActions
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.material3.Text
import androidx.compose.material3.TextField
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.saveable.rememberSaveable

val keyboardController = LocalSoftwareKeyboardController.current
var text by rememberSaveable { mutableStateOf("") }
TextField(
    value = text,
    onValueChange = { text = it },
    label = { Text("Label") },
    keyboardOptions = KeyboardOptions(imeAction = ImeAction.Done),
    keyboardActions = KeyboardActions(
        onDone = {
            keyboardController?.hide()
            // do something here
        }
    )
)
```
