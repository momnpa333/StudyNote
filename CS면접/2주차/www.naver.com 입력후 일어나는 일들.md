# 0. 서론

우리는 웹브라우저를 통해 너무나 일상적으로 여러 웹 사이트를 돌아다닌다.  
우리가 주소창에 서로 다른 웹 사이트 주소를 입력할 때마다, 저마다의 사이트가 우리를 반겨준다.  
우리가 웹 브라우저의 주소창에 웹 사이트 주소를 입력하고, 그 사이트가 우리의 눈에 보이기까지 어떤 과정이 일어나는 것일까?

# 1. 서버와 클라이언트 <=> 고객과 요리사?

우리가 보는 모든 웹 페이지(네이버, 구글, 유튜브 등등)는 모두 HTML이라고 하는 하나의 **문서파일**이다.  
(아래는 크롬 개발자도구를 통해 유튜브의 HTML을 열어본 이미지이다.)  
![](https://velog.velcdn.com/images/fastdodge7/post/8152c28e-c437-4ea9-bc53-578b6d347bc1/image.png)

우리가 주소창에 `www.naver.com`을 치는 행위는 곧 네이버의 웹서버에게 네이버 메인 화면에 대한 정보를 갖고 있는 HTML 문서 파일을 요청(Request)하는 행위인 것이다.  
네이버의 웹서버는 사용자의 이러한 요청(Request)에 대해 네이버 웹페이지의 HTML 문서 데이터를 그에 대한 응답(Response)로서 사용자에게 전달해 준다.

# 2. DNS - 웹서버는 그래서 어디에 있나요?

우리가 사용하는 컴퓨터, 스마트폰 등 인터넷에 연결되는 기기에는 모두 IP주소가 부여되어 있듯이, 우리가 HTML 문서를 요청하는 **웹서버 역시 IP 주소를 가지고 있다**.

상대방에게 택배를 보내기 위해서는 상대방의 주소를 알아야 하듯, 우리가 특정 **웹서버에게 HTML 문서를 요청하기 위해**서는 당연히 해당 **웹서버의 IP주소를 알고 있어야 한다**.

위에서 예시로 들었던 네이버 메인 웹 페이지 정보를 갖는 웹서버의 주소는 `125.209.222.141`이다. 당장 웹 브라우저에 `www.naver.com` 대신 `125.209.222.141`를 입력하면 우리에게 익숙한 네이버 메인 화면이 뜬다.

그런데 우리는 네이버에 접속할 때 `www.naver.com`이란 주소를 입력하지 저런 IP주소를 직접 입력하지 않는다.

`www.naver.com`이란 주소를 통해 `125.209.222.141`이란 IP주소를 찾아주는 작업이 필요한데, **DNS(Domain Name System)**이 이러한 서비스를 제공해 준다.

> **도메인 네임 시스템(Domain Name System, DNS)**은 호스트의 도메인 이름을 호스트의 네트워크 주소로 바꾸거나 그 반대의 변환을 수행할 수 있도록 하기 위해 개발되었다.  
> [출처 : 위키피디아 - 도메인 네임 시스템](https://ko.wikipedia.org/wiki/%EB%8F%84%EB%A9%94%EC%9D%B8_%EB%84%A4%EC%9E%84_%EC%8B%9C%EC%8A%A4%ED%85%9C).

_여기서 도메인 이름이란, 우리가 일상적으로 보는 url 주소, 즉 `www.naver.com`과 같은 주소를 말한다._

DNS가 하는 일은 생각보다 간단하다. 마치 전화번호부처럼, 도메인 이름과 IP주소 쌍을 가지고 있는 DB에서 우리가 입력한 도메인 이름에 해당하는 IP 주소를 꺼내 반환하는 것이다.

그렇다면 DNS는 브라우저나, 우리 컴퓨터에 내장되어 있을까? 그렇지 않다.  
DNS 역시 별도의 서버를 가지고 있고, 우리가 DNS를 통해 주소변환을 하려면 인터넷을 통해 요청해야 한다.  
그렇다면, **DNS 서버의 IP주소**는 어떻게 알아내는 것일까?

답은 간단하다. DNS는 우리가 이용하는 인터넷 서비스 제공자(ISP)가 가지고 있는 서버에 존재하는데 이 서버의 주소는 잘 알려져 있다. 우리가 주소창에 도메인 주소를 입력하면 브라우저에서 자동적으로 ISP가 제공하는 DNS 서버에 주소변환을 요청하게 된다.

## DNS 주소변환 과정

### 1. 브라우저가 캐시에서 www.naver.com에 대응되는 IP주소가 존재하는지 확인한다.

우리가 매번 웹사이트에 접속할 때마다 DNS를 통해 주소변환을 한다고 하자.  
그러면 아래와 같은 과정을 거치게 된다.

1. 사용자가 도메인 주소 입력
2. DNS 서버에 대응되는 IP주소 요청
3. DNS 서버에서 도메인 주소에 해당하는 IP주소를 탐색
4. DNS 서버에서 IP주소를 반환
5. 반환받은 IP주소로 원하는 웹서버에 요청을 보냄
6. 웹서버에서 사용자에게 필요한 데이터를 응답으로 반환

렇듯 DNS서버를 거치면서 시간이 지연되고, 응답 시간이 나빠질 것이다.

우리가 만약 자주 접속하는 사이트가 있다면, 해당 사이트의 **도메인 주소와 IP주소 쌍을 저장해두고(캐싱)** 이 정보를 나중에 재활용하면 주소변환 시간이 줄어들어 응답 시간을 개선할 수 있을 것이다.

우리의 똑똑한 브라우저는 DNS서버에서 IP주소를 직접 탐색하기 전까지 총 4개의 캐시를 탐색한다.

1. 브라우저 자신이 저장하고 있는 캐시를 가장 먼저 탐색한다. 브라우저는 사용자가 방문했던 웹페이지에 대한 도메인 주소 - IP주소쌍을 일정 시간동안 보관한다.
    
2. 그 다음으로, OS에 저장된 캐시를 탐색한다. 대표적으로 윈도우에는 hosts라는 파일이 존재하는데, 이 파일은 DNS에서 제공해 줬던 주소정보를 기록하고 있다.
    
3. 그 다음으로, 라우터(공유기)에 저장되어 있는 캐시 정보를 탐색한다. 여기까지는 DNS서버에게 주소변환을 요청할 필요가 없다.
    
4. 마지막으로, ISP에서 관리하는 DNS 서버(로컬 DNS 서버)의 캐시를 확인한다. DNS 서버는 자주 요청받는 도메인 주소에 대해 IP주소정보를 캐싱한다. 이를 통해 DNS 서버에서 주소정보를 직접 탐색하는 오버헤드를 줄일 수 있다.
    

여기까지 왔는데도 적절한 주소정보를 발견하지 못했다면, DNS 서버 내부 데이터를 탐색하여 주소정보를 가져올 필요가 있다.

# 2. DNS 서버에서 요청받은 www.naver.com에 대응되는 IP주소 정보를 탐색

위 과정을 모두 마쳤음에도 적절한 IP주소 정보를 발견하지 못한 경우, ISP의 DNS 서버는 다른 DNS서버에 `www.naver.com`에 해당하는 IP주소 정보를 질문하기 시작한다.  
![](https://velog.velcdn.com/images/fastdodge7/post/a1eb716f-1c83-4278-ada9-b0384eff692b/image.png)

1. local DNS 서버(ISP의 DNS 서버)는 Root DNS서버에게 `www.naver.com`에 해당하는 IP주소 정보에 대해 요청한다.  
    (참고로 Root DNS서버는 전 세계에 13개 존재한다. 우리나라에는 해당 서버의 미러서버가 존재한다.)
2. Root DNS서버는`www.naver.com`에 대한 주소 정보를 모른다. 대신 .com으로 끝나는 도메인에 대한 DNS 정보가 담긴 서버의 IP주소를 반환해 준다.
3. 반환받은 주소를 통해 .com DNS서버로 다시`www.naver.com`에 대한 IP주소정보를 요청한다.
4. .com DNS서버는 `www.naver.com`의 IP주소 정보를 모른다. 대신 naver.com으로 끝나는 도메인에 대한 DNS 정보가 담긴 서버의 IP주소를 반환해준다.
5. 반환받은 주소를 통해 naver.com DNS서버로 다시`www.naver.com`에 대한 IP주소정보를 요청한다.
6. naver.com DNS서버는 `www.naver.com`의 IP주소정보가 저장되어 있다.(그 외에도 `mail.naver.com`등 다른 도메인에 대한 DNS 정보도 담고 있다.)
7. 이를 통해 최종적으로 `www.naver.com` 웹서버의 IP주소를 획득한다.

길고 긴 과정이었다. 앞서 보았던 단계에서 4개씩이나 되는 캐시를 탐색하는 이유가 있었던 것이다. 성능을 생각한다면, 이렇게 DNS 서버망을 모두 돌아다니는 일은 최대한 피하고 싶을 것이기 때문이다.  
또한, 여러 서버를 오고가기 때문에, 중간에 패킷이 손실되는 등의 상황이 발생하면 **요청 실패** 오류가 발생할 수도 있다.

# 3. 웹서버와 TCP 연결을 수립한다.

브라우저가 마침내 `www.naver.com` 웹서버의 IP주소를 알아내면, 브라우저는 해당 웹서버와 **TCP(Transmission Control Protocol)**연결을 수립한다.

**TCP는** 인터넷 프로토콜(IP, Internet Protocol)의 일종으로 상호간의 교환하는 데이터의 신뢰성을 보장해 주는 인터넷 프로토콜이다.

TCP를 통해 데이터를 전송하려면, 우선 TCP 연결을 수립하는 과정이 선행되어야 한다. 이 과정은 TCP/IP 3-way handshake라는 연결 과정을 통해 이뤄진다.

1. 클라이언트는 인터넷을 통해 서버에 SYN 패킷을 보내 새 연결이 가능한지 여부를 묻는다.
    
2. 서버에 새 연결을 수락할 수 있는 열린 포트가 있는 경우, SYN/ACK 패킷을 사용하여 SYN 패킷의 ACK(승인)으로 응답한다.
    
3. 클라이언트는 서버로부터 SYN/ACK 패킷을 수신하고 ACK 패킷을 전송하여 승인한다.
    

이제 서버와 브라우저간의 TCP 연결 수립이 완료되었다.

# 4. 브라우저와 웹서버가 HTTP를 통해 데이터를 주고받는다.

> HTTP(Hypertext Transfer Protocol)는 클라이언트와 서버 간 통신을 위한 통신 규칙 세트 또는 프로토콜입니다. 사용자가 웹 사이트를 방문하면 사용자 브라우저가 웹 서버에 HTTP 요청을 전송하고 웹 서버는 HTTP 응답으로 응답합니다.  
> 출처 : [HTTP와 HTTPS 비교 - 전송 프로토콜 간의 차이점 - Amazon AWS](https://aws.amazon.com/ko/compare/the-difference-between-https-and-http/)

TCP 연결이 수립된 두 기기는 이제 HTTP 프로토콜을 통해 서로 데이터를 주고받는다.  
먼저 브라우저 측에서 웹서버에게 `www.naver.com`에 해당하는 웹페이지의 HTML문서를 요청한다. 이때 HTTP 프로토콜의 GET 메서드가 이용된다.

요청을 받은 웹서버는 HTTP 응답으로 `www.naver.com`의 HTML 문서 데이터를 전달해 준다.

# 5. 브라우저가 전달받은 HTML 문서를 파싱하여 사용자에게 화면으로 그려준다.

브라우저에서는 전달받은 HTML 문서를 다음 과정을 거쳐서 화면에 그린다.

1. HTML 파일과 CSS 파일을 파싱해서 각각 Tree를 만든다. (Parsing)
2. 두 Tree를 결합하여 Rendering Tree를 만든다. (Style)
3. Rendering Tree에서 각 노드의 위치와 크기를 계산한다. (Layout)
4. 계산된 값을 이용해 각 노드를 화면상의 실제 픽셀로 변환하고, 레이어를 만든다. (Paint)
5. 레이어를 합성하여 실제 화면에 나타낸다. (Composite)