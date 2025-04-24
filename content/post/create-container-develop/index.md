---
title: 컨테이너 개발 환경 만들기
description: Linux 환경에 Podman 기반 컨테이너 개발 환경 만들기
date: 2025-04-25 02:03:00+0900
lastmod: 2025-04-25
slug: create-container-development-environment
comments: true
math: false
categories:
  - Container
tags:
  - Podman
  - Kubernetes
keywords:
  - Container
  - Podman
  - Kubernetes
---

Linux에 컨테이너 개발 환경을 구축하기 위한 과정입니다. 먼저 어떤 컨테이너 개발 도구를 사용할지 고믾했습

## Docker vs Podman

컨테이너 개발, 배포 시 많은 개발자들에게 익숙한 도구는 Docker입니다. OCI 표준에도 매우 큰 영향을 줬을 정도로 사용자가 많고, 지금도 Kubernetes 환경에 배포하기 위한 컨테이너 개발 도구로 사용하기에 전혀 문제 없습니다.

Docker 대신 Podman을 선택해야 할 이유가 있을까요? 둘의 특징을 비교하면 다음과 같습니다:

| 기준               | Docker                                    | Podman                                                   |
| ------------------ | ----------------------------------------- | -------------------------------------------------------- |
| 아키텍처           | 데몬 기반(client-server, dockerd 필요)    | 데몬리스(daemonless), 각 컨테이너가 독립적 프로세스      |
| 성능 및 리소스     | 데몬 상주로 메모리/CPU 오버헤드 발생 가능 | 데몬 없으므로 리소스 사용량 적고 시작 속도 빠름          |
| 보안               | 데몬이 루트 권한으로 동작, 공격 표면 존재 | 루트리스 실행으로 공격 표면 최소화                       |
| Kubernetes 연동    | 별도 런타임(containerd 등) 필요           | Kubernetes와 직접 연동, YAML 생성 등 지원                |
| Pod 지원           | 기본적으로 컨테이너 단위 관리             | Pod 개념을 기본 지원, Kubernetes와 개념 일치             |
| CLI 호환성         | 표준 Docker CLI                           | Docker CLI와 호환, 명령어 거의 동일                      |
| 생태계/도구 호환성 | 가장 넓은 생태계, Docker Hub 등 풍부      | Docker 이미지 및 레지스트리 호환, Podman Compose 등 지원 |
| 시스템 통합        | systemd 직접 통합 불가(스크립트 필요)     | systemd 유닛 파일 직접 생성/통합 지원                    |

구체적으로 살펴보겠습니다.

### 아키텍처

Docker는 중앙 집중식 데몬(dockerd)에 의존하는 반면, Podman은 데몬리스 아키텍처를 채택했습니다. 이것은 매우 중요한 차이점으로, 여러 장점을 가져옵니다:

- **향상된 보안**: 데몬이 없으므로 루트 권한으로 실행되는 중앙 프로세스가 없어 공격 표면이 감소합니다. 컨테이너가 격리된 개별 프로세스로 실행됩니다.
- **자원 효율성**: 항상 실행 중인 백그라운드 데몬이 없어 <u>시스템 리소스 사용이 줄어듭니다.</u>
- **안정성 향상**: 중앙 데몬에 발생한 문제가 모든 컨테이너에 영향을 미치는 단일 장애점이 제거됩니다.

물론 데몬이 없기에 발생하는 단점도 존재합니다.

- **복잡성 증가**: Podman은 각 컨테이너가 별도의 프로세스로 실행되고, 중앙에서 모든 컨테이너 상태를 관리하는 데몬이 없기 때문에, 여러 컨테이너의 상태를 한 번에 모니터링하거나 관리하는 작업이 Docker에 비해 더 복잡할 수 있습니다. 이로 인해 Podman의 명령어나 운용 방식이 Docker보다 복잡하게 느껴질 수 있고, 학습 곡선이 더 가파를 수 있습니다.

- **커뮤니티 및 도구 지원의 한계**: Docker는 데몬 기반 구조 덕분에 다양한 서드파티 도구와의 통합이 쉽고, 생태계가 매우 넓습니다. 반면, Podman은 아직 커뮤니티와 도구 지원이 Docker만큼 성숙하지 않아, 특정 자동화나 모니터링 도구, 클라우드 서비스 등과의 연동에서 제약이 있을 수 있습니다.

- **고급 네트워킹 및 오케스트레이션 기능 부족**: Podman의 네트워킹 기능은 Docker에 비해 아직 미성숙한 부분이 있습니다. 로드 밸런싱, 고급 라우팅, 서비스 디스커버리 등 고급 네트워킹이나 오케스트레이션 기능에서는 Docker보다 불편하거나, 직접 추가 설정이 필요할 수 있습니다.

- **중앙 집중식 관리의 부재**: Docker 데몬은 모든 컨테이너 상태를 중앙에서 관리하므로, 컨테이너 전체를 한 번에 제어하거나, 데몬 재시작으로 전체 컨테이너를 제어할 수 있습니다. Podman은 데몬이 없기 때문에, 시스템 전체의 컨테이너 상태를 한 번에 제어하거나, 일괄적으로 관리하는 데 한계가 있습니다.

- **백그라운드 서비스/자동 재시작 기능 한계**: 데몬 기반 Docker는 컨테이너의 자동 재시작 정책 등 백그라운드 서비스 관리에 강점을 가집니다. Podman은 systemd와의 연동을 통해 이 문제를 보완하지만, Docker만큼 직관적이거나 일관된 경험을 제공하지는 않습니다.

이러한 장단점은 두 도구의 목적에 따른 차이입니다. Docker는 Kubernetes와 함께 사용하기 위한 목적으로 등장한 도구가 아니기에, 단독으로 사용할 때는 Podman보다 강력한 관리 기능을 제공합니다. 하지만 Podman은 Kubernetes와 통합이 용이합니다. Kubernetes와 함께 사용할 경우 위 단점은 해소됩니다.

> Podman이 Kubernetes 컨테이너 런타임으로는 사용되지 않습니다. 하지만 CRI-O나 containerd를 개발 도구로 직접 사용하는 것은 크게 불편하기에 Docker나 Podman을 개발 도구로 사용합니다.

### Kubernetes 통합

Podman의 명령 구조와 기능은 Kubernetes와 더 일관성 있게 설계되었습니다:

- **Pod 개념 기본 지원**: Podman은 이름에서 알 수 있듯이 pod 개념을 기본적으로 지원하므로 Kubernetes의 pod와 유사한 방식으로 컨테이너 그룹을 관리할 수 있습니다.
- **학습 곡선 감소**: 개발자가 로컬에서 Podman을 사용하여 개발한 후 Kubernetes로 이동할 때 개념적 일관성이 있어 전환이 수월합니다.
- **Kubernetes와의 연동**: Podman은 현재 Podman 환경의 컨테이너/Pod를 Kubernetes YAML(manifest)로 바로 변환할 수 있어, 개발-운영 환경 전환이 쉽고 일관성이 높습니다.

### 호환성과 대체 가능성

기존에 Docker를 사용했더라도 호환성을 제공합니다:

- **Docker 호환 API**: Podman은 Docker CLI와 호환되는 명령어를 제공하므로 기존 Docker 스크립트와 워크플로우를 거의 수정 없이 사용할 수 있습니다.
- **Docker 컨테이너 이미지 지원**: Docker로 생성된 이미지를 Podman에서도 사용할 수 있어 기존 자산을 계속 활용할 수 있습니다.

### 선택한 컨테이너 개발 도구: Podman

Docker의 관리 기능은 분명 강력하지만, 제가 컨테이너를 배포하려는 환경은 Kubernetes입니다. Docker를 사용 못할 이유는 없지만, Podman을 사용했을 때 얻을 수 있는 장점이 더 많다고 판단하여 Podman을 선택했습니다.

> 만약 Kubernetes 없이 컨테이너만 배포하여 운영 환경을 구축해야 한다면 Docker를 사용할 것입니다. 이 경우에도 Docker와의 호환성을 제공하므로 전환하기 쉬울 것입니다.

## 구축 과정
(작성 중)

## 다음 목표
(작성 중)




> **참조**
> - [Podman이란 무엇입니까? - Red Hat](https://www.redhat.com/en/topics/containers/what-is-podman)
> - [Podman과 Docker의 3가지 장점 | Red Hat 개발자](https://developers.redhat.com/articles/2023/08/03/3-advantages-docker-podman#_3__better_tools_and_extensions)
> - [전체 Podman 대 Docker 분석: 기능, 성능 및 보안 | Uptrace](https://uptrace.dev/comparisons/podman-vs-docker)
> - [Podman 공식 문서](https://podman.io/docs/installation)
