---
title: 에이전트 프레임워크 고르기
description: 에이전트 프레임워크를 비교하고 선택하는 과정
date: 2025-04-25 02:03:00+0900
lastmod: 2025-04-26
slug: choose-agents-framework
comments: true
math: false
categories:
  - Agents
tags:
  - LangGraph
keywords:
  - Agents
  - LangGraph
---

회사 내부에서 에이전트 구축 프로젝트 관련 제안을 받으면서, 준비 과정으로 먼저 사용할 에이전트 프레임워크를 고민했습니다.

## 프레임워크 선택 기준

프로젝트의 구체적인 목표가 정해지지 않은 만큼, 다음의 기준으로 프레임워크를 고민했습니다:

- 기능성: 복잡한 로직, 워크플로우도 구현할 수 있어야 합니다.
- 확장성: 최근 공개된 MCP, A2A 프로토콜이나 이외에 새로운 기능을 구현하더라도 원활하게 통합할 수 있어야 합니다.
- 안정성: 연구 목적이 아닌 상품이 목적이기에 안정적인 동작 및 메트릭 등 추적 환경이 있으면 좋습니다.
- 커뮤니티 활성화: 꾸준한 지원을 받기 위해 오픈 소스 커뮤니티가 활성화되어야 합니다.

## 프레임워크 비교

고민했던 후보는 CrewAI, Letta, LangGraph, Smolagents, AutoGen, Semantic Kernal입니다. 커뮤니티의 평가 등을 확인한 내용을 정리하면 다음과 같았습니다.

| 프레임워크           | 특징           | 장점                                                           | 단점                                                                                                         | 개인 의견 |
| --------------- | ------------ | ------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- | ----- |
| CrewAI          | 멀티 에이전트 협업   |                                                              |                                                                                                            |       |
| Letta           | 장기 메모리 관리    |                                                              | 복잡한 워크플로우 구현 어려움                                                                                           |       |
| LangGraph       | 그래프 기반 워크플로우 | 복잡한 워크플로우 처리 가능<br>모든 노드 상태 실시간 공유 및 수정으로 장기 작업 시 데이터 일관성 보장 | 그래프 설계 등 러닝 커브 높음<br>단순 작업도 과도한 구조화 필요                                                                     |       |
| Smolagents      | 초경량 코드 실행    |                                                              | 복잡한 에이전트 로직 구현 어려움                                                                                         |       |
| AutoGen         |              |                                                              | 대화 기반 멀티 에이전트로 복잡한 분기/반복 처리 어려움<br>정적 상태 관리로 에이전트 간 컨텍스트 공유가 어려워 장기 작업에서 상태 일관성 유지 불가<br>운영 안정성 낮음 (연구 목적) |       |
| Semantic Kernal |              |                                                              | 복잡한 워크플로우 구현 어려움                                                                                           |       |
| OpenAI SDK      |              | OpenAI 도구 체인 통합 용이                                           | 복잡한 워크플로우 구현 어려움                                                                                           |       |

### CrewAI

#### 장점

#### 단점

- 워크플로우의 **에이전트 역할을 사전 정의**하므로 필요에 따른 동적 변경이 어렵습니다.
- 순차적 작업과 같은 **단순한 워크플로우**만 지원합니다.


### Letta

#### 장점

#### 단점

- 장기 메모리를 분리된 세션으로 관리하는 **컨텍스트 단편화**로 워크플로우 전체 상태 추적이 불가능합니다.
- **단일 에이전트 중심**으로 멀티 에이전트 협업 시 직접 구현해야 합니다.
### LangGraph

#### 특징

- **그래프 기반 비선형 워크플로우**: 분기, 반복, 병럴 처리로 복잡한 의사 결정 구조를 구현할 수 있습니다.
- **실시간 워크플로우 수정**: 특정 상태 변경 시 다른 노드를 자동 활성화하는 이벤트 기반 트리거 등 런타임 중 노드/엣지 동적 추가, 제거가 가능합니다.
- **전역 상태 추적**: 모든 노드의 상태를 실시간으로 공유, 수정할 수 있습니다.

#### 장점

- 에이전트 간 상호 작용을 그래프 노드로 정의하여 **멀티에이전트 네트워크**를 구현하거나 동일 노드 재방문과 같은 **순환 구조** 등 복잡한 워크플로우도 구현할 수 있습니다.
- **전역 상태 추적** 형태로 장기 작업 시 데이터 일관성을 보장합니다.

#### 단점

- 엔터프라이즈 보안/컴플라이언스 준수는 미흡합니다. 추가적인 

### Smolagents

#### 장점

#### 단점

- 지나친 경량화로 **복잡한 워크플로우 설계가 불가능**합니다.
- **각 작업이 독립적**으로 이전 단계 결과 활용이 어렵습니다.

### AutoGen

#### 장점

- 비동기 메시징 시스템을 도입하여 **이벤트 기반** 워크플로우 설계가 가능합니다.

#### 단점

- 에이전트 간 컨텍스트 공유가 제한적인 **정적 상태 관리**로 장기 작업에서 상태 일관성 유지가 불가능합니다.

### Semantic Kernal

#### 장점

#### 단점

- 순차적 실행에 최적화된 **함수 중심의 파이프라인**으로 동적 분기, 반복 구현이 어렵습니다.
- **단일 에이전트 중심**으로 멀티 에이전트 협업 시 직접 구현해야 합니다.


### OpenAI SDK


### 선택: LangGraph

다른 프레임워크보다 높은 학습 요구량을 요구한다는 의견이 있었지만, 다양한 요구에 가장 잘 대응할 수 있다고 생각하여 LangGraph로 구현을 시작하려 합니다.
AutoGen과의 통합하여 사용

## 다음 목표

LangGraph를 활용하여 간단한 예제를 만들어보면서 학습하고, 생각나는 기능을 구현할 계획입니다.

> **참조**
>
> - [Comparing AI agent frameworks: CrewAI, LangGraph, and BeeAI - IBM Developer](https://developer.ibm.com/articles/awb-comparing-ai-agent-frameworks-crewai-langgraph-and-beeai/)
> - [Smolagents vs LangGraph: Which is Better?-AI-php.cn](https://www.php.cn/faq/1796776052.html)
> - [Crewai vs. LangGraph: Which multi agent framework should you use? | Zams](https://www.zams.com/blog/crewai-vs-langgraph)
> - [(12) Deep Dive: LangGraph vs. AutoGen - A Detailed Comparison | LinkedIn](https://www.linkedin.com/pulse/deep-dive-langgraph-vs-autogen-detailed-comparison-ravi-gupta-dzkrf/)
> - [(12) Choosing the Right AI Agent Framework: LangGraph vs. CrewAI vs. AutoGen | LinkedIn](https://www.linkedin.com/pulse/choosing-right-ai-agent-framework-langgraph-vs-crewai-jaganlal-thoppe-2aele/)



**반영**
- [Smolagents 대 LangGraph: 어느 것이 더 낫습니까?-AI-php.cn](https://www.php.cn/faq/1796776052.html)
- [AI 에이전트 프레임워크 비교: CrewAI, LangGraph 및 BeeAI - IBM Developer](https://developer.ibm.com/articles/awb-comparing-ai-agent-frameworks-crewai-langgraph-and-beeai/)
- [(12) 심층 분석: LangGraph 대 AutoGen - 상세 비교 | 링크드인](https://www.linkedin.com/pulse/deep-dive-langgraph-vs-autogen-detailed-comparison-ravi-gupta-dzkrf/)
- [(12) 올바른 AI 에이전트 프레임워크 선택: LangGraph 대 CrewAI 대 AutoGen | 링크드인](https://www.linkedin.com/pulse/choosing-right-ai-agent-framework-langgraph-vs-crewai-jaganlal-thoppe-2aele/)
- [(12) Multi-Agent with LangGraph: From Basics to Advanced Insights | LinkedIn](https://www.linkedin.com/pulse/multi-agent-langgraph-from-basics-advanced-insights-ke-zheng-dmgre/)
- [Crewai 대 LangGraph: 어떤 다중 에이전트 프레임워크를 사용해야 할까요? | 잠스](https://www.zams.com/blog/crewai-vs-langgraph)
- [2025년 상위 6개 AI 에이전트 프레임워크에 대한 자세한 비교](https://www.turing.com/resources/ai-agent-frameworks)
- [Top 12 AI Agent Frameworks in 2025](https://brightdata.com/blog/ai/best-ai-agent-frameworks)
- [Comparing Open-Source AI Agent Frameworks - Langfuse Blog](https://langfuse.com/blog/2025-03-19-ai-agent-comparison)