## 1. 학습 키워드

### 1. 인텔리제이 없이 자바 실행하기

## 2. 활용 사례

1. [Main.java](http://Main.java) 및 MyMath.java작성

```java
public class Main{
    public static void main(String[] args){
        System.out.println("Hello world");
        MyMath math = new MyMath();
        Long tmp1=math.add(1L,2L);
        // Long tmp2=math.add(1L,3L,5L,9L,7L);
        System.out.println(tmp1);
        math.printArgs(1L,2L,3L,4L,5L);
    }
}
```

```java
import java.util.List;

/**
     * Prints a String and then terminate the line.  This method behaves as
     * though it invokes {@link #print(String)} and then
     * {@link #println()}.
     *
     * @param x  The {@code String} to be printed.
     */

public class MyMath {
    long test(Long a,Long b){
        return add(a,b)+sub(a,b);
    }
    long add(Long a,Long b){
        return a+b;
    }
    long sub(Long a,Long b){
        test2(a,b);
        return a-b;
    }
    static long test2(Long a,Long b){   
        return a+b;
    }
    static void printArgs(Long... arg){
        for(Long i:arg){
            System.out.println(i);
        }
    }
}
```

1. javac [Main.java](http://Main.java) 를 사용하여 Main.class 및 [MyMath.](http://MyMath.java)class 생성
    
  ![[Pasted image 20240411221750.png]]
2. java Main을 통해 Main.class 실행
    

### 기존의 경우 javac를 통해 class을 생성하는데 과연 intellij의 경우 class파일을 어디에 저장하고 있을까?

1. intellij를 사용하여 빌드하였을시 나타나는 파일 구조

![[Pasted image 20240411221809.png]]

1. out폴더안에 test/test/.idea폴더가 생성됨을 확인

- out 폴더가 생긴 이유
    
    ![[Pasted image 20240411221837.png]]
    
    위 사진은 Project structure>Project Settings > Project > Compiler output 에서 확인가능하다.
    
    IntelliJ에서 컴파일러가 build할때 out폴더를 생성하여 class파일을 모아둔다.
    
    따라서 out폴더가 생김
    
    ![[Pasted image 20240411221851.png]]
    
    out 폴더안의 Main 파일을 열어보면 위에 .class파일을 decompiled한 모습을 보여주었다고 표시된것을 볼수 있고, bytecode 버전을 보여준다.
    

## intellij가 없이 java파일을 실행시키는 법을 알아야할까?

1. 배포할 때에는 ide가 없는 환경이 대부분이다.
    - 대부분의 컨테이너 환경이나 VM환경에서 ide가 없기 때문에 서버를 빌드하고 실행시키는 과정을 알아야할 필요가 있다.
    - 도커로 이미지화 하거나 github-actions안에 러너를 이용하게 될 경우에도 반드시 알아야 한다.
2. intellij를 이해하기 위해
    - 만약 자바를 실행시키는 법을 모른다면 결국 intellij라는 ide에 종속될 수 밖에 없다. intellij에서 기본 빌드 환경으로 out이라는 폴더 안에 class파일을 모아두는데 이를 모르고 실행하게 될경우 class파일을 다른곳에 모아두고 싶을때 혹은 빌드 할때 다른 설정으로 하고싶을때 이 개념이 없을경우 다른 곳에서 헤맬 가능성이 높다.
    - ide는 결국 도움을 주기위한 도구일 뿐. 반드시 종속적이게 되면 안된다. intellij는 환경변수나, 빌드할때 도구를 자동으로 세팅해 주기 때문에 기초적인 부분을 까먹거나 등한시 할수 있는데, 위 부분을 잘 이해해야 한다. intellij에서 세팅한 것과 다르게 세팅해야하는 경우가 반드시 오기 때문!

## 3. Challenging (학습하며 어려웠던 점)

- 어려웠던 점 보다는 좋았던 점을 적자면 강의에서 인텔리제이 없이 자바 실행법을 알려주셨는데, 인텔리제이에 대해 다시한번 생각해 보게되어 좋았던 것 같다.

## 4. Resource Links

[[Intellij] Gradle project - out build 폴더](https://sehyeona.tistory.com/32)

## Feedback Form

(코치의 피드백이 필요한 내용이 있다면 남겨주세요!)