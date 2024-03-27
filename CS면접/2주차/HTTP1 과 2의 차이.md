- HTTP 1.1과 HTTP 2.0의 주요한 차이점은 HTTP 메세지가 1.1에서는 text로 전송되었던 것과 달리, 2.0에서는 binary frame로 인코딩되어 전송된다는 점이다.
- 또한 HTTP 헤더에 대해서 배웠을때 헤더와 바디를 \r 이나 \n 과 같은 개행 문자로 구분한다고 하였는데, HTTP/2.0에서 부터는 헤더와 바디가 **layer**로 구분된다. 이로인해 데이터 파싱 및 전송 속도가 증가하였고 오류 발생 가능성이 줄어들었다.
### **Multiplexing**

[![Multiplexing](https://blog.kakaocdn.net/dn/dIbRiK/btrRpXx8YKa/PZJEeEpq9joyI6pOkiNGz1/img.png)](https://blog.kakaocdn.net/dn/dIbRiK/btrRpXx8YKa/PZJEeEpq9joyI6pOkiNGz1/img.png)

바로 위에서 frame - message - stream - connection 그림에서 봤듯이, HTTP 헤더 메세지를 바이너리 형태의 프레임으로 나누고 하나의 커넥션으로 동시에 여러개의 메세지 스트림을 응답 순서에 상관없이 주고 받는 것을 **멀티플렉싱(multiplexing)**이라고 한다.

- HTTP/1.1의 Connection Keep-Alive, Pipelining, Head Of Line Blocking을 개선했다.
- latency만 줄여주는게 아니라 결국 네트워크를 효율적으로 사용할 수 있게 하고 그 결과 네트워크 비용을 줄여준다.
- 특히 클라우드 시스템을 이용한다면 비용과 직결된다.

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]

#### **HTTP 1.1 통신 과정**

**HTTP 1.1**에서는 한 TCP 커넥션을 통해 요청을 보냈을 때, 그에 대한 응답이 도착하고 나서야 같은 TCP 커넥션으로 다시 요청을 보낼 수 있다. 따라서 웹브라우저들은 회전 지연을 줄이기 위해 여러 개의 TCP 커넥션을 만들어 동시에 여러 개의 요청을 보내는 방법을 사용하였다. 그러나 그렇다고 TCP 커넥션을 무한정 만들 수는 없기에, 한 페이지에 보내야 할 요청이 수십개에서 수백개에 달하는 요즘 시대에는 한계가 있었다.

[![Multiplexing](https://blog.kakaocdn.net/dn/said3/btrRnOIdnoJ/v7PfJwYFICniIiOA6LdjFk/img.gif)](https://blog.kakaocdn.net/dn/said3/btrRnOIdnoJ/v7PfJwYFICniIiOA6LdjFk/img.gif)

1. Request 1을 전송 받기 위해 하나의 TCP Connection 1 을 열고 요청/응답한다.
2. 다음으로 Request 2, 3, 4을 요청하는데 빠르게 전송받기 위해 여러개의 커넥션 TCP Connection 2 와 TCP Connection 3을 만들어 요청/응답한다.
3. 하지만 커넥션을 무한정으로 만들수없어 이러한 방식은 한계가 존재한다.

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]

#### **HTTP 2.0 통신 과정**

반면, **HTTP 2**에서는 하나의 커넥션에 여러 개의 스트림이 동시에 요청/응답 한다.

HTTP 1.1은 요청과 응답이 메시지라는 단위로 구분되어 있었지만, HTTP 2부터는 Stream을 통해 요청과 응답이 묶일 수 있어 다수 개의 요청을 병렬적으로 처리가 가능해졌다. 따라서 응답 프레임들은 요청 순서에 상관없이 먼저 완료된 순서대로 클라이언트에 전달이 가능하다.

[![Multiplexing](https://blog.kakaocdn.net/dn/b7nEZv/btrRo3kn075/YfKfNG45pJl7k9DwXlBrKK/img.gif)](https://blog.kakaocdn.net/dn/b7nEZv/btrRo3kn075/YfKfNG45pJl7k9DwXlBrKK/img.gif)

1. Request 1을 전송 받기 위해, 우선 Framing Layer을 통해 바이너리 프레임 단위로 쪼개고 하나의 TCP Connection을 만들고 통신한다.
2. 다음으로 Request 2, 3, 4을 요청하는데 기존의 커넥션을 이용하며, 쪼개진 프레임들은 메세지 통로를 통해 동시다발적으로 요청/응답 받는다.
3. 커넥션 낭비도 없고 병렬적으로 자원이 전송받기에 매우 빠르다.

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]


### **Stream Prioritization**

HTTP 1.1에서 파이프라이닝 이라는 혁신적인 기술이 있었지만, 우선 순위 문제 때문에 HOLB(Head Of Line Blocking)가 발생하여 사장되었다고 [HTTP 1.1 글Visit Website](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-09-HTTP-30-%EA%B9%8C%EC%A7%80-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0)에서 소개했었다.

HTTP 2에서는 **리소스간 의존관계(우선순위)를 설정**하여 이런 문제를 해결하였다.

위에서 봤던 것 처럼 HTTP 메세지가 개별 바이너리 프레임으로 분할되고, 여러 프레임을 멀티플렉싱 할 수 있게 되면서 요청과 응답이 동시에 이루어져 비약적인 속도 향상이 되었다.

하지만 하나의 연결에 여러 요청과 응답이 뒤섞여 버려 패킷 순서가 엉망 징창이 되었다. 따라서 스트림들의 우선순위를 지정할 필요가 생겼는데, 클라이언트는 **우선순위 지정 트리**를 사용하여 스트림에 식별자를 설정함으로써 해결 하였다.

- 각각의 스트림은 1-256 까지의 가중치를 갖음
- 하나의 스트림은 다른 스트림에게 명확한 의존성을 갖음

[![Stream Prioritization](https://blog.kakaocdn.net/dn/bovfzI/btrRoqmKpkC/0AuGPTjUW6ZKM2ruwnojPk/img.png)](https://blog.kakaocdn.net/dn/bovfzI/btrRoqmKpkC/0AuGPTjUW6ZKM2ruwnojPk/img.png)

우선순위 지정 트리

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]

#### **스트림 우선순위 통신 과정**

[![Stream Prioritization](https://blog.kakaocdn.net/dn/wtkcr/btrSmYRnoQc/mSbVkISwaBuw9ZKlKB2vm0/img.png)](https://blog.kakaocdn.net/dn/wtkcr/btrSmYRnoQc/mSbVkISwaBuw9ZKlKB2vm0/img.png)

1. 클라이언트는 서버에게 스트림을 보낼때, 각 요청 자원에 가중치 우선순위를 지정하고 보낸다.
2. 그렇게 요청 받은 서버는 우선순위가 높은 응답이 클라이언트에 우선적으로 전달될 수 있도록 대역폭을 설정한다.
3. 응답 받은 각 프레임에는 이것이 어떤 스트림인지에 대한 고유한 식별자가 있어, 클라이언트는 여러개의 스트림을 interleaving을 통해 서로 끼워놓는 식으로 조립한다.

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]