HTTPS 통신과정에서도 송신자와 수신자가 암호화 통신을 하기 위한 방법과 수단에 대해 공유합니다. 즉 데이터를 암호화할 대칭키(비밀키)를 타인에게 노출시키지 않고 Client가 Server에게 전송하기 위해 협상을 벌이는 것이죠. 이번에 다룰 **SSL Handshake(TLS Handshake)**가 그 방법으로, 송신자와 수신자가 암호화된 데이터를 교환하기 위한 일련의 협상과정을 의미합니다. 협상과정에는 SSL 인증서 전달, 대칭키(비밀키) 전달, 암호화 알고리즘 결정, SSL/TLS 프로토콜 결정 등이 포함됩니다.

![](https://blog.kakaocdn.net/dn/cStny6/btqCTwn2FTq/1U8WuM3ZgMKbrtaLgPKNvk/img.png)

<SSL handshake의 과정(출처 : Cloudflare)>

SSL Handshake의 과정을 그린 그림을 가져왔습니다. 여기 **파란색** 칸과 **노란색** 칸은 네트워크 상에서 전달되는 IP Packet을 표현한 것입니다. 맨 윗줄의 **SYN, SYN ACK, ACK**는 TCP layer의 3-way handshake로 HTTPS가 TCP 기반의 프로토콜이기 때문에 암호화 협상(SSL Handshake)에 앞서 연결을 생성하기 위해 실시하는 과정이고 아래 **노란색 상자의 패킷**들이 SSL Handshake입니다.

> 1. 암호화 알고리즘(Cipher Suite) 결정
> 
> ​
> 
> 2. 데이터를 암호화할 대칭키(비밀키) 전달

위 두 가지를 기억하면서 순서대로 보시길 권장합니다.

**Client Hello**

Client가 Server에 연결을 시도하며 전송하는 패킷입니다. 자신이 **사용 가능한 Cipher Suite 목록, Session ID, SSL Protocol Version, Random byte** 등을 전달합니다. Cipher Suite는 SSL Protocol version, 인증서 검증, 데이터 암호화 프로토콜, Hash 방식 등의 정보를 담고 있는 존재로 선택된 Cipher Suite의 알고리즘에 따라 데이터를 암호화하게 됩니다. 아래 사진을 보면 Client가 사용 가능한 Cipher Suite를 Sever에 제공하는 것을 알 수 있습니다.

![](https://blog.kakaocdn.net/dn/CNmBF/btqCQoSgXrr/tDeqBcxzzKxfGJQQ9oX5f0/img.png)



**Server Hello**

Client가 보내온 ClientHello Packet을 받아 Cipher Suite 중 하나를 선택한 다음 Client에게 이를 알립니다. 또한 자신의 SSL Protocol Version 등도 같이 보냅니다. 아래 사진을 보면 ClientHello에서 17개였던 Cipher Suite와 달리 아래에서는 Server가 선택한 한 줄(Cipher Suite: TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256)만이 존재하는 것을 알 수 있습니다. Client가 보낸 리스트에 있는 Cipher Suite 중에 한 개를 선택한 것이지요.

![](https://blog.kakaocdn.net/dn/K9RpI/btqCUxz0g9M/KdEcN5kyKkFCk2cKZAgT61/img.png)

<ServerHello Packet>

**Certificate**

Server가 자신의 SSL 인증서를 Client에게 전달합니다. 인증서 내부에는 Server가 발행한 **공개키(+개인키는 Server가 소유)**가 들어있습니다. Client는 Server가 보낸 **CA(Certificate Authority, 인증기관)의 개인키**로 암호화된 이 SSL 인증서를 이미 모두에게 공개된 **CA(Certificate Authority, 인증기관)의 공개키**를 사용하여 복호화합니다. 복호화에 성공하면 이 인증서는 CA(Certificate Authority, 인증기관)가 서명한 것이 맞는 것이니 진짜임이 증명되는 것이죠. 즉, 인증서를 검증하는 것입니다.

또한 Client는 데이터 암호화에 사용할 **대칭키(비밀키)**를 생성한 후 SSL 인증서 내부에 들어 있던 (Server가 발행한) 공개키를 이용해 암호화하여 Server에게 전송할 것입니다. 아래를 보면 SSL 인증서가 무슨 알고리즘(RSA)으로 암호화되었고, 무슨 Hash 알고리즘(SHA256)으로 서명되었는지 확인 가능합니다.

![](https://blog.kakaocdn.net/dn/8xpEy/btqCUdu4lru/3MMhBWMBvjyh5xK9UtruiK/img.png)

<Certificate Packet>

**Server Key Exchange / ServerHello Done**

'Server Key Exchange'는 Server의 **공개키**가 SSL 인증서 내부에 없는 경우, Server가 직접 전달함을 의미합니다. **공개키**가 SSL 인증서 내부에 있을 경우 Server Key Excahnge는 생략됩니다. 인증서 내부에 공개키가 있다면 Client가 **CA(Certificate Authority, 인증기관)의 공개키**를 통해 인증서를 복호화한 후 Server의 공개키를 확보할 수 있습니다. 그리고 Server가 행동을 마쳤음을 전달합니다.

![](https://blog.kakaocdn.net/dn/cdTyNE/btqCVEZOpha/8rGCckSXEj8QU2ztYzDqf0/img.png)

<Server Key Excahnge>

위의 경우, 키 교환 알고리즘이 Diffie-Hellman(DH, DHE 등) 알고리즘이기에 키 교환 재료를 서로 교환하므로 'Server Key Exchange'를 발생시키는 것입니다. 아래 'Client Key Exchange'에서 자세히 설명합니다.

**Client Key Exchange**

**대칭키(비밀키, 데이터를 실제로 암호화하는 키)**를 Client가 생성하여 SSL 인증서 내부에서 추출한 Server의 공개키를 이용해 암호화한 후 Server에게 전달합니다. 여기서 전달된 '대칭키'가 바로 **SSL Handshake의 목적**이자 가장 중요한 수단인 데이터를 실제로 암호화할 **대칭키(비밀키**)입니다. 이제 키를 통해 Client와 Server가 교환하고자 하는 데이터를 암호화합니다.

![](https://blog.kakaocdn.net/dn/IAqPh/btqCTxmZtMn/KUG9Tyo6tSmNV7obViDJvk/img.png)

<Client Key Exchange>

위의 캡쳐화면을 보고 의아한 생각이 드실겁니다. Client가 데이터를 암호화할 대칭키(비밀키)를 보낸다고 했는데 'EC Diffie-Hellman Client Params', 즉 키를 생성할 재료를 보낸다는 것으로 보이네요. 키교환 알고리즘을 RSA가 아닌 Diffie-Hellman(DH, DHE 등) 알고리즘과 타원곡선 암호인 ECDHE(Elliptic Curve DHE)을 사용하게 된다면 Client가 데이터를 암호화할 대칭키(비밀키)를 보내는 것이 아니라 대칭키(비밀키)를 생성할 재료를 Client와 Server가 교환하게 됩니다. 그리고 서로 교환한 각자의 재료를 합쳐 동일한 대칭키를 만들 수 있답니다. Diffie-Hellman(DH, DHE 등)과 타원곡선 암호인 ECDHE(Elliptic Curve DHE)의 특징입니다. RSA 키교환 알고리즘을 사용하게 되면 Client가 대칭키(비밀키)를 생성하여 인증서 내부에 들어 있던 서버의 공개키로 암호화 한 후 전달하게 됩니다.

**ChangeCipherSpec / Finished**

Client, Server 모두가 서로에게 보내는 Packet으로 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알리는 패킷입니다. 그리고 'Finished' Packet을 보내어 SSL Handshake를 종료하게 됩니다.

![](https://blog.kakaocdn.net/dn/z5FYE/btqCUyZ4tgo/RQGmgVkFERkkfPQvUHyNs1/img.png)

<Change Cipher Spec>

여기까지가 SSL Handshake입니다. 요약해보면 ClientHello(암호화 알고리즘 나열 및 전달) - > Serverhello(암호화 알고리즘 선택) - > Server Certificate(인증서 전달) - > Client Key Exchange(데이터를 암호화할 대칭키 전달) - > Client / ServerHello done (정보 전달 완료) - > Fisnied (SSL Handshake 종료) 이정도로 나열할 수 있습니다. 과정이 끝나면 Client와 Server는 데이터를 암호화할 동일한 대칭키(비밀키)를 갖게 되며, 서로에게 각자 갖고 있는 동일한 대칭키를 통해 데이터를 암호화하여 전송하거나 데이터를 복호화합니다. Client와 Server는 이제 성공적으로 비밀친구가 되었습니다.

여기까지가 SSL Handshake의 과정에 대한 전반적인 내용입니다. 사실 RSA나 DHE와 같은 알고리즘에 대한 언급이 없다보니 약간 나사가 빠진 기분이 드네요. 그래서 다음 편에 **Cipher Suite**를 다룰 예정인데요. Cipher Suite를 이해하시고 나면 좀더 쉽게 느껴지지 않을까 생각합니다. 감사합니다.

​

**HTTPS 통신과정 쉽게 이해하기** [**#4(Cipher Suite, 암호문의 집합)**](https://aws-hyoh.tistory.com/47)

​

이 문서에서 Cipher Suite에 대해 중점적으로 다루고자 합니다. 그 전에 [HTTPS 통신과정 쉽게 이해하기 #3(SSL Handshake, 협상과정)](https://aws-hyoh.tistory.com/entry/HTTPS-%ED%86%B5%EC%8B%A0%EA%B3%BC%EC%A0%95-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3SSL-Handshake)에서 언급한 Ciper Suite를 다시 정리해보겠습니다.

> 1. SSL Handshake는 암호화 알고리즘 결정과 대칭키(비밀키) 전달을 위해 사용
> 
> 2. SSL Handshake의 첫 단계, Client가 'Client Hello'로 협상을 시작할 때 자신이 사용 가능한 'Cipher Suite'를 쭉 나열
> 
> 3. Server가 'ServerHello'를 통해 자신이 고른 'Cipher Suite'를 전달

SSL Handshake의 첫 협상 단계인 'Client Hello'와 'Server Hello'에서 '**Cipher Suite**'가 가장 먼저 언급되는 것을 통해 'Cipher Suite'의 중요성을 알 수 있습니다. 'Cipher Suite'를 정하지 않고서는 다음 모든 단계를 처리할 수 없다는 뜻이니까요.

**Cipher Suite**

'Cipher'와 'Suite'를 쪼개어 단어를 찾아보았더니 다음과 같은 의미가 나왔습니다.

> * Cipher
> 
> 1.(글로 쓰인) 암호 2. 하찮은 사람 3.(특별히 디자인된) 이름 첫 글자들
> 
> ​
> 
> * Suite
> 
> 1.(특히 호텔의) 스위트룸(연결된 몇 개의 방으로 이루어진 공간) 2.(가구·용품) 세트 3. 모음곡
> 
> ​
> 
> 출처 : 네이버 지식백과

'Cipher'의 1번 의미와 'Suite'의 3번 의미를 조합해보면 암호 모음곡(?) 정도가 되겠네요. 암호를 모아 만든 집합소로 해석하면 될 것 같습니다. 이름 한 번 참 어렵게 짓네요...... 그럼 모인 암호들을 그림으로 표현하면 어떻게 되는지 다시 한번 보겠습니다.

![](https://blog.kakaocdn.net/dn/qg6sA/btqCZ1nMKr5/6MOMdBgYbeUEEHNZT90KyK/img.png)

<Cipher Suite>

위의 그림이 **Cipher Suite**로 암호화 협상을 위한 알고리즘을 무엇을 쓸 것인지 모아둔 것입니다. SSL/TLS 프로토콜, 키 교환 방식, 인증서 검증, 블록 암호화 방식, 메시지 인증... 양이 좀 많군요; 그럼 프로토콜부터 하나씩 살펴보도록 하겠습니다!

**SSL/TLS Protocol**

SSL Handshake를 기반이 되는 SSL/TLS Protocol의 버전을 의미합니다. 보통 SSLv3, TLSv1.0, TLSv1.1, TLSv1.2등이 있고 Cipher suite에서는 SSL 혹은 TLS로 표기됩니다. ClientHello Packet에서 'Version' 항목을 통해 확인할 수 있습니다. 아래 보시면 TLS 1.0 임을 확인하실 수 있죠?

최근에 Google Chrome에서 TLSv1.0, TLSv1.1의 지원을 종료하겠다는 발표가 있었지요. 사용 가능한 Version 중 TLSv1.0, TLSv1.1을 빼겠다는 뜻이 됩니다. 해당 버전의 프로토콜을 빼게 되면 해당 프로토콜과 연결된 Cipher Suite는 사용할 수 없습니다.

![](https://blog.kakaocdn.net/dn/Kys6j/btqCYE0Vz5A/XsFmQbHfKkRomXr3bP0qz1/img.png)

<TLS Version 확인>

**키 교환 방식(키 교환 알고리즘)**

SSL Handshake의 **목표**이자 데이터를 암호화하는 실질적인 키인 **대칭키(비밀키)**를 서버에게 전달하기 위한 방법(알고리즘) 선택을 의미합니다. 주로 ECDHE, RSA 등이 사용됩니다. 여기서 잠깐 짚고 넘어가야 할 것은 RSA와 Diffie-Hellman(DH)입니다.

> * RSA(로널드 라이베스트(R), 아디 샤미르(S), 레너드 애들먼(A) 이름의 앞글자) 암호
> 
> 공개키 알고리즘입니다. 앞서 HTTPS 통신과정 #2에서 한 쌍의 공개키와 개인키가 있다고 말씀드렸던 게 기억나시나요? 바로 각기 다른 키인 공개키와 개인키를 이용하여 암호화/복호화하는 알고리즘입니다. 공개키로 암호화하여 개인키로 복호화하거나, 개인키로 암호화하여 공개키로 복호화합니다. CA(Certificate Authority)가 자신이 발급한 인증서를 암/복호화하는데도 사용하죠.
> 
> ​
> 
> * DH(Diffie-Hellman) 키 교환
> 
> RSA와는 약간 다른 알고리즘이지만 데이터를 암호화/복호화하기 위해 하나의 키를 쓰는 알고리즘입니다. RSA가 공개키와 개인키를 생성하여 공개키를 모두에게 공개하는 것과 달리, DH 키 교환 알고리즘은 Parameter(매개변수), 즉 키를 생성할 재료를 교환합니다. 매개변수는 공개적으로 교환하며 노출되어도 상관없습니다. 매개변수를 교환한 후, Client와 Server 모두 동일한 대칭키(비밀키)를 얻게 됩니다. 종류로는 DH, DHE(Diffie-Hellman Ephermeral), ECDHE(Elliptic Curve Diffie-Hellman Exchange)가 있습니다.

이전 글에서 인증기관인 CA가 발급한 SSL 인증서에 서버가 발행한 공개키가 들어있다고 말씀드렸는데 기억나시나요? SSL 인증서에 서버의 공개키가 들어있다는 것은 서버가 대칭키(비밀키)를 암호화하기 위한 키 교환 알고리즘 선택 시 RSA를 선택했다는 것을 의미합니다. 왜냐하면 공개키가 있다는 것은 곧 서버가 공개키-개인키 쌍(RSA)을 생성하여 SSL 인증서 생성 시 공개키를 제출했기 때문이죠. 다시 말해 공개키 암호 방식을 선택한 것입니다. 그리고 SSL Handshake의 'Client Key Exchange' 과정에서 사용자는 서버의 공개키를 이용해 대칭키(비밀키)를 암호화하고 서버에 전달하게 됩니다.

Diffie-Hellman(DH)를 선택하면 어떻게 될까요? 위의 설명처럼 Diffe-Hellman(DH) 알고리즘은 공개키-개인키 쌍을 생성하지 않지 않고 Key Paramenter(이하 키 매개변수)만을 교환하면 동일한 대칭키(비밀키)를 얻을 수 있기 때문에 SSL 인증서 내부에 서버의 공개키가 존재하지 않습니다. 그리고 사용자와 서버는 SSL Handshake의 'Client Key Exchange'와 'Server Key Exchange'를 통해 서로 키 매개변수를 교환합니다.

**인증서 검증**

말 그대로 인증서를 검증하기 위한, 인증기관인 CA가 발급한 SSL 인증서를 검증하기 위한 알고리즘을 선택합니다. 여기서 검증이란 CA의 개인키로 암호화된 SSL 인증서를 CA의 공개키로 복호화하는 과정을 의미합니다. SSL 인증서가 CA가 발급한 것이 맞다면 CA가 공개한 공개키로 인증서가 복호화될 것입니다. 만약 인증서가 RSA 알고리즘으로 암호화되어있다면 당연 서버는 RSA 알고리즘이 담긴 Cipher Suite를 선택하겠지요?

> 인증서 검증에는 RSA 혹은 타원곡선(ECC) 암호 알고리즘 ECDSA만이 가능합니다. 대칭키 하나만을 갖는 DHE를 쓸 경우, 인증서가 진짜인지 가짜인지 밝힐 수단이 없습니다. 암호화에도 복호화에도 하나의 키를 이용한다면 인증기관인 CA가 이 키를 공개할 수도, 그렇다고 공개하지 않을 수도 없거든요. 반면, RSA는 공개키와 개인키가 존재하기 때문에 암호화와 복호화에 각각 사용할 수 있습니다.

**암호화 알고리즘(블록 암호화 방식)**

HTTPS 통신과정은 데이터를 암호화하여 전송하기 위한 협의 과정이라고 말씀드린 적이 있지요. '암호화 알고리즘'은 데이터를 실질적으로 암호화하기 위해 사용되는 알고리즘입니다. Client와 Server가 공유하는 **대칭키(비밀키)**와 선택한 **암호화 알고리즘(블록 암호화 방식)**을 가지고 데이터를 암호화하게 됩니다. 블록 암호화 방식으로 불리는 이유는 데이터를 일정 길이의 블록으로 쪼개어 암호화하기 때문입니다. 보통 암호화에 사용되는 대칭키(비밀키)의 길이가 길면 길수록 암호화의 강도가 강해지고 암호화/복호화에 사용되는 리소스 소모가 커지는데요. 이 키 길이는 어떤 암호화 알고리즘을 사용하느냐에 따라 달라집니다. 암호화 알고리즘의 종류와 키 길이, 블록 길이는 다음과 같습니다.

![](https://blog.kakaocdn.net/dn/bz5lnK/btre7JLzda0/KgY1DsROyMh6QmY2KfsQFK/img.png)

<출처 : IT 위키(https://itwiki.kr/w/암호화알고리즘)>

또한 암호화 알고리즘은 '**양방향**'이라는 특성을 갖기 때문에 원본 데이터를 암호화 데이터로 암호화할 수 있고 암호화 데이터를 원본 데이터로 복호화할 수 있습니다.

**메시지 인증 코드(Message Authentication Code, MAC)**

암호화된 데이터를 서로 주고받는다 하더라도 이 데이터가 정말로 누군가의 조작에 의해 변했는지, 변하지 않았는지 알 수 없습니다. 이를 확인하기 위해 특정 알고리즘을 사용해 원본 데이터를 일정한 길이의 암호화된 문자열로 변경합니다. **이 특정 알고리즘은 원본 데이터마다 일정한 결괏값을 도출하며 원본 데이터가 변경되면 결괏값 또한 달라지는 특성**을 지닙니다. 이 특정 알고리즘을 **Hash 알고리즘(이하 해시 알고리즘)**이라고 부르며 해시 알고리즘이 적용된 암호화된 문자열를 **Hash 값(이하 해시값)**라고 부릅니다. 해시 알고리즘의 종류에는 MD5, SHA-128, SHA-256 등이 있습니다. 아래 그림은 SHA-256 해시 알고리즘을 평문에 적용했을 때의 값을 나타낸 것입니다. 같은 단어를 사용하였음에도 순서가 다르자 완전 다른 해시값이 나타나는 것을 알 수 있지요.

![](https://blog.kakaocdn.net/dn/GJo80/btrfa3irNWE/ucKc3oqLdGMa0Xs3yErDIK/img.png)

<평문에 'SHA-256' 해시 알고리즘을 적용하여 생성한 해시값>

송신자는 원본 데이터(암호화 알고리즘에 의해 암호화된)와 해시 알고리즘이 적용된 데이터를 함께 전송하고 수신자는 약속한 해시 알고리즘을 이용해 원본 데이터에 적용해봅니다. 해시 알고리즘이 적용된 원본 데이터가 상대방이 보낸 '해시 알고리즘이 적용된 데이터'와 결과가 같다면 이것은 변조되지 않은 메시지임을 알게 됩니다. 해시 알고리즘의 종류에는 MD5, SHA-128, SHA-256 등이 있습니다.

![](https://blog.kakaocdn.net/dn/PWzVy/btre6mpeCMA/76MnQEDdGkGNM1NdbvBwIk/img.png)

<해시 알고리즘의 종류>

해시 알고리즘은 '**일방향**'이라는 특성을 갖기 때문에 해시 알고리즘이 적용된 결과값에서 원본 메시지로 되돌릴 수 없다는 특성을 지닙니다. 다시 말해 해시값을 원본 데이터로 되돌릴 수 있다면 취약한 알고리즘임을 뜻하는 것이죠.

여기서 암호화 알고리즘과 해시 알고리즘의 차이가 드러나는데요. 암호화 알고리즘은 원본 데이터를 암호화할 수 있고 암호화된 데이터를 복호화하여 원본 데이터로 되돌릴 수 있지만 해시 알고리즘은 일방향 특성을 갖기 때문에 해시가 적용된 데이터를 원본 데이터로 되돌릴 수 없습니다.

여기까지가 Cipher Suite에 대한 이야기입니다. 다음 문서에서는 SSL 인증서를 사용자에게 발급하는 존재인 Certificate Authority(CA), 인증기관에 대해 알아보도록 하겠습니다.

​

​

​

**HTTPS 통신과정 쉽게 이해하기** [**#5(CA, 인증기관)**](https://aws-hyoh.tistory.com/59)

​

지난 #1, #2, #3, #4 문서에서 서버가 보유한 SSL 인증서를 인증기관(CA)이 발급한다고 설명했습니다. 그렇다면 도대체 이 인증기관(CA)은 무엇일까요? 그리고 내가 사용하는 브라우저는 접속하는 웹사이트마다 인증서를 검증받으러 인증기관에 찾아가는 것일까요? 만약 그렇다면 인증기관은 이 수많은 요청들을 혼자 해결하는 것일까요? 그에 대해 알아보고자 합니다.

**CA(Certificate Authority, 인증기관)**

이전 문서에서 브라우저가 접속하고자 하는 서버의 SSL 인증서를 받으면 이것이 진짜인지 가짜인지 검증하기 위해 인증기관(CA)에 요청한다고 말씀드린 것을 기억하시나요? 이 인증기관은 단 하나만 존재하는 것일까요?

![](https://blog.kakaocdn.net/dn/cGUHG5/btqIruFu9t5/wfy2oaKpdrYSl2cmaMPhlk/img.gif)

<인증기관의 역할(출처 : www.n3ncloud.co.kr)>

하나의 인증기관만이 존재하여 모든 요청을 처리하게 된다고 생각해봅시다. 그럴 일은 없겠지만 인증기관이 여러분이 만든 인증서를 암호화할 때 사용한 인증기관의 개인키를 탈취당한다면 어떻게 될까요? 인증기관이 하나뿐이므로 모든 SSL 인증서가 위험에 노출됩니다. 그러나 누구나 신뢰할 수 있는 최상위 인증기관 아래 다수의 중간 인증기관을 두고 그 인증기관들이 SSL 인증서 생성을 요청한다면? 중간 인증기관 하나에 문제가 생기더라도 다른 중간 인증기관을 사용하는 SSL 인증서들은 문제가 없을 것입니다. 그래서일까요? **CA는 사실 3계층 혹은 2계층 구조의 형태를 띄고 있습니다.**

**1. Root CA(최상위 기관)**

**2. Intermediate CA(중간 기관)**

계층 구조의 가장 위에는 모두가 신뢰하기로 합의한 최상위 인증기관인 Root CA가 존재합니다. 그리고 그 아래에는 중간 기관인 Intermediate CA가 존재하지요. 이런 계층 구조라면 서버가 요청하는 SSL 인증서 생성을 어떻게 처리하는 것일까요? 지난 문서에서 설명한 것을 다시 이야기해보겠습니다.

![](https://blog.kakaocdn.net/dn/bOylck/btqIrtTXck7/FkjcHRl1BYOshEJIg8qMg0/img.png)

<인증기관의 계층 구조(출처 : 위키백과)>

여러분이 SSL 인증서를 생성하기 위해 서버의 각종 정보와 공개키를 Intermediate CA에 제출합니다. 그러면 Intermediate CA는 서버가 제출한 각종 정보를 Intermediate CA의 개인키로 암호화(서명)합니다. 그런데 모두가 신뢰하기로 합의한 Root CA가 아닌 Intermediate CA가 서명한 인증서를 어떻게 신뢰할 수 있겠습니까? 그래서 Intermediate CA도 중간기관에 불과하기 때문에 상위 기관의 검증을 받게 됩니다. Intermediate CA는 자신의 공개키를 상위 기관(상위 Intermeidate CA)에 제출하면 상위 기관이 인증서를 생성하고 암호화(서명)합니다. 그리고 그 위의 상위 기관 또한 자신의 공개키를 Root CA에 제출하고 Root CA가 인증서를 생성하고 암호화(서명)합니다. 마지막으로 Root CA는 스스로 인증서를 생성하고 자신이 암호화(서명)합니다. 그 인증서들의 나열을 직접 확인해보면 다음과 같습니다.

![](https://blog.kakaocdn.net/dn/smuqk/btqDUEehsrS/fS5UFCGrTMKTqViPahONQ1/img.png)

<네트워크 엔지니어 환영의 AWS 기술블로그 인증서>

짜잔~ 제 블로그의 SSL 인증서(*.tistory.com)의 상위 인증서들(G1,G2)과 Root CA(VeriSign)이 나타나있습니다. 위에서 언급한 것처럼 제 블로그는 Intermediate CA에게서 검증을 받고, Intermediate CA들은 Root CA에게 검증받으며, Root CA는 스스로를 검증합니다. 이러한 인증서의 나열을 나타낸 것이 **Certificate Chain(인증서 체인)**입니다.

인증서 체인은 왜 필요한 것일까요? 만약 Client가 Server의 인증서를 가져오는 과정에서 해커가 개입하여 인증서를 가로채고 자신의 인증서를 보냈다고 가정해봅시다. 해커가 보낸 인증서, 공개키, Root CA 등은 모두 해커가 만든 것이기 때문에 인증서를 확인하는 과정에 전혀 문제가 없게 됩니다. 해커가 보낸 공개키로 인증서가 복호화될테고 또 인증서 서명(지문) 또한 변조되지 않았을테니 정상이니 말입니다.(전자서명, 지문에 대해서는 깊게 설명하지 않겠습니다.) 사용자는 해당 진짜 인증서라고 착각할 수 있게 만듭니다. 그래서 이 인증서가 해커가 아닌 올바른 Intermediate CA, Root CA를 가리키고 있다고 증명하는 **Certificate Chain**이 필요한 것입니다. 해커가 만든 Root CA, Intermediate CA가 아니라 신뢰할 수 있는 인증기관을 말입니다.

![](https://blog.kakaocdn.net/dn/cWuRoP/btqD1p9nXr7/9KKz2W19AGAZqOuw33K381/img.png)

<인증서 서명과 검증 과정>

**Browser(브라우저)**

**단도직입적으로 말하면 브라우저는 인증서를 발급한 주요 인증기관의 리스트와 공개키를 이미 보유하고 있습니다.** 그러므로 서버의 인증서를 요청하여 검증할 때마다 직접 인증기관으로 가는 것이 아닌 해당 인증서가 자신이 보유한 인증기관의 리스트 중 하나일 경우 이미 갖고 있는 공개키를 이용하여 인증서를 복호화합니다. 그렇다면 자신이 가지고 있지 않은 인증기관의 공개키라면 어떻게 할까요? 이럴 경우 외부 인터넷으로 나아가 인증기관 관련 정보와 공개키를 확보합니다. 그래서 SSL 인증서를 사용할 때 최소한의 인터넷 접속이 필요한 이유가 이런 이유 때문입니다.

네트워크를 운영하시는 분이라면 가끔 인프라망의 트래픽 조절을 위해 일부 대역을 차단했는데, 사용자로부터 몇몇 사이트 접속시 신뢰할 수 없는 사이트라 뜬다는 문의 전화를 받아본 적이 있으실 겁니다. 모든 경우는 아니지만 브라우저가 갖고 있는 않은 CA 리스트, 공개키를 확보하기 위해 가는 목적지의 IP가 차단되어 발생하는 경우가 종종 있으니 참고하시면 좋을 것 같다는 생각이 드네요.

**[출처]** [HTTPS 통신과정 쉽게 이해하기](https://blog.naver.com/hanajava/223126802656)|**작성자** [하나자바](https://blog.naver.com/hanajava)