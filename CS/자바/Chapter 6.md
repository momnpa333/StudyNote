- final class (243p)
- [?] **final 클래스**

class명 앞에 final이 붙은 class로서 상속이 불가능하다. 그렇다면 왜 final class를 사용하는 것일까?

이는 보안상의 이유때문인데, Java에서는 이론상으로는 중요한 class의 sub class를 만들어 sub class로 하여금 시스템을 파괴하도록 할 수 있기 때문에 Java 시스템은 중요한 class에 대해서는 final class로 선언하고 있다. 대표적인 것이 String class이다.

final class로 선언되면 상속받을 수 없기 때문에 당연히 내부의 모든 method는 overridding(재정의)될 수 없다.

- setter 존재 이유(245p)
- 