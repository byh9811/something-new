# Java Parameter Passing

## 자바에서 파라미터는 어떤 방식으로 전달될까?  
우리는 메서드를 호출할 때 파라미터를 넘기는 경우가 많다. 이는 호출된 메서드에서 호출한 메서드의 값을 사용할 수 있도록 하기 위해서인데, 이 때 원리가 무엇일까?

## Pass by Value & Pass by Reference
Pass by Value 방식은 값을 복사하여 전달하는 방식이고, Pass by Reference 방식은 주솟값을 전달하는 방식이다.  
그리고 자바의 데이터 타입은 크게 원시 타입(Primitive Type)과 객체 타입(Object Type)이 존재하는데, 파라미터로 받는 변수가 둘 중 어떤 타입이냐에 따라 적용되는 방식이 살짝 달라진다.  
배열과 같은 큰 크기의 객체를 Pass by Value 방식으로 넘기면 메모리 낭비가 굉장히 심하기 때문에 원시 타입은 Pass by Value 방식으로, 객체 타입은 Pass by Reference 방식으로 넘기는 것이 국룰이다. 아래 예시를 보자.
```java
public class Ssafy {
    public static void main(String[] args) {
        Computer myCom = new Computer("i5-13600k", 5.2);
        System.out.println(myCom);      // ??
    }
}

class Computer {
    private String cpu;
    private double clock;
    
    public Computer(String cpu, double clock) {
        this.cpu = cpu;
        this.clock = clock;
    }
    
    // Getter & Setter & toString
}
```
```text
i5-13600k, 5.2
```
수업 시간에 배운 캡슐화를 적용시킨 간단한 코드이다. main 메서드에서 Computer 객체를 생성할 때 모든 멤버변수를 파라미터로 받는 생성자를 이용해 생성하고 있다. 이 때 cpu는 객체 타입의 파라미터이므로 Pass by Reference 방식으로, clock은 원시 타입의 파라미터이므로 Pass by Value 방식으로 각각 전달된다.

## Pass by Reference ?
나는 어느날 더 성능이 좋은 맥으로 업그레이드하고 싶어져서 아래와 같이 실행했다. 아래의 코드는 어떻게 실행될까?
```java
public class Ssafy {
    public static void main(String[] args) {
        Computer myCom = new Computer("i5-13600k", 5.2);
        change(myCom);
        System.out.println(myCom);      // ??
    }
    
    public static void change(Computer myCom) {
        myCom = new Computer("M1", 5.5);
        System.out.println(myCom);      // ??
    }
}

class Computer {
    private String cpu;
    private double clock;
    
    public Computer(String cpu, double clock) {
        this.cpu = cpu;
        this.clock = clock;
    }
    
    // Getter & Setter & toString
}
```
```text
M1, 5.5
i5-13600k, 5.2
```
change 메서드 안에서는 잘 변경된 모습이었으나 main에서는 바뀌지 않았다! Computer라는 객체 타입 데이터를 넘겼는데 주솟값이 전달되지 않는 걸까? 그렇다면 cpu를 바꾸지 않고 오버클럭킹을 통해 클럭이라도 바꿀 수는 없는걸까? 다음을 예측해보자.
```java
public class Ssafy {
    public static void main(String[] args) {
        Computer myCom = new Computer("i5-13600k", 5.2);
        overclocking(myCom);
        System.out.println(myCom);      // ??
    }

    public static void overclocking(Computer myCom) {
        myCom.setClock(5.5);
        System.out.println(myCom);      // ??
    }
}

class Computer {
    private String cpu;
    private double clock;
    
    public Computer(String cpu, double clock) {
        this.cpu = cpu;
        this.clock = clock;
    }
    
    // Getter & Setter & toString
}
```
```text
i5-13600k, 5.5
i5-13600k, 5.5
```
이번에는 overclocking 메서드를 벗어나 main에서도 바꾼 값이 유지되는 모습이다! 그렇다면 주솟값을 넘겨준다는 얘기가 아닌가? 너무 헷갈리기 시작한다.

## Pass by Value !
결론부터 말하자면 사실 자바는 모든 파라미터를 Pass by Value 방식으로 넘긴다. 그렇다면 바로 위의 예제에서 우리는 어떻게 클럭을 바꿀 수 있었던 걸까? 메모리의 상태를 생각하면서 알아보자.  
- 수업 시간에 객체는 메모리의 Heap 영역에, 참조 변수는 Stack 영역에 배치된다고 배운 사실을 복습하고 시작하자.
```java
public class Ssafy {
    public static void main(String[] args) {
        Computer myCom = new Computer("i5-13600k", 5.2);    // Computer 인스턴스가 Heap 메모리상에 올라가고, 이 위치를 가리키는 myCom 변수가 Stack 메모리상에 올라간다.
        overclocking(myCom);        // 메서드의 종료 시점을 Stack에 올리고, myCom을 넘기면서 메서드를 호출한다.
        System.out.println(myCom);      // 주소값을 통해 Computer 인스턴스의 값을 출력해보면 바뀐 값이 출력된다.
    }

    public static void overclocking(Computer myCom) {       // myCom이라는 새로운 변수에 main의 myCom의 값이 복사된다.
        myCom.setClock(5.5);        // 복사된 값을 통해 main에서 생성된 Computer 인스턴스에 접근할 수 있고, 해당 객체의 clock값을 바꾼다.
        System.out.println(myCom);      // 주소값을 통해 Computer 인스턴스의 값을 출력해보면 바뀐 값이 출력된다.
    }                           // 이 메서드에서 사용중이던 myCom을 메모리에서 내리고, 호출 지점으로 돌아간다.
}

class Computer {
    private String cpu;
    private double clock;
    
    public Computer(String cpu, double clock) {
        this.cpu = cpu;
        this.clock = clock;
    }
    
    // Getter & Setter & toString
}
```
```text
i5-13600k, 5.5
i5-13600k, 5.5
```
아~ 이 코드는 이해가 되는것 같은데 그럼 아까는 왜 안되었던 거지? 그 코드를 불러와서 다시 분석해보자.
```java
public class Ssafy {
    public static void main(String[] args) {
        Computer myCom = new Computer("i5-13600k", 5.2);    // Computer 인스턴스가 Heap 메모리상에 올라가고, 이 위치를 가리키는 myCom 변수가 Stack 메모리상에 올라간다.
        change(myCom);        // 메서드의 종료 시점을 Stack에 올리고, myCom을 넘기면서 메서드를 호출한다.
        System.out.println(myCom);      // main 메서드에서 myCom은 i5 Computer를 가리키고 있으므로 해당 컴퓨터가 출력된다.
    }
    
    public static void change(Computer myCom) {       // myCom이라는 새로운 변수에 main의 myCom의 값이 복사된다.
        myCom = new Computer("M1", 5.5);        // 새로운 Computer 인스턴스가 생성되어 Heap 메모리상에 올라가고, myCom이 이 인스턴스의 위치를 가리키도록 변경한다.
        System.out.println(myCom);      // 현재 myCom이 가리키는 M1 Computer가 출력된다.
    }                           // 이 메서드에서 사용중이던 myCom을 메모리에서 내리고, 호출 지점으로 돌아간다.
}

class Computer {
    private String cpu;
    private double clock;
    
    public Computer(String cpu, double clock) {
        this.cpu = cpu;
        this.clock = clock;
    }
    
    // Getter & Setter & toString
}
```
```text
M1, 5.5
i5-13600k, 5.2
```
이제 이해가 되는 것 같다! 이렇게 호출된 메서드에서 호출한 메서드의 참조 변수에 새로운 객체를 할당하는 것은 불가능하다는 것을 알게 되었다. 이 주제를 학습하다가 Immutable Object와 String의 Constant Pool을 추가로 공부해보면 좋을 것 같다는 생각이 들었다.

