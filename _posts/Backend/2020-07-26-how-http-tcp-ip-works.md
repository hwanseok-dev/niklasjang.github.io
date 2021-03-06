---
title: "[Backend] HTTP, TCP/IP 동작과정"
excerpt: ""
date: 2020-07-26
categories:
  - Backend
tags:
  - backend
  - WAS
  - web server
toc : false
toc_label: "Table of contents"
toc_icon: "list"  # corresponding Font Awesome icon name (without fa prefix)
toc_sticky: true
classes: wide
---

# HTTP 동작 과정

## host, 통신 링크, 라우터, 데이터, 패킷

host들은 통신 링크로 연결되며, 링크의 특성에 따라서 전송률과 대역폭이 달라집니다. 통신 링크들이 연결되는 지점을 router라고 합니다. link와 router를 통해서 전송되는 데이터는 packet의 형태를 가집니다. data를 segment로 나누고 header를 붙히면 packet이 됩니다.  

## 네트워크 5계층

| 이름 | 정보 패킷 | 프로토콜 | 역할 |
|:-----|:---------|:------|:-------|
| 애플리케이션 | message  | HTTP, SMTP, FPT | 두 host는 HTTP 메시지 포멧을 통해서 통신합니다. |
| 트랜스포트   | segment(출발지 포트 번호, 목적지 포트 번호, )  | TCP , UDP | TCP는 신뢰성,흐름제어, 혼잡제어의 기능을 제공합니다. |
| 네트워크     | datagram | IP | segment를 datagram으로 나눈 뒤 routing 하는 역할을 맡습니다. |
| 링크         | frame    | - | 통신 링크는 경로상의 다음 노드에 전달합니다. |
| 물리         | bit      | - | 링크를 이루는 물질은 bit를 전송하는 역할을 맡습니다. | 

## HTTP와 TCP의 협업

HTTP는 TCP를 전송 프로토콜로 사용합니다.  

1. 클라이언트와 서버에는 각각 TCP 연결을 위한 소켓이 존재합니다.
2. HTTP 클라이언트는 HTTP 기본 포트인 80을 통해 HTTP 서버로 TCP 연결을 시도합니다.
3. HTTP 서버는 HTTP 클라이언트의 TCP 연결에 응답합니다.
4. TCP 연결이 완료됩니다.
5. HTTP 클라이언트는 연결된 TCP 통신을 통해 요청 메시지를 전송합니다.
6. HTTP 서버는 연결된 TCP 통신을 통해 요청 메시지를 받습니다.
7. HTTP 서버는 저장장치로부터 요청 받은 객체를 추출하고 응답 메시지에 캡슐화합니다.
8. HTTP 서버는 응답 메시지를 소켓을 통해 HTTP 클라이언트에게 전송합니다.
9. HTTP 서버는 TCP에게 TCP 연결을 끊으라고 합니다.
10. HTTP 클라이언트가 응답 메시지를 받으면 TCP 연결이 중단됩니다. 캡슐화된 객체를 추출하고 객체에 대한 참조를 사용자에게 보여줍니다.  

## HTTP Session 동작 과정

1. 클라이언트가 서버로 http 요청을 보내 접속을 합니다.
1. 서버는 접근한 클라이언트의 request-header field인 cookie를 확인해 클라이언트의 session-id를 확인합니다.
1. 클라이언트로부터 전송된 session-id가 cookie에 없으면 서버는 session-id를 생성해서 클라이언트에게 response-header field인 set-cookie 값으로 session-id를 보내줍니다. 

## web cache 동작 과정

1. 브라우저는 웹 캐시와 TCP 연결을 설정하고 웹 캐시에 있는 객체에 대한 HTTP 요청을 보냅니다.
2. 웹 캐시는 객체의 사본이 자기에게 저장되어 있는지 확인합니다. 만일 저장되어 있다면 웹 캐시는 클라이언트 브라우저로 HTTP 응답 메시지와 함께 객체를 전송합니다.
3. 만일 웹 캐시가 객체를 가지고 있지 않다면, 웹 캐시는 원출처의 서버에 TCP 연결을 설정합니다. 그리고 나서 웹 캐시는 객체에 대한 HTTP 요청을 TCP를 통해 서버로 보냅니다. 서버는 이 요청을 받은 후에 웹 캐시로 HTTP 응답 메시지와 함께 객체를 보냅니다.
4. 웹 캐시의 객체를 수신할 때, 객체를 지역 저장장치에 복사하고 클라이언트 브라우저에 HTTP 응답 메시지와 함께 객체의 사본을 보냅니다.

## HTTP의 조건부 GET

웹 캐싱이 위와 같은 장점이 있지만, 웹 캐시에 저장된 값이 항상 새로운 값으로 갱신되지 않을 수 있습니다. 조건부 GET은 클라이언트가 브라우저로 전달되는 모든 객체들이 최신의 것임을 확인하면서 캐싱을 하도록 해주는 HTTP의 방식입니다. HTTP 요청 메시지가 GET 방식을 사용하고 If-Midified-Since: 헤더를 포함한다면 조건부 GET 방식의 메시지 입니다.  

웹 캐시는 요청하는 브라우저에 객체를 보내 주고 자신에게도 객체를 저장합니다. 중요한 것은 캐시가 객체와 더불어 마지막으로 수정된 날짜를 함께 저장한다는 것입니다. 일주일 뒤에 다른 브라우저가 같은 객체를 캐시에게 요청하면 객체는 여전히 저장되어 있습니다. 이 객체는 지난주에 웹 서버에서 수정되었으므로 브라우저는 조건부 GET으로 갱신 조사를 수행합니다. 갱신조사는 If-modified-since: 헤더 라인의 값이 일주일 전에 서버가 보낸 값과 정확히 일치하는지를 확인합니다. 만약 변경되지 않았다면 웹 서버는 클라이언트에게 빈 객체 몸체를 포함하는 응답 메시지를 보냅니다.  

# TCP/IP 동작과정

## 네트워크 계층 기능을 사용하는 트랜스포트 계층

트랜스포트 계층 프로토콜은 서로 다른 호스트에서 동작하는 애플리케이션 프로세스 간의 논리적인 통신logical communications을 제공합니다. 논리적 통신은 애플리케이션의 관점에서 보면 프로세스들이 동작하는 호스트들이 직접 연결된 것처럼 보인다는 것을 의미합니다.  

트랜스포트 계층 프로토콜은 네트워크 라우터가 아닌 종단 시스템에서 구현됩니다. 송신측과 수신측 모두에서 네트워크 계층이 존재하고 이를 통과하면서 header가 추가/삭제되면서 데이터가 전송됩니다. 송신 측에서 데이터를 전송하는 과정에서 애플리케이션 -> 물리 계층으로의 흐름을 `다중화`, 물리계층 -> 수신측의 애플리케이션 계층으로의 흐름을 `역다중화`라고 합니다.  

트랜스포트 계층은 네트워크 계층의 기능을 확장합니다. 즉, 종단 시스템 사이의 IP 전달 서비스(네트워크 계층의 기능)을 종단 시스템에서 동작하는 두 프로세스(애플리케이션계층) 간의 전달 서비스(트랜스포트계층)로 확장합니다.  

## TCP와 UDP의 기능

TCP는 1 ~ 5의 기능을 UDP는 1 ~ 2의 기능을 지원합니다. 

1. 프로세스 대 프로세스 데이터 전달
1. 오류 검출
1. 신뢰적인 데이터 전달
1. 혼잡제어
1. 흐름제어

## 애플리케이션 계층과 트랜스포트 계층 사이의 데이터 교환

네트워크 애플리케이션의 한 부분으로서 프로세스는 소켓을 가집니다. 이를 통해서 네트워크에서 프로세스로의 데이터를 전달하며, 프로세스로부터 네트워크로 데이터를 전달하는 출입구 역할을 합니다. 따라서 수식 측 호스트의 트랜스포트 계층은 실제로 데이터를 직접 프로세스로 전달하지 않습니다. 대신에 중간 매개자인 소켓(애플리케이션 계층과 트랜스포트 계층 사이의)에게 전달합니다.

어떤 주어진 시간에 수신 측 호스트에 하나 이상의 소켓이 있을 수 있으므로, 각각의 소켓은 어떤 하나의 유잃나 식별자를 가집니다. 이 식별자의 포멧은 UDP인지 TCP인지에 따라서 달라집니다. 적절한 소켓을 찾는 방법은 다음과 같습니다. 각각의 트랜스포트 계층 세그먼트는 소켓을 찾기 위해 세그먼트에 필드 집합을 가지고 있습니다. 수신 측의 트랜스포트 계층은 수신 소켓을 식별하기 위해서 이들 필드를 검사합니다. 그리고 해당 세그먼트를 적절한 소켓으로 보냅니다.  

## TCP 송신자의 역할  

1. segment : TCP는 애플리케이션으로부터 데이터를 받고, 세그먼트로 이 데이터를 캡슐화하고, IP에게 세그먼트 넘긴다. 각 세그먼트는 첫 번째 데이터 바이트의 바이트열 번호인 순서번호를 포함한다.  
2. timer : 타이머가 이미 다른 세그먼트에 대해서 실행 중이 아니면, TCP는 이 세그먼트를 IP로 넘길 때 타이머를 시작한다. 타이머에 대한 만료 주기는 TimeoutInterval이다.  
3. timeout : 타임아웃 이벤트에 대해 타임아웃을 일으킨 세그먼트를 재전송하여 응답한다. 그리고 다시 타이머를 다시 시작한다.
4. 누적 확인응답(ACK 수신) : SendBase는 수신 측에서 다음에 받아야하는 바이트 주소이다. 따라서 SendBase - 1은 성공적으로 전송된 마지막 바이트 번호이다.(수신자에게 정확하고 차례대로 수신되었음을 알리는 마지막 바이트의 순서번호이다.) 아직 확인 응답 안된 세그먼트들이 존재한다면 타이머를 다시 시작한다.(timeout이 되면 재전송하기 위함)  

## 데이터가 Segment로 나뉘는 과정  

일반적으로 최대 세그먼트 길이(MSS)는 가장 큰 링크 계층 프레임의 길이에 의해서 결정됩니다. 애플리케이션 계층에서 소켓을 통해 전달받은 데이터를 나누고 여기에 TCP/IP Header가 붙어야하기 때문에 `가장 큰 링크 계층 프레임 길이 - TCP header 길이 - IP Header 길이 = MSS`가 됩니다.  

전송될 데이터가 500,000 바이트이고 MSS가 1000바이트일 때 첫 번째 segment의 순서번호는 0이고 두 번째 segment의 순서번호는 1000입니다. 각각의 순서번호는 segment의 header에 추가됩니다.  

그리고 두 호스트는 동시에 서로 데이터를 주고 받을 수 있습니다. 따라서 segment의 header에 자신이 기다리고 있는 다음 바이트의 순서번호를 기록하여 전달합니다. 이를 통해서 내가 어디까지 받았는지 상대방에게 알려줄 수 있습니다.  

예를 들어서 A가 0 ~536바이트까지의 데이터를 B로부터 받았으면, A는 세그먼트를 보낼 때 확인응답번호 필드에 537을 입력하고 송신합니다. 만약 A가 B로부터 0~100 세그먼트, 101~200 세그먼트, 201~300 세그먼트를 받고 싶은데 가운데 세그먼트가 누락된다면 A는 다음 세그먼트의 확인응답번호로 101을 적어서 보낼 것입니다. 이 때 세 번째 세그먼트가 두 번쨰 세그먼트보다 먼저 받아서 순서가 틀리게 되는데 이 때의 규칙은 개발자가 직접 구현하도록 되어 있습니다. 일반적으로는 먼저 받았던 세그먼트를 저장해두었다가 사용합니다.  

## Timeout 설정

타임아웃은 연결의 왕복시간보다 좀 더 커야합니다. 만약 그렇지 않으면 불필요한 재전송이 발생합니다. 하지만 너무 크면 세그먼트를 읽어버렸을 때, TCP는 세그먼트의 즉각적인 재전송을 하지 않습니다. 따라서 타임아웃은 EstimateRTT에 약간의 여유 값을 더한 값으로 설정하는 것이 바람직합니다.  

```
EstimatedRTT =( 1 - a) * EstimatedRTT + a * SampleRTT // RTT 추정 값
DevRTT = (1 - b) * DevRTT + b * |SampleRTT - EstimatedRTT| //적당한 여유값
TimeoutInterval - EstimatedRTT + 4 * DevRTT
```

SampleRTT는 세그먼트가 송신된 시간과 그 세그먼트에 대한 긍정 응답이 도착한 시간까지의 시간 길이입니다. 모든 전송된 세그먼트에 대해서 SampleRTT를 측정하는 대신, 대부분의 TCP는 한 번에 하나의 SampleRTT만 측정합니다. SampleRTT는 혼잡과 종단 시스템에서의 부하 변화 때문에 세그먼트마다 다릅니다. 따라서 불규칙적인 값에 평균값을 채택해서 사용합니다.

권장되는 a의 값은 0.125입니다. EstimatedRTT는 SampleRTT 값의 가중평균임에 주목합니다.  

DevRTT는 RTT의 변화율을 의미합니다. 이는 SampleRTT가 EstimatedRTT로부터 얼마나 많이 벗어나있는지에 대한 예측으로 정의합니다.  

초기 TimeoutInterval의 값으로 1초를 권고합니다. 그리고 타임아웃이 발생했을 때는 TimeoutInterval을 두 배로 일시적으로 변경합니다. 이를 통해서 타임아웃에 의한 후속 확인 응답(재전송한 세그먼트)이 너무 빠른 타임아웃으로 인해서 무시되는 것을 피합니다. 그리고 EstimatedRTT가 계산되면 다시 위 공식에 따라서 TimeoutInterval을 설정합니다.  

## 중복 ACK

A가 B에게 {순서번호 92, data 8byte}, {순서번호 100, data 20byte} , {순서번호 120, data 15byte}의 세그먼트를 보낸다고 가정하겠습니다. 두 번째 세그먼트가 유실되었다면 세 번째 세그먼트를 받고나서 두 번째 세그먼트가 유실된 것을 파악할 수 있고, B가 A에게 보내는 다음 세그먼트에 포함된 확인 응답 번호는 100이 됩니다. 만약 세 번째 세그먼트 이후 네 번째 세그먼트를 받았다고 해도 B가 A에게 보내는 세그먼트의 확인응답 번호는 여전히 100입니다. 따라서 A의 입장에서는 중복된 ACK을 받습니다.  

만약 호스트가 3개의 중복된 ACK을 수신하는 경우에는 세그먼트의 타이머가 만료되기 이전에 손실 세그먼트를 재전송하는 빠른 재전송을 합니다. 타임아웃이 발생할 때마다 TimeoutInterval을 2배로 늘리기 때문에 불필요한 기다림을 최소화 하기 위함입니다.  

## 흐름제어

TCP 연결의 각 종단은 수신 버퍼를 갖습니다. 만약 송신자가 점점 더 많은 데이터를 빠르게 전송하면 연결읜 수신 버퍼는 쉽게 오버플로될 수 있습니다. 이를 위해서 흐름제어 서비스를 제공합니다. 이는 흐름제어는 속도를 일치시키는 서비스입니다. 수신하는 애플리케이션이 읽는 속도와 송신자가 전송하는 속도를 갖게 합니다.  

데이터를 받는 호스트는 송신 호스트에게 모든 세그먼트의 윈도우 필드에 현재의 수신버퍼 여유값(전체 버퍼 크기 - (마지막으로 받은 바이트 - 마지막으로 읽은 바이트))을 명시합니다. 즉, 수신 호스트는 수신 버퍼의 크기, 마지막으로 읽고/받은 바이트를 기록합니다.  

## 혼잡제어  

TCP 송신자는 IP 네트워크에서 혼잡 때문에 억제될 수 있습니다. 송신자가 억제되는 것은 같지만 흐름제어와 분명히 다른 목적을 갖습니다.  

TCP송신자는 자신과 목적지 사이에 혼잡이 없음을 감지하면 송신자는 송신율을 높이고, 혼잡이 있으면 송신율을 줄입니다. 이때 세 가지 문제가 대두됩니다.

1. 송신자는 혼잡을 어떻게 감지하는가?  
  타임아웃이나 3연속 중복 ACK 을 보고 혼잡이 일어남을 파악한다.
1. 송신자는 자신의 연결에 트랙픽 전송률을 어떻게 제한하는가?  
  혼잡 윈도우를 기록해서, 매 RTT의 시작 때 송신자는 혼잡 윈도우 만큼의 데이터를 전송한다. RTT가 끝나는 시점에 송신자의 송신율은 혼잡 윈도우 크기 / RTT Byte/second가 된다.
1. 혼잡을 감지하고 전송률을 변화시키기 위해서 어떤 알고리즘을 사용하는가?  
  혼잡 왼도우의 크기를 결정하는 것이 중요한데, 확인 응답이 낮은 속도로 오면 혼잡 윈도우는 낮은 속도로 증가하고, 확인은답이 빠른 속도로 오면 혼잡 윈도우는 높은 속도로 증가한다.  