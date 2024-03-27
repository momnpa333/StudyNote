- HTTP 1.1과 HTTP 2.0의 주요한 차이점은 HTTP 메세지가 1.1에서는 text로 전송되었던 것과 달리, 2.0에서는 binary frame로 인코딩되어 전송된다는 점이다.
- 또한 HTTP 헤더에 대해서 배웠을때 헤더와 바디를 ~~\r~~ 이나 ~~\n~~ 과 같은 개행 문자로 구분한다고 하였는데, HTTP/2.0에서 부터는 헤더와 바디가 **layer**로 구분된다.

이로인해 데이터 파싱 및 전송 속도가 증가하였고 오류 발생 가능성이 줄어들었다.

출처: [https://inpa.tistory.com/entry/WEB-🌐-HTTP-20-통신-기술-이제는-확실히-이해하자](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-HTTP-20-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0-%EC%9D%B4%EC%A0%9C%EB%8A%94-%ED%99%95%EC%8B%A4%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EC%9E%90) [Inpa Dev 👨‍💻:티스토리]
