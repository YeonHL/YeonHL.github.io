---
title: 배포 테스트를 위한 Kubernetes 로컬 환경 구축
description: 시스템의 Kubernetes 배포 테스트를 위한 로컬 환경 Kubernets 클러스터 구축 과정
date: 2025-04-29 17:73:00+0900
lastmod: 2025-04-29
slug: local-kubernetes-for-deployment-testing
comments: true
math: false
categories:
  - Container
tags:
  - Kubernetes
  - Minikube
  - Podman
keywords:
  - Kubernetes
  - Minikube
  - Podman
---
시스템의 Kubernetes 배포 테스트를 위해 로컬 환경에 Kubernets 클러스터를 구축한 과정입니다.

## Kubernetes 클러스터 도구

이번 로컬 환경은 개발 단계에서 테스트하는 것이 목적이므로 구현하려는 기능의 지원 여부와 사용 편의성을 기준으로 결정했습니다. kubeadm 대신 아래의 클러스터 배포 도구들을 고민했습니다:

**Minikube**
- Kubernetes를 학습하고 개발하기 쉽게 만드는 데 중점을 둔 로컬 Kubernetes입니다.
- 주로 가상 머신이나 컨테이너 위에 실행됩니다.
- 다른 쿠버네티스를 Add-on 형식으로 쉽게 설치할 수 있다는 장점이 있습니다.

**KinD (Kubernetes in Docker)**
- Docker 컨테이너 노드를 사용하여 로컬 Kubernetes 클러스터를 실행하기 위한 도구입니다.
- 주로 Kubernetes 자체를 테스트하기 위해 설계되었지만, 로컬 개발이나 CI에 사용될 수 있습니다.

**k3s**
- 경량화된 쿠버네티스 배포판으로 리소스가 적은 환경이나 IoT, 엣지 컴퓨팅, 학습용으로 적합합니다.
- 설치와 관리가 상대적으로 간단합니다.


위의 도구 중에서는 아래의 이유로 <span style="background:#fff88f">Minikube</span>의 사용을 결정했습니다:

- [이전의 포스트](https://yeonhl.github.io/p/create-container-development-environment/)에서 Podman 기반의 컨테이너 개발 환경을 구축했습니다. Podman Desktop의 Kubernetes 지원 대상으로는 **Minikube**와 **Kind**가 있었는데, 테스트에 문제가 없다면 조금이라도 더 편리한 환경을 제공하는 도구를 사용하고자 했습니다. 
- **Minikube**는 Kubernetes를 학습하고 개발하기 쉽게 만드는 것을 목적으로 설계된 도구이고, **Kind**는 Kubernetes 자체를 테스트하기 위해 설계된 도구입니다. 이번 로컬 개발 환경의 목적을 고려했을 때 **MiniKube**가 더 적합하다고 판단했습니다.  

> 추후 Kubernetes 서버 관리를 위한 로컬 테스트 환경을 구성한다면 **Kind**을 사용하려 합니다.




## 구현 과정



## 결과



## 다음 목표



> **참조**
> - [kind – Principles](https://kind.sigs.k8s.io/docs/design/principles/)
> - [podman | minikube](https://minikube.sigs.k8s.io/docs/drivers/podman/)
