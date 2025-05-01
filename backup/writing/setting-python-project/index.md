---
title: 파이썬 프로젝트 시작하기
description: 파이썬 프로젝트를 시작하면서 의존성 추가 등 환경을 설정하는 과정
date: 2025-04-28 22:19:00+0900
lastmod: 2025-04-28
slug: setting-python-project
comments: true
math: false
categories:
  - Python
tags:
  - uv
  - pytest
  - ruff
  - bandit
keywords:
  - uv
  - pytest
  - ruff
  - bandit
---

파이썬 프로젝트 환경을 설정하면서 적용했던 도구들과 그 이유에 대해 작성했습니다.
파이썬 버전 충돌 등의 문제가 없다면 아래 도구들을 기본으로 사용하려고 합니다.

## 프로젝트 관리 도구

프로젝트를 설정할 때 사용한 도구들입니다.

### 파이썬 버전 & 의존성 관리: `uv`

이전에는 파이썬 버전 관리로 `pyenv`를, 의존성 관리로 `poetry`를 사용했습니다. 하지만 최근 uv의 기능이 많이 업데이트 되면서 `uv`의 아래 장점을 계기로 사용을 결정했습니다:

- **Python 버전 관리와 의존성 관리 통합**: Python 버전 관리 및 고정 기능을 자체적으로 제공합니다. `.python-version` 파일을 수정하면 자동으로 적용합니다.
- **글로벌 캐시를 통한 종속성 중복 제거**: 전역 캐시를 제공하여 여러 프로젝트에서 같은 종속성 패키지를 사용한다면 이를 활용합니다. 중복 설치를 방지하여 디스크 공간을 절약합니다.
- **간편한 설치**: Python이나 Rust에 대한 종속성이 없습니다. `curl` 혹은 `pip`로 설치할 수 있습니다.

특히 pyenv 없이 단일 도구로 사용할 수 있다는 것이 사용하는 계기가 되었고, 글로벌 캐시에서 큰 편리함을 느꼈습니다. 그 외의 빠른 속도나 `pip` 호환성 등의 장점이 있습니다.

설치는 OS에 따라 콘솔에서 아래 명령어를 실행합니다:

```sh
# Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

이외에 대규모 프로젝트 지원을 위해 여러 프로젝트의 의존성을 한번에 관리하고 공통 모듈을 쉽게 참조할 수 있는 워크스페이스 기능을 제공하는데, 이는 기회가 있을 때 사용 후 포스트로 남기겠습니다.

### 테스트: `pytest`

테스트 코드 작성에는 `pytest`를 사용하고 있습니다. 
Python에서 기본으로 제공하는 `unittest`와 비교했을 때 클래스나 아닌 함수 단위의 작성과 같은 간결한 작성이나 `assert` 자동 메시지 등의 추가 기능에서 편리함을 느꼈습니다.

### 린트 & 포맷터: `ruff`

이전에는 `flake8` + `isort` + `black`의 조합을 사용했습니다. 그리고 보안 취약점 정적 분석 도구로 `bandit`을 사용했습니다. 
하지만 `ruff`의 도구로 위 도구들을 통합하여 사용할 수 있음에 편리함을 느꼈고, 현재는 `ruff`를 주로 사용하고 있습니다.
규칙 적용은 그대로 가져왔으며, 보완할 점을 반영하고 있습니다.

Ruff의 규칙 설정은 별도로 분리하여 관리하기 위해 `pyproject.toml`이 아닌 `ruff.toml` 파일에 작성했습니다. 아래와 같이 사용하고 있습니다:

```toml

```

`ignore`의 경우 Ruff에서 사용하지 않는 것을 권장하는 규칙들을 기본으로 추가했습니다.

> 위를 따르는 것은 도구를 사용한다면 권장하는 규칙을 준수하는 것이 향후 업데이트로 인한 충돌을 최소화할 수 있다고 생각하기 때문입니다.
> 프로젝트와 맞지 않는 규칙이 있다면 그때 제거를 고민하고, 일반적으로는 따르는 것을 선호합니다.

직접 규칙을 설정하려면 아래의 공식 가이드라인을 참조하세요:

- 룰 세트를 명시적으로 만들기 위해 `lint.extend-select` 대신 `lint.select`를 선호하세요.
- `ALL`은 신중하게 사용하세요. `ALL`을 활성화하면 업그레이드할 때마다 새로운 룰이 암시적으로 활성화됩니다.
- 작은 룰 세트(`select = ["E", "F"]`)로 시작하여 카테고리를 하나씩 추가하세요. 예를 들어, 인기 있는 flake8-bugbear 확장을 활성화하기 위해 `select = ["E", "F", "B"]`로 확장할 수 있습니다.


### Pre-commit




## 구현 과정

`uv`를 사용하여 프로젝트를 초기화 합니다:

```sh
uv init
```

이후 프로젝트 관리 도구들을 추가합니다. 개발 과정에서만 사용하고 실행 시에는 불필요한 라이브러리 이므로 `--dev`를 붙여 개발용 의존성으로 추가합니다.

```sh
uv add --dev pytest ruff
```




> **참조**
> - [uv](https://docs.astral.sh/uv/)
> - [pytest documentation](https://docs.pytest.org/en/stable/)
> - [Ruff](https://docs.astral.sh/ruff/)
> - [Welcome to Bandit — Bandit documentation](https://bandit.readthedocs.io/en/latest/index.html)
