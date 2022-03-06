# DI[Dependency Injection]
## DI란?
- 객체를 직접 생성하지 않고 외부에서 주입한 객체를 사용하는 방법입니다.
- 쉽게 의존성과 주입으로 나누어 생각하기 위해 간단한 예제를 만듭니다.
```
public class CPU{}

public class Computer{
  private CPU cpu;
  public Computer(){
    cpu = new CPU();
  }

}
```
위 코드에서 컴퓨터를 생성하기 위해선 CPU가 필요하며 CPU를 업그레이드 하고싶어도 변경할 수가 없습니다. 이를 Computer가 CPU에 의존성을 가진다라고 말합니다.

그렇다면 주입의 예제는 아래와 같습니다.
```
public class Computer{
  private CPU cpu;
  
  public void setCPU(CPU cpu){
    this.cpu = cpu;
  }
}
```
setCPU 메소드를 이용해 외부로부터 생성된 객체를 전달받아 멤버 변수가 넣습니다. 즉, 의존성과 주입을 함께 생각하면 
"의존관계에 있는 클래스의 객체를 외부로부터 생성하여 주입받는다" 라고 말할 수 있습니다.

## 의존성 주입은 왜 필요한가?

의존성 주입을 어떻게 활용하는지 생각하기 전, 의존성 주입이 왜 꼭 필요하지?라는 생각이 먼저 들었습니다. 위의 예시를 다시한번 살펴보겠습니다.

### 변경의 전이

위 예제에서 Computer는 CPU라는 한가지 타입에 의존합니다. 만약 사용자가 A사의 CPU사용을 원한다면,CPU클래스명을 A_CPU로 변경하고, 이것으로 끝나는 것이 아니라 Computer에 포함된 Computer클래스 까지 함께 변경해야합니다.

즉, 하나의 클래스를 변경함으로써 그와 의존관계에 있는 다른 클래스를 모두 변경해야하는 상황이 발생합니다.

이를 해결하기 위해 CPU를 인터페이스로 만든다면 어떤 클래스든 Computer의 CPU로서 동작할 수 있을 것입니다.

```
public interface CPU{...}

public class A_CPU implements CPU {...}

public class Computer{
  private CPU cpu;
  public Computer(){
    cpu = new A_CPU()
  }
}
```

이로써 변경의 전이는 최소화했지만, Computer클래스에서 객체를 생성하고, I회사의 CPU를 사용한다면 
cpu = new A_CPU() -> cpu = new I_CPU()로 변경해야 할 것입니다.

### 제어의 역전
제어의 역전은 어떠한 일을 수행하도록 만든 프레임워크에 제어권을 위임함으로써 관심사를 분리하는 것을 말합니다. 제어의 역전을 통해 앞의 코드의 문제점을 해결합니다.

```
public class Computer{
  
  private CPU cpu;
  
  public Computer(){}
  
  public Computer(CPU cpu){
    this.cpu = cpu;
  }
  
  public void setCPU(CPU cpu){
    this.cpu = cpu;
  }
}

public static void main(String[] args){
  CPU cpu = new I_CPU;
  Computer computer1 = new Computer(cpu);
  //또는
  Computer computer2 = new Computer();
  computer2.setCPU(cpu);
}
```

Computer 클래스의 생성자에서 CPU객체를 만들지 않고, 외부로부터 CPU 객체를 생성한 뒤 생성자 또는 Setter를 이용하여 CPU 객체를 제공합니다.
기존에는 Computer가 CPU를 생성하고 객체를 관리했으나 이 코드에서는 CPU객체의 생성 및 관리를 외부에 위임한 것입니다. 이것이 제어의 역전이며 이를 통해 결합도를 느슨하게 만들었고, Computer는 이제 CPU의 변경으로 인해 내부 코드를 변경하지 않아도 됩니다.

### 의존성 주입의 장단점
-	의존성 주입은 인터페이스를 기반으로 설계되며 코드를 유연하게 합니다.
-	주입하는 코드만 따로 변경하면 되므로 리팩토링이 수월합니다.
-	의존성 주입을 사용하는 결과로 stub이나 mock객체를 사용하여 단위테스트를 하기가 더욱 쉬워집니다. 의존성주입의 가장 큰 장점입니다.
-	클래스간 결합도를 느슨하게 합니다.
-	인터페이스를 기반으로 설계하므로 여러 개발자가 서로 사용하는 클래스를 독립적으로 개발할 수 있습니다. 
반면 아래와 같은 단점도 존재합니다.
-	간단한 프로그램을 만들때 사용하려 한다면 코드가 길어지고 번거로운 작업이 추가됩니다.
-	의존성 주입은 동작과 구성을 분리해 코드를 추적하기 어렵게 하고, 가독성을 떨어뜨립니다. 
-	Dagger2와 같은 의존성 주입 프레임워크는 컴파일 타임에 어노테이션 프로세서를 이용하여 파일을 생성하므로 빌드에 시간이 조금 더 소요됩니다.

따라서 짧은 기간 개발하고 더는 유지보수를 하지 않는 경우, 사용을 추천하지 않는다고 합니다. 

의존성 주입을 사용하는 경우 코드 추적이 힘들어지고, Dagger2사용시 빌드 시간이 조금 더 소요된다는 단점도 있습니다. 

하지만 적응하고 나면 코드 추적은 그렇게 어렵지 않고, Dagger2를 사용하여 다른 부분에서 크게 시간을 절약할 수 있습니다.


# Dagger2

dagger2는 자바와 안드로이드를 위한 강력하고 빠른 의존성 주입 프레임워크입니다. 리플렉션을 사용하지 않고 런타임에 바이트코드도 생성하지 않는 것이 특징입니다. 컴파일 타임에 어노테이션 프로세서에 의해 의존성 주입과 관련된 모든 코드를 분석하고 자바 코드를 자동으로 생성합니다.

Dagger2는 인턴십 입과 전, 이론적으로 공부하고 실제로 적용해보려고 했으나 어노테이션이 많고 Dagger에서만 사용하는 용어가 많아 어렵다고 느꼈습니다. 이번기회에 확실하게 짚고 원리와 동작방법에 대해 확실하게 정리하려고 합니다.

## Dagger의 장점
- 자원 공유의 단순화, 지정된 범위의 생명 주기 내에서 동일 인스턴스를 제공합니다.
- 복잡한 의존성을 단순하게 설정하고, 애플리케이션이 커질수록 많은 의존성을 갖는데 Dagger가 이를 해결해줍니다.
- 유닛테스트를 쉽게 도와줍니다.
- 자동으로 생성되는 코드는 명확하고 디버깅이 가능합니다.

간단한 실습을 통해 Dagger2를 사용해보겠습니다.
우선 모듈단위의 build.gradle에 의존성을 추가합니다.
```
 implementation 'com.google.dagger:dagger:2.40.5'
 kapt 'com.google.dagger:dagger-compiler:2.40.5'
 //최신버전
```
간단하게, Hello World문자열을 제공할 모듈을 만들어 보겠습니다. @Module로 모듈을 지정하고, 의존성을 제공하는 메소드에 @Provides를 사용합니다.

```
@Module
class Person() {
    @Provides
    fun provideHello() :String = "Hello World"
}
```
현재 Person은 의존성을 제공하는 클래스이고, provideHello()는 String 타입의 Hello World문자열을 제공하는 것을 뜻합니다. 
모듈만으는 별도의 클래스 파일이 생성되지 않습니다. 모듈을 참조하는 컴포넌트를 작성해야합니다.

```
@Component(modules = [Person::class])
interface PersonComponent {
    fun getHelloWorld() : String
}
```
@Component가 붙은 인터페이스에는 제공할 의존성들을 메소드로 정의해야하며 @Component에 참조된 모듈 클래스로부터 의존성을 제공받습니다.
컴포넌트 메소드의 반환형을 보고 모듈과 관계를 맺으므로 바인드된 모듈로부터 해당 반환타입을 갖는 메소드를 찾지 못한다면 컴파일타임에 에러가 발생합니다.

Dagger는 컴파일 타임에 @Component를 구현한 클래스를 생성하는데, 이때 Dagger라는 접두어가 붙습니다.

현재 PersonComponent를 만들었으므로 Dagger에 의해 생성된 클래스이름은 DaggerPersonComponent가 됩니다.
간단하게 JUnit테스 클래스로 의존성이 제대로 제공되는지 확인합니다.
컴포넌트 구현 후에는 프로젝트 리빌드가 필요합니다.

```
 @Test
    fun testDagger(){
        var personComponent = DaggerPersonComponent.create()
        println("result = ${personComponent.getHelloWorld()}")
    }
```


