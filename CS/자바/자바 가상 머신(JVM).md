- 자바 프로그램이 실행되는 [[가상 컴퓨터(VM)]]<-가상 컴퓨터
- JVM은 그냥 프로그램 
- Java는 JVM덕분에 OS에 종속적이지 않음
- 

## 자바는 컴파일 언어다

- [ ] 
----
- [ ] 

## 자바 가상 머신(JVM)의 동작 방식
**1.** 자바로 개발된 프로그램을 실행하면 JVM은 OS로부터 메모리를 할당

**2.** 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일

**3.** Class Loader를 통해 JVM Runtime Data Area로 로딩

**4.** Runtime Data Area에 로딩 된 .class들은 Execution Engine을 통해 해석

**5.** 해석된 바이트 코드는 Runtime Data Area의 각 영역에 배치되어 수행하며 이 과정에서 Execution Engine에 의해 GC의 작동과 스레드 동기화가 이루어짐
![[Pasted image 20231231130709.png]]



### 네이티브 메서드 스택 (Native Method Stack)

**1.** 자바 이외의 언어(C, C++, 어셈블리 등)로 작성된 네이티브 코드를 실행할 때 사용되는 메모리 영역으로 일반적인 C 스택을 사용

**2.** 보통 C/C++ 등의 코드를 수행하기 위한 스택을 말하며 (JNI) 자바 컴파일러에 의해 변환된 자바 바이트 코드를 읽고 해석하는 역할을 하는 것이 자바 인터프리터(interpreter)