---
title: "[Git] 커밋 컨벤션"
excerpt: "프로젝트 참여자들이 일관된 형식의 커밋 메시지를 작성하기 위한 규칙을 말하며, 간단히 줄여서 Git Commit Convention 이라고도 합니다. 현재 여러 개발자들 사이에서 관습적으로 통용되는 가이드라인이 있지만, 각 프로젝트에 따라서 별도의 규칙을 만들어 적용하기도 합니다."

categories:
  - Git
tags:
  - DevOps
  - Control-Version
  - Git
  - Git-Commit

last_modified_at: 2024-09-27T09:00:00-05:00
---
- 프로젝트 참여자들이 일관된 형식의 커밋 메시지를 작성하기 위한 규칙을 말하며, 간단히 줄여서 Git Commit Convention 이라고도 합니다.
- 현재 여러 개발자들 사이에서 관습적으로 통용되는 가이드라인이 있지만, 각 프로젝트에 따라서 별도의 규칙을 만들어 적용하기도 합니다.

## 필요성

**가독성 및 커뮤니케이션 효율성 제고**
- 정해진 규칙에 따라 커밋 메시지를 기재함으로써 프로젝트의 가독성과 유지 보수성을 향상할 수 있습니다.
- 코드 리뷰 및 버그 수정 과정에서 불필요한 의사소통 과정을 간소화하여, 프로젝트 관리에 들어가는 시간도 줄일 수 있습니다.

**변경 이력 추적 및 문제 해결 속도 향상**
- 자동화된 도구를 사용하여 릴리즈 노트를 생성하거나 버전 관리를 적용할 때, Git 커밋 컨벤션을 지키면 일관된 커밋 메시지를 통해 소스 변경 이력을 효율적으로 추적할 수 있습니다.
- 문제 발생 시 더 빠르게 원인을 찾아 수정할 수 있으며, 전반적인 프로젝트 안정성을 높일 수 있습니다.

## Udacity

### 메시지 구조

```
type: [#issueNumber]Subject  # 제목
(Blank line)
body(옵션) # 본문
(Blank line)
footer(옵션) # 꼬리말
```

#### type (Required)
Commit 의 유형을 나타냅니다. ": " 로 Subject 와 구별합니다.

| 타입       | 설명                                                                                   |
| -------- | ------------------------------------------------------------------------------------ |
| feat     | 새로운 기능을 추가                                                                           |
| fix      | 버그 수정                                                                                |
| docs     | 문서를 수정한 경우                                                                           |
| style    | 코드 포맷 변경, 세미 콜론 누락, 코드 수정이 없는 경우                                                     |
| refactor | production 코드 리팩토링                                                                   |
| test     | 테스트 추가, 테스트 리팩토링 (test 폴더 내부의 변경이 일어난 경우에만 해당)                                       |
| chore    | 빌드 프로세스, 패키지 매니저 수정, 도구 설정 변경 등 기타 작업 (package.json 의 변경이나 dotenv 의 요소 변경 등, 모듈의 변경) |
위 표에 해당하는 커밋 메시지는 해당 타입을 사용하는 것을 권장드립니다.

| 태그 이름            | 설명                          |
| ---------------- | --------------------------- |
| design           | CSS 등 사용자 UI 디자인 변경         |
| !BREAKING CHANGE | 커다란 API 변경                  |
| !HOTFIX          | 급하게 치명적인 버그를 고쳐야하는 경우       |
| comment          | 필요한 주석 추가 및 변경              |
| rename           | 파일 혹은 폴더명을 수정하거나 옮기는 작업만 수행 |
| remove           | 파일을 삭제하는 작업만 수행             |

위 표에 해당하는 커밋 메시지가 있을 경우 해당 타입을 사용하는 것을 고려할 수 있습니다.

- Feat, Fix, Design,!BREAKING CHANGE는 기능 태그에 해당합니다.
- Style, Refactor, Comment는 개선 태그에 해당합니다.
- Docs, Test, Chore, Rename, Remove는 그 외 태그에 해당합니다.

#### issueNumber (Optional)
해당되는 이슈 번호를 나타냅니다.

#### subject (Required)
- Commit Message 의 제목, 간단, 명확한 변경 내용을 설명합니다.
- 50 자 이내로 작성하며, 대문자로 시작하고 마침표를 사용하지 않습니다.
- 과거 시제를 사용하지 않고 명령문으로 작성합니다.
  "Fixed" --> "Fix"
  "Added" --> "Add"
- 한글로 작성할 경우에도 " 고침 ", " 추가 ", " 변경 " 의 명령어로 시작할 수 있습니다.

#### body (Optional)
- Commit 의 상세한 변경 내용을 설명합니다.
- 무엇을, 왜 변경했는지를 작성합니다.
- 부연설명이 필요하거나 커밋의 이유를 설명할 경우 최대한 상세히 작성합니다.
- 각 줄은 72 자를 넘기지 않고 제목과 구분되기 위해 한칸을 띄워 작성하는 것을 권장드립니다.

#### footer (Optional)
- 관련 이슈나 패치를 참조하는 데 사용됩니다.
- 이슈 트래커의 ID 를 명시하거나, 이전 커밋과 관련된 내용을 작성할 수 있습니다.
- 특정 작업이나 이슈를 해결한 경우에는 일반적으로 "Closes # 작업번호 또는 이슈번호 " 같은 형태로 기재합니다.
  ex) Fixes: #45 Related to: #34, #23

### 예시
```
feat: Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequenses of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

## Google / Angular
### 메시지 구조

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

#### type (Required)
- Commit 의 유형을 나타냅니다.

| 타입       | 설명                            |
| -------- | ----------------------------- |
| feat     | 새로운 기능을 추가                    |
| fix      | 버그 수정                         |
| docs     | 문서 변경                         |
| style    | 코드 스타일 변경 (포매팅 수정, 세미콜론 추가 등) |
| refactor | 코드 리팩토링                       |
| test     | 테스트 코드 추가, 수정                 |
| chore    | 빌드 프로세스, 도구 설정 변경 등 기타 작업     |

#### scope (Optional)
- 변경된 범위나 컴포넌트를 나타냅니다.
- 어떤 모듈, 서비스 또는 컴포넌트에 해당하는지를 명시할 수 있습니다.

#### subject (Required)
- Commit Message 의 제목, 간단, 명확한 변경 내용을 설명합니다.
- 50 자 이내로 작성하며, 대문자로 시작하고 마침표를 사용하지 않습니다.

#### body (Optional)
- Commit 의 상세한 변경 내용을 설명합니다.
- 각 줄은 72 자 이내로 작성하며, 어떻게 변경되었는지, 왜 변경되었는지 설명합니다.

#### footer (Optional)
- 관련 이슈나 패치를 참조하는 데 사용됩니다.
- 이슈 트래커의 ID 를 명시하거나, 이전 커밋과 관련된 내용을 작성할 수 있습니다.

### 예시
```
feat(auth): add login functionality # <type> (<scope>): <subject>

Add new login functionality to the authentication service. This allows users to log in using their email and password. # <body>

Closes #1234 # <footer>
```

## 이모지 (Optional)
메시지에 이모지를 추가하고 싶을 경우 아래와 같은 이모지가 사용되기도 합니다.

| Emoji | Description                                                                                                   |
| ----- | ------------------------------------------------------------------------------------------------------------- |
| 🎨    | 코드의 형식 / 구조를 개선 할 때                                                                               |
| 📰    | 새 파일을 만들 때                                                                                             |
| 📝    | 사소한 코드 또는 언어를 변경할 때                                                                             |
| 🐎    | 성능을 향상시킬 때                                                                                            |
| 📚    | 문서를 쓸 때                                                                                                  |
| 🐛    | 버그 reporting 할 때, [@FIXME](https://github.com/slashsbin/styleguide-todo-grammar#bug-report) 주석 태그 삽입 |
| 🚑    | 버그를 고칠 때                                                                                                |
| 🐧    | 리눅스에서 무언가를 고칠 때                                                                                   |
| 🍎    | Mac OS 에서 무언가를 고칠 때                                                                                   |
| 🏁    | Windows 에서 무언가를 고칠 때                                                                                  |
| 🔥    | 코드 또는 파일 제거할 때, @CHANGED 주석 태그와 함께                                                          |
| 🚜    | 파일 구조를 변경할 때. 🎨과 함께 사용                                                                        |
| 🔨    | 코드를 리팩토링 할 때                                                                                         |
| ☔️   | 테스트를 추가 할 때                                                                                           |
| 🔬    | 코드 범위를 추가 할 때                                                                                        |
| 💚    | CI 빌드를 고칠 때                                                                                             |
| 🔒    | 보안을 다룰 때                                                                                                |
| ⬆️    | 종속성을 업그레이드 할 때                                                                                     |
| ⬇️    | 종속성을 다운 그레이드 할 때                                                                                  |
| ⏩    | 이전 버전 / 지점에서 기능을 전달할 때                                                                         |
| ⏪    | 최신 버전 / 지점에서 기능을 백 포트 할 때                                                                     |
| 👕    | linter / strict / deprecation 경고를 제거 할 때                                                               |
| 💄    | UI / style 개선시                                                                                             |
| ♿️   | 접근성을 향상시킬 때                                                                                          |
| 🚧    | WIP (진행중인 작업) 에 커밋, @REVIEW 주석 태그와 함께 사용                                                     |
| 💎    | New Release                                                                                                   |
| 🔖    | 버전 태그                                                                                                     |
| 🎉    | Initial Commit                                                                                                |
| 🔈    | 로깅을 추가 할 때                                                                                             |
| 🔇    | 로깅을 줄일 때                                                                                                |
| ✨    | 새로운 기능을 소개 할 때                                                                                      |
| ⚡️   | 도입 할 때 이전 버전과 호환되지 않는 특징, @CHANGED 주석 태그 사용                                            |
| 💡    | 새로운 아이디어, @IDEA 주석 태그                                                                              |
| 🚀    | 배포 / 개발 작업 과 관련된 모든 것                                                                            |
| 🐘    | PostgreSQL 데이터베이스 별 (마이그레이션, 스크립트, 확장 등)                                                  |
| 🐬    | MySQL 데이터베이스 특정 (마이그레이션, 스크립트, 확장 등)                                                     |
| 🍃    | MongoDB 데이터베이스 특정 (마이그레이션, 스크립트, 확장 등)                                                   |
| 🏦    | 일반 데이터베이스 별 (마이그레이션, 스크립트, 확장명 등)                                                      |
| 🐳    | 도커 구성                                                                                                     |
| 🤝    | 파일을 병합 할 때                                                                                             |
