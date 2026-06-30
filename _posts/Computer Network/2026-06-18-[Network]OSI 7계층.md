---
title: "OSI 7계층"
date: 2026-06-18 00:00:00 +0900
categories: [Computer Network]
tags: [Computer Network, Network, OSI 7 Layers]
---

## OSI 7 계층이란?
OSI 7계층은 네트워크와 인터넷을 이해하는데 가장 기본이 되는 개념이다. 쉽게 말해 네트워크 통신 과정을 7단계로 나누고 각 단계의 역할을 표준화 (국제 표준) 한 것이다.

OSI 7계층은 이름과 같이 7개의 Layer로 구성되며, 각 Layer는 독립적이며 특정 작업을 처리하며, 그 위와 아래의 계층과 통신한다. 구성은 아래와 같다.

- Application Layer (7)
- Presentation Layer (6)
- Session Layer (5)
- Transport Layer (4)
- Network Layer (3)
- Data Layer (2)
- Physical Layer (1)

이렇게 계층을 나누면 통신의 과정을 단계별로 파악할 수 있기 때문에 흐름을 알아보기 쉽고, 이해도 쉽고, 문제가 발생했을때 여러 소프트웨어를 수정하지 않고, 문제가 생긴 단계만 수정하면 될 수 있다는 장점이 생긴다.

## OSI 7단계
###  Application Layer
사용자의 데이터와 직접 상호작용 하는 유일한 계층이다. 쉽게 이해하면 User가 UI로 접하는 응용 프로그램과 관련된 계층이다.

### Presentation Layer
Application layer에서 전달 받은 데이터를 사용할 수 있게 데이터의 형식을 변환하는 역할을 한다.

### Session Layer
서버와 Client가 통신을 시작하고 종료하는 과정을 관리하는 역할을 한다. 교환되고 있는 모든 데이터를 전송할 수 있도록 충분히 오랫동안 세션을 개방한 다음 리소스를 낭비하지 않기 위해 세션을 즉시 닫을 수 있도록 보장한다.
API와 Socket 기술이 해당된다.

### Transport Layer
두 기기 간의 End to End 통신을 담당한다. Session layer에서 데이터를 가져와서 Network Layer 보내기 전에 Segment라고 하는 조각들로 분할하는 일을 한다. 수신 기기의 경우 Segment를 Session Layer가 이용할 수 있도록 재조립을 한다.

### Network Layer
서로 다른 네트워크 간의 데이터를 전달하는 역할을 한다. Transport Layer의 Segment를 송신 장치에서는 Packet이라고 불리는 더 작은 단위로 세분화한다. 수신 장치에서는 이러한 Packet을 재조립을 하게 된다. 인터넷을 사용할 때 라우팅 기능을 통해 데이터를 목적지까지 가장 빠르고 안전하게 보낸다.

### Data Link Layer
Network Layer와 유사하지만 동일한 네트워크에 있는 두 개의 장치 간 데이터 전송을 용이하게 한다. Packet을 Frame이라고 불리는 더 작은 조각으로 세분화한다. 

### Physical Layer
데이터를 0과 1의 전기적 신호로 변환하여 물리적으로 전송하는 역할을 한다. LAN 케이블, 광 케이블 등 물리적인 요소들이 포함된다. 전송 단위는 Bit이고 통신케이블, Hub 장비가 동작한다.