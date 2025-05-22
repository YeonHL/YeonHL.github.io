---
title: 파이썬 프로젝트 시작하기
description: 파이썬 프로젝트를 시작하면서 의존성 추가 등 환경을 설정하는 과정
date: 2025-05-02 22:19:00+0900
lastmod: 2025-05-22
slug: setting-python-project
comments: true
math: false
categories:
  - Python
tags:
  - uv
  - pytest
  - ruff
  - pre-commit
keywords:
  - uv
  - pytest
  - ruff
  - pre-commit
---
> **수정 내용**
> - **2025-05-22**: `pre-commit`에 `uv-export` hook 추가

파이썬 프로젝트 환경을 설정하면서, 의존성 관리 및 코드 품질 개선을 위해 아래의 도구들을 적용했습니다.

## 프로젝트 관리 도구

프로젝트를 설정할 때 사용한 도구들입니다.

### 파이썬 버전 & 의존성 관리: `uv`

이전에는 파이썬 버전 관리로 `pyenv`를, 의존성 관리로 `poetry`를 사용했습니다. 하지만 최근 uv의 기능이 많이 업데이트 되면서 `uv`의 아래 장점을 계기로 사용을 결정했습니다:

- **Python 버전 관리와 의존성 관리 통합**: Python 버전 관리 및 고정 기능을 자체적으로 제공합니다. `.python-version` 파일을 수정하면 자동으로 적용합니다.
- **글로벌 캐시를 통한 종속성 중복 제거**: 전역 캐시를 제공하여 여러 프로젝트에서 같은 종속성 패키지를 사용한다면 이를 활용합니다. 중복 설치를 방지하여 디스크 공간을 절약합니다.
- **간편한 설치**: Python이나 Rust에 대한 종속성이 없습니다. `curl` 혹은 `pip`로 설치할 수 있습니다.

`pyenv` 없이 단일 도구로 사용할 수 있다는 것이 사용하는 계기가 되었고, 글로벌 캐시 기능이 특히 편리했습니다. 이외에도 빠른 속도나 `pip` 호환성 등의 장점이 있습니다.

설치는 OS에 따라 콘솔에서 아래 명령어를 실행합니다:

```sh
# Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

`uv`는 대규모 프로젝트 지원을 위해 여러 프로젝트의 의존성을 한번에 관리하고 공통 모듈을 쉽게 참조할 수 있는 **워크스페이스** 기능을 제공하는데, 이는 기회가 있을 때 사용 후 포스트로 남기겠습니다.

### 테스트: `pytest`

테스트 코드 작성에는 `pytest`를 사용하고 있습니다.
Python에서 기본으로 제공하는 `unittest`와 비교했을 때 클래스나 아닌 함수 단위의 작성과 같은 간결한 작성이나 `assert` 자동 메시지 등의 추가 기능에서 편리함을 느꼈습니다.
테스트 코드 작성의 편의성을 위한 린트는 하단의 `ruff`에서 함께 적용했습니다.

### 린트 & 포맷터: `ruff`

이전에는 `flake8` + `isort` + `black`의 조합을 사용했습니다. 그리고 보안 취약점 정적 분석 도구로 `bandit`을 사용했습니다.
하지만 `ruff`의 도구로 위 도구들을 통합하여 사용할 수 있음에 편리함을 느꼈고, 현재는 `ruff`를 주로 사용하고 있습니다.
규칙 적용은 그대로 가져왔으며, 보완할 점을 반영하고 있습니다.

Ruff의 규칙 설정은 별도로 분리하여 관리하기 위해 `pyproject.toml`이 아닌 `ruff.toml` 파일에 작성했습니다. 아래와 같이 사용하고 있습니다:

`ruff.toml`

```toml
line-length = 88
fix = true

[lint]
select = [
    "A", # flake8-builtins
    "ANN", # flake8-annotations
    "ARG", # flake8-unused-arguments
    "ASYNC", # flake8-async
    "B", # flake8-bugbear
    "C4", # flake8-comprehensions
    "DTZ", # flake8-datetimez
    "E", # pycodestyle Error
    "EM", # flake8-errmsg,
    "F", # Pyflakes
    "FBT", # flake8-boolean-trap
    "FURB", # refurb
    "G", # flake8-logging-format
    "I", # isort
    "ICN", # flake8-import-conventions
    "ISC", # flake8-implicit-str-concat
    "LOG", # flake8-logging
    "N", # pep8-naming
    "PERF", # Perflint
    "PIE", # flake8-pie
    "PLC", # Pylint Convention
    "PLE", # Pylint Error
    "PLR", # Pylint Refactor
    "PLW", # Pylint Warning
    "PT", # flake8-pytest-style
    "PTH", # flake8-use-pathlib
    "RET", # flake8-return
    "RSE", # flake8-raise
    "RUF", # Ruff-specific rules
    "S", # flake8-bandit
    "SIM", # flake8-simplify
    "SLF", # flake8-self
    "SLOT", # flake8-slots
    "TC", # flake8-type-checking
    "TRY", # tryceratops
    "UP", # pyupgrade
    "W", # pycodestyle Warning
]
ignore = [
    "W191", # tab-indentation
    "E111", # indentation-with-invalid-multiple
    "E114", # indentation-with-invalid-multiple-comment
    "E117", # over-indented
    "D206", # docstring-tab-indentation
    "D300", # triple-single-quotes
    "ISC002", # multi-line-implicit-string-concatenation
]

[format]
line-ending = "lf"
docstring-code-format = true
docstring-code-line-length = "dynamic"
```

각 항목은 다음의 특징을 가집니다.
아래는 기존에 사용하던 규칙입니다:

- `E`, `W`: PEP8 규칙 준수, 코드 스타일을 준수하여 가독성 향상
- `F`: Pyflakes, 코드 품질 개선, 중복이나 복잡도 등을 관리
- `I`: `isort`, import 순서 자동 정렬
- `S`, `bandit`, 보안 취약점 탐지

아래의 규칙은 이번에 설정하면서 새로 적용하는 규칙입니다:

**코드 품질 개선**

- `ARG`: 사용되지 않는 인자 개선
- `B`: PEP8의 권고 사항 적용
- `C4`: 컬렉션 리터럴 및 표현식의 일관된 포맷
- `FBT`: Boolean을 통한 함수 기능 처리 (The Boolean Trap 안티 패턴) 대신 명확한 동작을 수행하는 함수로 구현
- `FURB`: 코드 품질 개선
- `ISC`: 문자열 표현 개선
- `PIE`: 불필요한 표현 제거
- `PLC`, `PLE`, `PLR`, `PLW`: Pylint, 코드 품질 개선
- `RET`: 반환 로직 개선
- `RUF`: Ruff 규칙, 코드 품질 개선
- `SIM`: 불필요하게 복잡한 표현 단순화
- `UP`: 파이썬 최신 버전 문법 적용

**오류 방지**

- `A`: 파이썬 내장 객체명 재정의 경고
- `SLF`: `_private` 멤버 접근 방지를 통한 캡슐화

**성능 개선**

- `PERF`: 비효율적인 코드의 성능 최적화
- `SLOT`:불변 타입의 `__slot__`를 정의하여 메모리 최적화

**이름 관련**

- `ICN`: 라이브러리의 주요 약어 적용 (`import numpy as np` 등)
- `N`: PEP8의 이름 규칙 적용

**타입 관련**

- `ANN`: 타입 힌트 명시
- `TC`: 타입 검사 강화, 타입 오류 방지

**예외 관련**

- `EM`: 예외 메시지 가독성 개선
- `RSE`: 예외의 불필요한 괄호 개선
- `TRY`: 예외 처리 개선

**로그 관련**

- `G`: 로깅 레벨, 메시지 포맷 등 표준화
- `LOG`: 로그 관련 코드 품질 개선

**Docstring 관련**

- `D`, `DOC`: Docstring 개선

**라이브러리 관련**

- `ASYNC`: 비동기 작성 시 발생할 수 있는 오류 확인
- `DTZ`: Datetime 실수 방지
- `PT`: Pytest 개선
- `PTH`: `os` 대신 `Path`를 사용하여 불필요한 OS 접근 제한

Ruff에서는 `F` 규칙과 일부 `E` 규칙을 기본 설정으로 가집니다. `F`와 `E` 및 기존에 사용하던 `I`와 `S`까지는 그대로 적용했으며, 이외의 규칙은 지켰을 때 코드 퀄리티에 도움이 될 것이라 기대되는 규칙들을 추가했습니다. 해당 규칙들은 새로 적용했기에 사용하면서 충돌이 발생하는지 확인할 계획입니다.

> 규칙 (`lint`의 `select`)에 `ALL`을 추가하면 모든 규칙이 적용됩니다. 단, 규칙 간 충돌이 발생할 수 있고, 새롭게 업데이트 된 규칙이 자동 적용되면서 발생하는 문제도 있기에 원하는 규칙만 직접 적용했습니다.

`ignore`에는 Ruff에서 사용하지 않는 것을 권장하는 규칙들을 기본으로 추가했습니다.

> 개인적으로 권장하는 규칙은 최대한 따르는 것이 향후 업데이트로 인한 충돌을 최소화할 수 있다고 생각합니다.

포맷터는 Windows에서 작업할 때 Linux와 충돌하는 것을 막기 위해 `lf`를 명시했습니다. 그리고 `docstring`도 포맷팅을 적용하도록 작성했고, 행 길이 제한도 적용하는 것으로 설정했습니다.

> 직접 규칙을 설정하려면 아래의 공식 가이드라인을 참조하세요:
> 
> - 룰 세트를 명시적으로 만들기 위해 `lint.extend-select` 대신 `lint.select`를 선호하세요.
> - `ALL`은 신중하게 사용하세요. `ALL`을 활성화하면 업그레이드할 때마다 새로운 룰이 암시적으로 활성화됩니다.
> - 작은 룰 세트(`select = ["E", "F"]`)로 시작하여 카테고리를 하나씩 추가하세요. 예를 들어, 인기 있는 flake8-bugbear 확장을 활성화하기 위해 `select = ["E", "F", "B"]`로 확장할 수 있습니다.


#### VSCode 적용

저는 IDE로 VSCode를 주로 사용합니다. 앞에서 설정한 Ruff는 [Ruff - Visual Studio Marketplace](https://marketplace.visualstudio.com/items/?itemName=charliermarsh.ruff) 플러그인을 설치하여 사용하고 있습니다.

제가 적용한 파이썬 관련 설정은 아래와 같습니다:

```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.ruff": "explicit",
      "source.organizeImports.ruff": "explicit"
    },
    "editor.defaultFormatter": "charliermarsh.ruff"
  },
  "python.analysis.typeCheckingMode": "basic",
  "python.analysis.extraPaths": ["${workspaceFolder}"],
  "python.languageServer": "Pylance",
  "python.testing.cwd": "${workspaceFolder}",
  "python.testing.autoTestDiscoverOnSaveEnabled": true,
  "python.testing.pytestEnabled": true,
  "python.testing.pytestArgs": ["tests"],
  "python.testing.unittestEnabled": false,
  "ruff.configuration": "ruff.toml"
}
```

### Pre-commit

`ruff`가 잘 적용됐는지 확인하면서, 특정 규칙을 커밋 단계에서 추가로 적용하기 위해 `pre-commit`을 적용했습니다:

`.pre-commit-config.yaml`

```yaml
# uv
  - repo: https://github.com/astral-sh/uv-pre-commit
    rev: 0.7.6
    hooks:
      - id: uv-export
        args:
          - --no-dev
          - --format=requirements.txt
          - --output-file=requirements.txt

# Ruff
- repo: https://github.com/astral-sh/ruff-pre-commit
  rev: v0.11.8
  hooks:
    - id: ruff
      args:
        - --fix
        - --select=ERA # 주석 처리된 코드 감지

    - id: ruff-format

# mdformat
- repo: https://github.com/executablebooks/mdformat
  rev: 0.7.22
  hooks:
    - id: mdformat
      additional_dependencies:
        - mdformat-ruff
        - ruff==v0.11.8
```

아래 규칙을 추가로 적용했습니다:

- `ERA`: 주석 처리된 코드 감지, 로컬 개발에서는 주석 처리를 할 수도 있지만 Git에 이를 그대로 올리는 것은 가독성에 좋지 않기에 지양해야 한다고 생각하여 적용했습니다.

Python 코드 외에 저장소 내부에 작성한 Markdown 문서에 대해 린트를 적용하도록 `mdformat`을 추가했습니다. 이번에 설정하면서 새로 적용하는 훅이기에 저에게 적합한지 확인 후 이상이 없으면 계속 사용하려 합니다.

## 적용 과정

설정한 도구들을 프로젝트에 적용하는 것은 간단합니다.

사용하려는 파이썬 버전을 설정합니다. 사용 가능한 파이썬 버전은 아래 명령어로 확인합니다:

```sh
uv python list
```

설치는 아래 명령어로 수행합니다. 버전을 입력하면 해당 버전으로 설치하고 작성하지 않으면 최신 버전을 설치합니다:

```sh
uv python install  # 3.13, 3.13.3 등
```

프로젝트의 파이썬 버전 지정은 아래 명령어로 수행합니다:

```sh
uv python pin {Python 버전}
```

프로젝트를 처음 시작하는 경우 `uv`를 사용하여 프로젝트를 초기화합니다:

```sh
uv init {하위 폴더에 시작하고 싶은 경우 폴더 이름}
```

이후 프로젝트 폴더에서 의존성을 추가합니다. 개발 과정에만 사용하는 라이브러리이므로 `--dev`를 붙여 개발용 의존성으로 추가했습니다:

```sh
uv add --dev pytest ruff pre-commit
```

`pre-commit`를 활성화합니다:

```
pre-commit install
```

## 마치며

오랜만에 파이썬 프로젝트 설정을 업데이트하는 시간을 가졌습니다. 개발 도구가 빠르게 발전하고 있음을 느낄 수 있었고, 새로운 도구들을 적용하고 익숙해져서 습관이 된다면 제가 작성한 코드 퀄리티도 더 나아질 것으로 기대합니다.

> **참조**
>
> - [uv](https://docs.astral.sh/uv/)
> - [pytest documentation](https://docs.pytest.org/en/stable/)
> - [Ruff](https://docs.astral.sh/ruff/)
> - [pre-commit](https://pre-commit.com/)
