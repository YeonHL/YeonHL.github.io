---
title: MCP 서버 배포하기
description: Python으로 Typescript 기반 Puppeteer MCP 서버 배포하기
date: 2025-05-22 21:00:00+0900
lastmod: 2025-05-23
slug: deploy-puppeteer-mcp-server
comments: true
math: false
categories:
  - Agents
tags:
  - MCP
  - Docker
keywords:
  - MCP
  - Docker
links:
  - title: YeonHL/ai-agents-examples
    description: 에이전트 관련하여 구현한 기능 및 예제 저장소입니다.
    website: https://github.com/YeonHL/ai-agents-examples
    image: https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png
---

DockerStdioTransport 구현 후 Typescript 기반 MCP 서버 배포 과정 및 FastAPI 연결 후 여러 MCP 서버 배포 과정 작성
왜 FastMCP를 사용했는지, FastMCP로 다 구현하지 않고 FastAPI에 연결하여 사용한 이유 작성하기 (기존 FastAPI 서버 활용 및 둘의 특화된 구현 기능의 차이)

> **참조**
>
> -
