---
title: "[Backend] CDN 콘텐츠 분배 네트워크"
excerpt: ""
date: 2020-07-28
categories:
  - Backend
tags:
  - backend
  - cdn
toc : false
toc_label: "Table of contents"
toc_icon: "list"  # corresponding Font Awesome icon name (without fa prefix)
toc_sticky: true
classes: wide
---

## ISP, Internet Service Provider

사용자의 입장에서 접속 ISP는 텔코나 케이블 회사일 필요가 없다. 대신 대학교 또는 회사가 ISP가 될 수 있다. 이러한 연결은 종단 시스템이 연결되는 과정에서 극히 일부이고 접속 ISP들이 서로 연결되어야만 한다.  

최초의 ISP 연결은 모든 접속 ISP는 하나의 글로벌 ISP와 연결하는 방법이다. 만약 또 다른 글로벌 ISP 회사가 등장하면 다수의 ISP들이 연결된다. 접속 ISP 입장에서는 글로벌 ISP의 가격과 서비스를 선택할 수 있는 폭이 넓어진다.  

전 세계 모든 도시에 존재하는 ISP는 없다. 대신 주어진 지역에서 그 지역에 있는 접속 ISP들이 연결하는 지역 ISP가 있다. 각 지역 ISP는 1 계층 ISP(글로벌 ISP와 비슷함)들과 연결된다.  

접속 ISP는 지역 ISP에게, 지역 ISP는 1계층 ISP에게 비용을 지불한다. 이 비용을 줄이기 위해서 같은 계층에 있는 가까운 ISP와 peering할 수 있따. 즉, 이들 간에 송수신되는 모든 트래픽을 상위 ISP를 통하지 않고 직접 송수신할 수 있도록 자신들의 네트워크를 서로 직접 연결할 수 있다. peering하는 경우 서로 비용을 지불하지 않는다.  

## IXP, Internet Exchange Point

다중의 ISP들이 서로 피어링할 수 있는 만남의 장소이다. 일반적으로 교환기를 갖춘 독자적인 건물이다. 

## 단일 데이터 센터를 통한 스트리밍 서비스

안정적인 스트리밍 서비스를 제공하기 위한 네트워크이다. 단일한 거대 데이터 센터를 구축하고 모든 비디오 자료를 데이터 센터에 저장한 뒤 전 세계의 사용자에게 비디오 스트림을 데이터 센터로부터 전송하는 방법이 가장 기초적이다. 하지만 이 구조는 3가지 문제점이 있다.

1. 클라이언트가 데이터 센터로부터 지역적으로 먼 지점에 있는 경우, 연결된 링크 중 하나라도 비디오 소비율보다 낮은 전송용량을 갖는다면 종단간 처리율이 낮아지고 결국 사용자는 화면 정지 현상을 겪는다.
1. 인기 있는 비디오가 같은 통신 링크를 통해 여러번 반복적으로 전송된다. 이는 네트워크 대역폭의 낭비는 물로닝고 ISP에게 동일한 바이트를 전송하는 것에 대한 중복 비용을 지불하게 된다.
1. 한 번의 장애가 서비스 전체를 중단시킬 수 있다.  

따라서 대부분의 스트리밍 서비스는 단일 데이터 센터보다 CDN을 사용한다.  

## CDN

다수의 지점에 분산된 서버를 운영하며 데이터의 복사본을 이들 분산 서버에 저장한다. 사용자는 가까운 지점의 CDN 서버로 여녈된다. CDN은 컨텐츠 제공자가 소유한 사설 CDN일 수도 있고 제 3자가 운영하는 CDN일 수도 있다.  

CDN을 구축하는 두 가지 철학이 있다. 첫 번째 철학은 전세계에 많은 지점 서버 클러스터를 구축하는 것이다. 서버를 최대한 사용자 가까이에 위치시켜 CDN 서버와 사용자 사이의 링크 및 라우터 수를 줄인다. 속도가 빠르지만 서버 클러스터 관리 비용이 높다. 두 번째 철학은 접속 ISP에 연결하는 대신 CDN들을 일반적으로 접속 ISP들의 클러스터를 IXP에 배치하는 것이다. 지연 시간과 처리율이 상대적으로 나쁘지만 서버 클러스터 유지 관리 비용이 줄어든다.  

서버 클러스터의 위치가 정해지면 CDN은 콘텐츠의 복사본을 이들 클러스터에 저장한다. 각 CDN이 관리하는 지역에서 인기 있는 데이터만 복사해둔다. CDN은 클러스터에 대해서 Push  방식이 아니라 Pull 방식을 사용한다. 어떤 사용자가 지역 클러스터에 없는 비디오를 요청하면 해당 비디오를 중앙 서버나 다른 클러스터로부터 전송받아 사용자에게 서비스하는 동시에 복사본을 만들어 저장한다. 저장 공간이 가득차면 잘 사용되지 않는 비디오는 삭제된다.  

## CDN 동작

사용자 호스트의 웹 브라우저가 URL을 지정함으로써 특정 비디오의 재생을 요청하면 CDN은 그 요청을 가로채 가장 적당한 CDN을 선택해서 해당 클러스터의 서버로 연결한다. NetCinema라는 콘텐츠 제공자와 KingCDN이라는 CDN 업체 사이의 예를 들어보자. 각 콘텐츠는 http://video.netcinema.com/6Y7B23V라는 URL을 할당받는다.  

1. 사용자가 NetCinema 웹 페이지를 방문한다.
1. 사용자가 http://video.netcinema.com/6Y7B23V 링크를 클릭하면, 사용자의 호스트는 video.netcinema.com에 대한 DNS Query를 보낸다.
1. 사용자의 지역 DNS 서버는 호스트 이름의 video 문자열을 감지하고, 해당 query를 NetCinema의 책임 DNS 서버로 전달한다.
1. NetCinema의 책임 DNS서버는 해당 DNS Query를 KingCDN으로 연결하기 위해 IP 주소 대신에 KingCDN의 호스트이름(a1105.kingcdn.com)을 Local DNS에게 알려준다. 
1. 이 시점부터 DNS Query는 KingCDN의 사설 DNS 구조로 들어간다. 사용자가 a1105.kingcdn.com에 대한 두 번째 쿼리를 보내면 이는 KingCND의 DNS에 의해 KingCDN 콘텐츠 서버의 IP 주소로 변환되어 LDNS에게 응답된다. 이 때 클라이언트가 콘텐츠를 전송받게 될 서버가 결정된다.  
1. 클라이언트가 KingCDN의 IP 주소를 얻고나면, 해당 IP 주소로 직접 TCP 연결을 설정하고 비디오에 대한 HTTP Get 요청을 전송한다. 

CDN 입장에서는 클라이언트가 DNS를 이용하는 과정에서 클라이언트의 LDNS 서버의 IP 주소를 알게 된다. 이를 기반으로 최선의 클러스터를 선택한다. 

## 넷플릭스

넷플릭스는 아마존 클라우드(서버,저장소)와 자체 CDN 인프라를 사용한다. 아마존 클라우드에서 처리하는 기능에는 로그인,결제,영화 장르 검색, 영화 추천서비스 등이 포함된다. 좀 더 자세히 보면, 컨텐츠 수집을 할 때는 영화의 스튜디오 마스터 버전을 전달받아서 아마존 클라우드 시스템의 호스트에 업로드한다. 처리할 때는 고객들의 다양한 플레이어 기기 사양에 적합하도록 각 영화의 여러 가지 형식의 비디오를 생성한다. 그리고 각각의 형식별로 다양한 비트레이트를 적용한다. 영화에 대한 다양한 버전이 생성되면 아마존 클라우드 시스템의 호스트는 이들 파일을 CDN에 업로드 한다.  

넷플릭스는 IXP 및 거주용 ISP 자체에 서버 랙을 설치했다. 각각의 랙 서버에는 10Gbps 이더넷 포트와 100테라바이트 이상의 스토리지가 있다. 렉에 있는 서버의 수는 다양하다. 로컬 IXP는 하나의 서버만 가지고 가장 인기 있는 비디오만 포함할 수 있다.  

넷플릭스 CDN의 캐시는 일반적으로 사용하는 pull cache를 사용하고, 사용량이 적은 시간에 비디오를 CDN으로 옮겨 배포한다.  