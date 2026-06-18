---
title: "[논문리뷰] ReliabilityRAG: Effective and Provably RobustDefense for RAG-based Web-Search"
date: 2026-06-18 00:00:00 +0900
categories: [RAG]
tags: [RAG, Security, NLP]
---

## 논문 정보
- **논문 제목:**  ReliabilityRAG: Effective and Provably RobustDefense for RAG-based Web-Search

- **논문 정보:** NeurIPS(2026)

# Introduction
RAG는 불완전하거나 오래되었을 수 있는 고정된 지식에만 의존하는 LLM 모델의 한계를 극복하기 위한 해결책으로 등장했다. 외부 코퍼스에서 관련 문서를 검색하여 모델의 입력에 통합함으로써, RAG는 더욱 근거있는 응답을 가능하게 한다. RAG 패러다임의 두드러진 응용 중 하나는 언어 모델로 보강된 검색 엔진에서의 사용이다.
이러한 시스템에서 웹 검색 엔진은 retriever 역할을 하여 사용자의 쿼리와 관련된 문서를 식별하고, 검색된 콘텐츠는 LLM으로 전달되어 검색된 문서에 근거한 최종 응답을 생성한다. 이러한 RAG는 여러 장점을 가지지만 몇가지 단점이 있다.

## RAG-based Web search is vulnerable
생성된 응답의 품질을 저해하는 적대적 공격에 취약하다. 특히 검색 corpus는 LLM을 조작하여 부정확하거나 악의적인 응답을 생성하게 할 수 있든 corpus poisoning 및 prompt injection 공격과 같은 적대적 공격에 취약합니다. RAG 시스템의 효과를 저해하는 추가적인 견고성 과제로는 노이즈가 있거나, 모순되거나, 신뢰할 수 없는 문서의 존재들이 있다.

## Existion defenses have limited practicality
RAG의 견고성을 강화하기 위해 제안된 기존 프레임워크들은 실제 적용을 방해하는 한계를 보인다. 특히 적대적 견고성 제공을 목표로 하는 주요 기존 프레임워크인 Robust RAG는 공격이 없는 시나리오에서 성능이 제한적이며 복잡한 생성 작업이 어렵다는 문제점이 있다. 이 프레임워크는 다수결 투표 전략을 사용하지만, 키워드나 다음 토큰 확률에 기반한 구현은 상당한 정보 손실을 수반하며 자유 형식의 자연어에 대해 의미 있는 다수결 투표를 정의하는 것은 쉬운 일이 아니다.

## Document rank or Explicit reliability score
RAG 기반 검색은 적대자가 우회하기 어려운 문서 순위와 같은 내장된 신뢰성 신호를 포함하고 이씩 때문에 특히 흥미로운 적대적 환경을 제시합니다. 현재의 RAG 방어책들은 이러한 신뢰성 신호를 간과하고 검색된 문서를 순서가 없는 집합으로 취급하는데, 이는 심층 방어 접근 방식의 일부로서 상호 보완적인 보호 장치를 쌓을 수 있는 기회를 놓치는 것이다.

# Background and Problem Setting
논문 전반에 걸쳐, 문서가 적대자에 의해 오염된 경우 malicious(악성)이라고 부르고, 그렇지 않은 경우 양성(bengin)라고 정의한다. 사용자의 쿼리에 기반하여 순위 또는 신뢰성 정보를 포함한 문서를 반환하는 시스템을 지힝하기 위해 retriever라는 용어를 사용한다. 원래 쿼리와 retriever에 의해 검색된 문서를 사용하여 답변을 생성하는 LLM을 지칭하기 위해 LLM이라는 용어를 사용한다.

## RAG with Ordinal and Cardinal Relaibility
쿼리 $q$가 주어지면,  retriever는 순서가 있는 $k$개의 문서 리스트 $D=(x_1, x_2, ..., x_k)$를 반환한다. 일관되게 $x_1$이 가장 높은 순위(가장 신뢰할 수 있는) 문서이고, $x_k$가 가장 낮은 순위 문서라는 관례를 사용한다. 더 높은 순위의 문서를 언급할 떄, 이는 리스트의 앞부분(더 작은 인덱스, 더 큰 신뢰성)에 가까운 문서를 의미하며, 더 낮은 순위의 문서는 리스트의 뒷부분(더 큰 인덱스, 더 낮은 신뢰성)에 가까운 문서를 의미한다.

핵심적인 차이점은 retriever가 검색된 문서에 ordinal 또는 cardianl 신뢰성 정보를 제공하는지 여부이며, 이 논문에서의 방어는 두 형식 중 어느 것이든 활용할 수 있다. ordinal 신뢰성 설정에서는 "$x_1$은 적어도 $x_2$만큼 신뢰할 수 있다"는 등으로 해석되는 순서 $x_1 ⪰ x_2 \cdot \cdot \cdot x_k$만을 관찰합니다. cardinal 신뢰성 설정에서는 각 문서가 추가적으로 $w(x_1) ⪰ w(x_2) \cdot \cdot \cdot w(x_k)$인 비음수 가중지 $w(x_i) ∈ [0, 1]$ 을 가지며, 이는 등급화된 신뢰성을 나타낸다. 더 높은 가중치는 더 높은 신뢰성에 대응한다.

## Threat Model: Corrupted Documents in Retreival
이 논문에선 Google에서 AI Overview 또는 ChatGPT Search와 같은 RAG 기반 검색 시스템에 대한 targetted attack을 고려한다. 또, RAG 기반 검색 시스템에 의해 검색된 문서 중 일부를 오염 시킬 수 있는 공격에 집중한다. 시스템 제공자의 운영 인프라를 직접 공격하는 공격은 상정하지 않는다. 권위 있고 신뢰할 수 있는 소스의 효과적인 우선 순위 지정을 가능하게 하고 SEO 공격을 견뎌내는 정보 검색 분야의 광범위한 연구에 동기를 부여받아, 본 연구에서의 Threat Model은  적대자가 높은 순위나 높은 가중치의 문서를 오염시키는 것이 낮은 순위나 낮은 가중치의 문서를 오염시키는것보다 상대적으로 더 어렵다는 점을 포착한다.

### Adversary Goal
적대자의 목표가 AI 개요에 자신의 제품을 포함시키는 것과 같이 LLM에 특정한 출력을 유도하는 공격에 집중한다. 공격자가 쿼리에 대한 응답으로 retriever

