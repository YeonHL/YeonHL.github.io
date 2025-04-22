---
title: Kubernetes - 컴포넌트
description: 쿠버네티스 클러스터는 컴퓨터 집합인 노드 컴포넌트와 컨트롤 플레인 컴포넌트로 구성됩니다.
date: 2025-04-23 00:39:00+0900
lastmod: 2025-04-23
image: 
slug: components
comments: true
math: false
tags:
  - Kubernetes
keywords:
  - Kubernetes
---
[**목록으로 돌아가기**](..)

> **목차**
> - [개요](../overview)
> - **컴포넌트**



쿠버네티스를 배포하면 클러스터를 얻습니다. 쿠버네티스 클러스터는 <u>컴퓨터 집합인 노드 컴포넌트와 컨트롤 플레인 컴포넌트로 구성</u>됩니다.

쿠버네티스 클러스터는 컨테이너화된 애플리케이션을 실행하는 [**노드**](https://kubernetes.io/ko/docs/concepts/architecture/nodes/)라고 하는 워커 머신의 집합입니다. 모든 클러스터는 최소 한 개의 워커 노드를 가집니다.

워커 노드는 애플리케이션의 구성요소인 [**파드**](https://kubernetes.io/ko/docs/concepts/workloads/pods/)를 호스트합니다. [**컨트롤 플레인**](https://kubernetes.io/ko/docs/reference/glossary/?all=true#term-control-plane)은 워커 노드와 클러스터 내 파드를 관리합니다. 프로덕션 환경에서는 일반적으로 <u>컨트롤 플레인이 여러 컴퓨터에 걸쳐 실행</u>되고, 클러스터는 일반적으로 <u>여러 노드를 실행</u>하므로 내결함성과 고가용성이 제공됩니다.

아래는 쿠버네티스 클러스터 구성 요소 이미지입니다:

![쿠버네티스 구성 요소](kubernetes-cluster.svg)


## 코어 컴포넌트 (Core Components)

### 컨트롤 플레인 컴포넌트
> 클러스터의 전반적인 상태를 관리합니다:

컨트롤 플레인 컴포넌트는 <span style="background:#fff88f">클러스터에 관한 전반적인 결정</span>(예를 들어, 스케줄링)을 수행하고 클러스터 이벤트(예를 들어, 디플로이먼트의 `replicas` 필드에 대한 요구 조건이 충족되지 않을 경우 새로운 [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/)를 구동시키는 것)를 감지하고 반응합니다.

컨트롤 플레인 컴포넌트는 <u>클러스터 내 어떠한 머신에서든지 동작</u>할 수 있습니다. 그러나 간결성을 위하여, 구성 스크립트는 보통 동일 머신 상에 모든 컨트롤 플레인 컴포넌트를 구동시키고, 사용자 컨테이너는 해당 머신 상에 동작시키지 않습니다. 

**NOTE**: 여러 머신에서 실행되는 컨트롤 플레인 설정의 예제를 보려면 [kubeadm을 사용하여 고가용성 클러스터 만들기](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/)를 확인하세요.

#### kube-apiserver
> 쿠버네티스 HTTP API를 노출하는 핵심 컴포넌트 서버입니다.

API 서버는 쿠버네티스 <span style="background:#fff88f">API를 노출</span>하는 쿠버네티스 [컨트롤 플레인](https://kubernetes.io/ko/docs/reference/glossary/?all=true#term-control-plane) 컴포넌트입니다. API 서버는 쿠버네티스 **컨트롤 플레인의 프론트엔드**입니다.

쿠버네티스 API 서버의 주요 구현은 [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/) 입니다. kube-apiserver는 수평으로 확장되도록 디자인되었습니다. 즉, 더 많은 인스턴스를 배포해서 확장할 수 있습니다. 여러 kube-apiserver 인스턴스를 실행하고, 인스턴스간의 트래픽을 균형있게 조절할 수 있습니다.

#### etcd
> 모든 API 서버 데이터에 대한 일관되고 가용성이 높은 key-value 저장소입니다.

모든 <span style="background:#fff88f">클러스터 데이터를 담는</span> 쿠버네티스 백엔드의 저장소로 사용되는 일관성 · 고가용성 key-value 저장소입니다.

쿠버네티스 클러스터에서 etcd를 백엔드의 저장소로 사용한다면, 이 데이터의 [백업](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster)은 필수입니다.

**NOTE**: etcd에 대한 자세한 정보는, 공식 [문서](https://etcd.io/docs)를 참고하세요.

#### kube-scheduler
> 아직 노드에 바인딩되지 않은 파드를 찾고 각 파드를 적합한 노드에 할당합니다.

[노드](https://kubernetes.io/ko/docs/concepts/architecture/nodes/)가 배정되지 않은 새로 생성된 [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/) 를 감지하고, <span style="background:#fff88f">실행할 노드를 선택</span>하는 컨트롤 플레인 컴포넌트입니다.

스케줄링 결정을 위해서 고려되는 요소는 리소스에 대한 개별 및 총체적 요구 사항, 하드웨어/소프트웨어/정책적 제약, 어피니티(affinity) 및 안티-어피니티(anti-affinity) 명세, 데이터 지역성, 워크로드-간 간섭, 데드라인을 포함합니다.

#### kube-controller-manager
> 컨트롤러를 실행하여 쿠버네티스 API 동작을 구현합니다.

[컨트롤러](https://kubernetes.io/ko/docs/concepts/architecture/controller/) 프로세스를 실행하는 컨트롤 플레인 컴포넌트입니다.

논리적으로, 각 [컨트롤러](https://kubernetes.io/ko/docs/concepts/architecture/controller/)는 분리된 프로세스이지만, 복잡성을 낮추기 위해 모두 단일 바이너리로 컴파일되고 단일 프로세스 내에서 실행됩니다. 이들 컨트롤러는 다음을 포함합니다:

- **노드 컨트롤러**: 노드가 다운되었을 때 통지와 대응에 관한 책임을 가집니다.
- **잡 컨트롤러**: 일회성 작업을 나타내는 잡 오브젝트를 감시한 다음, 해당 작업을 완료할 때까지 동작하는 파드를 생성합니다.
- **엔드포인트슬라이스 컨트롤러**: (서비스와 파드 사이의 연결고리를 제공하기 위해) 엔드포인트슬라이스(EndpointSlice) 오브젝트를 채웁니다
- **서비스어카운트 컨트롤러**: 새로운 네임스페이스에 대한 기본 서비스어카운트(ServiceAccount)를 생성합니다.

#### cloud-controller-manager (Optional)
> 기본 클라우드 제공업체와 통합합니다.

클라우드별 컨트롤 로직을 포함하는 쿠버네티스 [컨트롤 플레인](https://kubernetes.io/ko/docs/reference/glossary/?all=true#term-control-plane) 컴포넌트입니다. 클라우드 컨트롤러 매니저를 통해 클러스터를 클라우드 공급자의 API에 연결하고, 해당 클라우드 플랫폼과 상호 작용하는 컴포넌트와 클러스터와만 상호 작용하는 컴포넌트를 구분할 수 있게 합니다.

cloud-controller-manager는 클라우드 제공자 전용 컨트롤러만 실행합니다. 자신의 사내 또는 PC 내부의 학습 환경에서 쿠버네티스를 실행 중인 경우 클러스터에는 클라우드 컨트롤러 매니저가 없습니다.

kube-controller-manager와 마찬가지로 cloud-controller-manager는 논리적으로 독립적인 여러 컨트롤 루프를 단일 프로세스로 실행하는 단일 바이너리로 결합합니다. 수평으로 확장(두 개 이상의 복제 실행)해서 성능을 향상시키거나 장애를 견딜 수 있습니다.

다음 컨트롤러들은 클라우드 제공 사업자의 의존성을 가질 수 있습니다:

- **노드 컨트롤러**: 노드가 응답을 멈춘 후 클라우드 상에서 삭제되었는지 판별하기 위해 클라우드 제공 사업자에게 확인하는 것
- **라우트 컨트롤러**: 기본 클라우드 인프라에 경로를 구성하는 것
- **서비스 컨트롤러**: 클라우드 제공 사업자 로드밸런서를 생성, 업데이트 그리고 삭제하는 것

### 노드 컴포넌트

노드 컴포넌트는 동작 중인 파드를 유지시키고 쿠버네티스 런타임 환경을 제공하며, 모든 노드 상에서 동작합니다.

#### kubelet
> 컨테이너를 포함하여 파드가 실행 중인지 확인합니다.

클러스터의 <span style="background:#fff88f">각 노드에서 실행되는 에이전트</span>. Kubelet은 [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/)에서 [컨테이너](https://kubernetes.io/ko/docs/concepts/containers/)가 확실하게 동작하도록 관리합니다.

Kubelet은 다양한 메커니즘을 통해 제공된 파드 스펙(PodSpec)의 집합을 받아서 컨테이너가 해당 파드 스펙에 따라 건강하게 동작하는 것을 확실히 합니다. Kubelet은 <u>쿠버네티스를 통해 생성되지 않는 컨테이너는 관리하지 않습니다.</u>

#### kube-proxy (Optional)
> 서비스를 구현하기 위해 노드에서 네트워크 규칙을 유지 관리합니다.

kube-proxy는 클러스터의 <span style="background:#fff88f">각 노드에서 실행되는 네트워크 프록시</span>로, 쿠버네티스의 [서비스](https://kubernetes.io/ko/docs/concepts/services-networking/service/) 개념의 구현부입니다.

[kube-proxy](https://kubernetes.io/ko/docs/reference/command-line-tools-reference/kube-proxy/)는 <u>노드의 네트워크 규칙을 유지 관리</u>합니다. 이 네트워크 규칙이 내부 네트워크 세션이나 클러스터 바깥에서 파드로 네트워크 통신을 할 수 있도록 합니다.

kube-proxy는 운영 체제에 가용한 패킷 필터링 계층이 있는 경우, 이를 사용합니다. 그렇지 않으면, kube-proxy는 트래픽 자체를 포워드(forward)합니다.

**NOTE**: 서비스에 대한 패킷 포워딩을 자체적으로 구현하고 kube-proxy와 동등한 동작을 제공하는 [네트워크 플러그인](https://kubernetes.io/docs/concepts/architecture/#network-plugins)을 사용하는 경우, 클러스터의 노드에서 kube-proxy를 실행할 필요가 없습니다.

#### 컨테이너 런타임
> 컨테이너 실행을 담당하는 소프트웨어. 자세한 내용은 컨테이너 런타임을 참조하세요.

쿠버네티스가 컨테이너를 효과적으로 실행할 수 있도록 지원하는 기본 구성 요소입니다. 쿠버네티스 환경 내에서 <span style="background:#fff88f">컨테이너의 실행과 라이프사이클을 관리</span>하는 역할을 담당합니다.

쿠버네티스는 [containerd](https://containerd.io/docs/), [CRI-O](https://cri-o.io/#what-is-cri-o)와 같은 컨테이너 런타임 및 모든 [Kubernetes CRI (컨테이너 런타임 인터페이스)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md) 구현체를 지원합니다.


## 애드온

클러스터에는 각 노드에 추가 소프트웨어가 필요할 수 있습니다. 예를 들어 Linux 노드에서 [systemd](https://systemd.io/)를 실행하여 로컬 구성 요소를 감독할 수도 있습니다.

애드온은 쿠버네티스 리소스([데몬셋](https://kubernetes.io/ko/docs/concepts/workloads/controllers/daemonset), [디플로이먼트](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/) 등)를 이용하여 클러스터 기능을 구현합니다. 이들은 <span style="background:#fff88f">클러스터 단위의 기능을 제공</span>하기 때문에 애드온에 대한 네임스페이스 리소스는 `kube-system` 네임스페이스에 속합니다.

선택된 일부 애드온은 아래에 설명하였고, 사용 가능한 전체 확장 애드온 리스트는 [애드온](https://kubernetes.io/ko/docs/concepts/cluster-administration/addons/)을 참조합니다.

### DNS
> 클러스터 전체 DNS 확인

여타 애드온들이 절대적으로 요구되지 않지만, <u>많은 예시에서 필요</u>로 하기 때문에 모든 쿠버네티스 클러스터는 [클러스터 DNS](https://kubernetes.io/ko/docs/concepts/services-networking/dns-pod-service/)를 갖추어야만 합니다.

클러스터 DNS는 구성 환경 내 다른 DNS 서버와 더불어, <span style="background:#fff88f">쿠버네티스 서비스를 위해 DNS 레코드를 제공</span>해주는 DNS 서버입니다.

쿠버네티스에 의해 구동되는 컨테이너는 DNS 검색에서 이 DNS 서버를 자동으로 포함합니다.

### 웹 UI (대시보드)
> 웹 인터페이스를 통한 클러스터 관리

[대시보드](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/)는 쿠버네티스 클러스터를 위한 범용의 <span style="background:#fff88f">웹 기반 UI</span>입니다. 사용자가 <u>클러스터 자체뿐만 아니라, 클러스터에서 동작하는 애플리케이션에 대한 관리와 문제 해결이 가능</u>합니다.

### 컨테이너 리소스 모니터링
> 컨테이너 메트릭 수집 및 저장

[컨테이너 리소스 모니터링](https://kubernetes.io/ko/docs/tasks/debug/debug-cluster/resource-usage-monitoring/)은 중앙 데이터베이스 내의 컨테이너들에 대한 포괄적인 시계열 <span style="background:#fff88f">매트릭스를 기록하고 그 데이터를 열람</span>하기 위한 <u>UI를 제공</u>합니다.

### 클러스터-레벨 로깅
> 컨테이너 로그를 중앙 로그 저장소에 저장

[클러스터-레벨 로깅](https://kubernetes.io/ko/docs/concepts/cluster-administration/logging/) 메커니즘은 검색/열람 인터페이스와 함께 중앙 로그 저장소에 컨테이너 로그를 저장하는 역할입니다.

### 네트워크 플러그인

[네트워크 플러그인](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/)은 컨테이너 네트워크 인터페이스(CNI) 사양을 구현하는 소프트웨어 컴포넌트입니다. 이들은 파드에 IP 주소를 할당하고 클러스터 내에서 서로 통신할 수 있도록 하는 역할을 합니다.


> **출처**
> - [쿠버네티스 컴포넌트 | Kubernetes](https://kubernetes.io/ko/docs/concepts/overview/components/)