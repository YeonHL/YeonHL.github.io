---
title: "[RPI/Python] 인증번호 생성"
excerpt: "인증번호 로직을 위한 랜덤 번호 생성"

categories:
  - Face Recognition Server
tags:
  - Python Random

last_modified_at: 2023-06-12T22:55:00-05:00
---
## 구현 이유
- 비즈니스 로직에서 요청에 따라 간단한 인증번호를 제공해야 하는 요구 사항이 발생했다.
- 본 서비스는 별도의 폐쇄망 환경에 구현되어 요청이 들어올 때마다 대응하기엔 번거로움이 있었다.
- 특정 기간 동안 사용할 인증 번호를 미리 생성하여 관리하고, 요청이 들어오면 해당 인증번호를 제공하는 방법으로 구현했다.

## 구현 과정
### 근무일 체크
```python
import holidays

kor_holiday = [str(day) for day in holidays.KOR(years=range(2023,2025)).keys()]
```
- `pip install holidays`로 설치할 수 있다. 공휴일에는 인증번호를 제공하지 않아도 무방하여 이를 체크하기 위해 사용했다.
- `holidays.KOR(years=(2023,2025)).keys()`은 한국의 공휴일 날짜의 `datetime.date` 타입을 key로 갖는다. 이를 `str`로 변환하여 `kor_holiday` 명칭의 리스트로 저장했다.

```python
import pandas as pd

date_list = [str(day).split(" ")[0] for day in pd.date_range(start="2023-06-12",end="2025-06-12",freq='B')]
```
- `pd.date_range`를 사용하여 날짜들을 리스트로 생성한다. freq='B'`는 주말이 아닌 평일만 반환한다.
- 시간까지 함께 저장된다. `split`을 사용하여 날짜 정보만 저장했다.

```python
work_day = [day for day in date_list if day not in kor_holiday]
```
- `date_list`에서 `kor_holiday`에 포함되지 않은 날짜만 저장하여 근무일을 추출했다.

### 일 단위 인증번호 생성
```python
import random

day_number_count = 30
number_length = 6


day_number_list = []
for count in range(day_number_count):
    day_number_set = set()
    while len(day_number_set) != len(work_day):
        day_number_set.add(str(random.randint(0, 10**number_length-1)).zfill(number_length))
    day_number_list.append(list(day_number_set))
```
- 매 날짜마다 랜덤한 6자리의 인증번호 30개를 생성하려면 위처럼 코드를 작성할 수 있다. 
- 중복을 방지하기 위해 `set`를 사용한다. `work_day`의 길이와 수가 같아질 때까지 인증번호를 생성하여 `day_number_set`에 추가한다.
- `randint`로 0부터 `number_length` 자리의 정수 중 하나를 생성한다. 부족한 자리 수는 `zfill`로 0을 채워서 맞춘다.
- `day_number_count`만큼 반복하여 `work_day` 만큼의 인증번호를 `day_number_count` 수만큼 생성한다.

```python
import os

try:
    os.makedirs("./numbers")
except FileExistsError:
    pass

for count, day_number in enumerate(day_number_list, 1):
    filename = f"./numbers/day_number{count}.txt"
    with open(filename, "w") as f:
        f.write("\n".join(day_number))
```
- 생성한 일 단위 인증번호를 텍스트 파일로 저장했다.
- 텍스트 파일을 저장할 디렉터리가 없을 경우 생성하도록 코드를 작성했다.

### 주 단위 인증번호 생성
```python
from datetime import datetime, timedelta


current_date = datetime.strptime(current_date, "%Y-%m-%d")
    previous_date = datetime.strptime(previous_date, "%Y-%m-%d")

    return current_date - timedelta(days=current_date.weekday()) == previous_date - timedelta(days=previous_date.weekday())
```
- 공휴일이 있는 주가 존재하므로 모든 주가 5일의 근무일을 갖지 않는다. 연속된 두 근무일을 입력 받아서 같은 주에 속하는지 확인하는 함수를 만들었다.
- `strptime`으로 `str`을 `datetime` 객체로 변환했다.
- 두 날짜의 월요일이 며칠인지 구했을 때 같은 날짜인지의 여부로 확인했다. 요일 정보를 `timedelta`로 변환하고 입력한 날짜에서 빼서 구할 수 있다. (월요일부터 일요일까지 0~6의 숫자로 나타난다.)

```python
week_number_count = 10
week_number_list = []

for count in range(week_number_count):
    week_number_set = set()
    while len(week_number_set) != len(work_day):
        week_number_set.add(str(random.randint(0, 10 ** number_length - 1)).zfill(number_length))
    week_number_list.append(list(week_number_set))
```
- `number_length`는 일 단위 인증번호가 동일하게 사용했다.
- 위 코드를 실행하여 일 단위 인증번호가 동일하게 생성한다.

```python
for count in range(week_number_count):
    number = week_number_list[count][0]
    week_number = [number]

    for order in range(1,len(work_day)):
        if not is_same_week(work_day[order], work_day[order-1]):
            number = week_number_list[count][order]
        week_number.append(number)

    week_number_list[count] = week_number
```
- 각 날짜에 인증번호를 할당하는 방식은 동일하다.
- 생성했던 `number_list`의 첫 인증번호는 유지한다. 다음 날짜부터 같은 주인지 확인하고, 같은 주라면 이전 인증번호를 그대로 추가한다.
- 다른 주라면 `number`를 바뀐 주의 첫 인증번호로 변경한다. 이후 그 다음주가 될 때까지 같은 인증번호를 추가한다.
- `week_number_count` 수만큼 이를 반복하여 일주일 단위로 사용 가능한 인증번호를 추가한다.

```python
for count, week_number in enumerate(week_number_list, 1):
    filename = f"numbers/week_number{count}.txt"
    with open(filename, "w") as f:
        f.write("\n".join(week_number))
```
- 주 단위 인증번호도 마찬가지로 텍스트 파일로 저장한다.

### 통합 인증번호 생성
```python
total_number_list = []

for count in range(1, day_number_count + 1):
    filename = f"numbers/day_number{count}.txt"
    with open(filename, 'r') as file:
        lines = file.readlines()
        lines = [line.strip() for line in lines]
    total_number_list.append(lines)

for count in range(1, week_number_count + 1):
    filename = f"numbers/week_number{count}.txt"
    with open(filename, 'r') as file:
        lines = file.readlines()
        lines = [line.strip() for line in lines]
    total_number_list.append(lines)
    
total_number_series = pd.Series(total_number_list)
```
- 일 단위와 주 단위 비밀번호를 통합하여 `total_number_series` 이름의 Pandas Series로 변환하였다.

```python
number_data_frame = pd.concat([pd.Series(work_day)] + 
                              [pd.Series(total_number_series[index]) 
                               for index in range(day_number_count + week_number_count)],
                              axis=1)
```
- `work_day`의 날짜를 행 이름으로 하고 `total_number_series`의 내용을 그 내용으로 하여 Pandas Dataframe을 생성했다.

```python
column_name = {0: "work_day"}

for index in range(1, day_number_count + 1):
    column_name[index] = f'day_number{index}'

for index in range(day_number_count + 1, day_number_count + week_number_count + 1):
    column_name[index] = f'week_number{index - day_number_count}'

number_data_frame = number_data_frame.rename(columns=column_name)
```
- 각 열의 이름을 저장한 Dictionary를 생성하여 `rename`으로 이름을 설정했다.
- 이름은 `day_number`, `week_number`에 숫자를 붙여서 사용했다.

### 파일로 저장하기
#### CSV
```python
number_data_frame.to_csv('number_data_frame.csv', index=False)
```
- `to_csv` 명령어로 작성한 Dataframe을 csv 파일로 저장할 수 있다.

#### Pickle
```python
result = [
    [total_number_list[j][i] for j in range(day_number_count + week_number_count)]
    for i in range(len(work_day))
]

data = dict(zip(work_day, result))
```
- pickle 형태로 저장하기 위해 행과 열을 변환했다.

```python
import pickle

with open("number_data_frame.pickle", "wb") as f:
    f.write(pickle.dumps(data))
``` 

- 구현한 함수를 활용하여 pickle 파일 형태로 저장했다.

```python
with open("number_data_frame.pickle", 'rb') as f:
    data = pickle.load(f)
```
- 저장한 pickle의 데이터는 위 코드로 불러올 수 있다.


## 결과 및 느낀 점
- 폐쇄망이 아니였다면 요청이 들어올 때마다 발급해줄 수 있었겠지만, 폐쇄망 환경이기에 부득이하게 미리 발급해두는 구조를 사용하였다.
- 암호화가 필요한 번호가 아니라서 따로 처리하지 않았지만 만약 보안이 필요하다면 패스워드에 암호화 절차를 한번 더 적용하면 좋을 것 같다.
- 인증번호를 저장하는 방법도 파일이 아니라 DB 등 다른 장소에 저장한다면 관리가 더 용이할 것 같다.


## 참고
[Holidays Github](https://github.com/vacanza/python-holidays/)