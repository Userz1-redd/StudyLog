## Module

모듈은 컴포넌트에 의존성을 제공하는 역할을 합니다. 모듈은 프로바이더를 포함하고 Null을 허용하지 않으며 상속이 가능하다는 특징이 있습니다.


### Provider

프로바이더는 @Provides 어노테이션을 사용하는 것으로 컴파일 타임에 의존성을 제공하는 바인드된 프로바이더를 생성합니다. 
메소드의 반황형을 보고 컴포넌트에서 의존성이 관리되기 때문에 같은 타입이 하나의 컴포넌트 내에 존재해서는 안됩니다. 
예를들어 아래와같이 String을 제공하는 프로바이더가 중복되서는 안됩니다.
```
@Module
class PersonModule() {
    @Provides
    fun provideHello() :String = "Hello World"
    
    @Provides
    fun provideBye() : String = "Good Bye"
}
```
또한 컴포넌트 내 바인드된 메소드의 반환형은 프로바이더의 매개변수로 사용할 수 있습니다.

```
@Module
class PersonModule() {
    @Provides
    fun provideName() :String = "MyName"

    @Provides
    fun provideAge() : Int = 26
    
    @Provides
    fun providePerson(name : String, age : Int) : Person = Person(name,age)
    //name = MyName, age = 26 제공
}
```
providePersondms 매개변수로 String과 Int를 갖습니다. 위에서 바인드된 String타입과 Int타입으로부터 의존성을 제공받아 name은 MyName 이되고
age는 26이 될것입니다. 매개변수 타입에 맞는 의존성이 컴포넌트 또는 컴포넌트와 바인드된 모듈에 없다면, 에러가 발생합니다.

만약 모듈클래스가 추상클래스인경우, 프로바이더는 자바 기준 static 메소드를 사용해야 합니다.


### Null 비허용

프로바이더는 기본적으로 null을 반환하는 것을 제한합니다. 따라서 null을 반환하고 싶다면, @Nullable 어노테이션을 사용해야 합니다.
의존성을 주입받는 부분에도 @Nullable 어노테이션이 존재해야하며 한쪽이라도 없으면 컴파일에러가 발생합니다.
아래는 Nullable을 사용한 예시입니다.

```
@Module
class PersonModule() {
  @Provides
  @Nullable
  fun provideString() : String? = null
}
```
```
@Component(modules = [PersonModule::class])
interface PersonComponent {
    @Nullable
    fun getString() : String
}
```
프로비전 메소드 뿐만 아니라 멤버-인젝션 메소드를 써서 null을 주입하는 경우에도 멤버 변수에 @Nullable을 꼭 사용해야 합니다.

### 모듈의 상속

모듈이 가질수 있는 속성 중 includes라는 것을 사용하여 프로바이더를 상속할 수 있습니다. 
ModuleA와 ModuleB가 존재하고, B가 A를 상속하는 코드는 아래와 같습니다.

```
@Module
class ModuleA{
    @Provides
    fun provideA : A = A()
}

@Module(includes = [ModuleA::class])
class ModuleB{
    @Provides
    fun provideB : B = B()
}
```

만약 컴포넌트에서 모듈B를 참조하는 경우, A타입의 객체도 함께 바인딩 됩니다.

## Component

컴포넌트는 바인딩된 모듈로부터 오브젝트 그래프를 생성하는 핵심적인 역할을 합니다.
@Component 사용을 통해 컴포넌트를 생성할 수 있으며, @Component 어노테이션은 인터페이스나 추상클래스에만 붙일 수 있ㅅ브니다.
컴파일타임에 DaggerComponent형식으로 합쳐진 이름을 가진 클래스를 생성하게 됩니다.

@Component의 속성으로 modules와 dependencies가 있습니다. Modules에는 컴포넌트에 바인드 되는 @Module이 지정한 클래스 배열을 선언합니다.
모듈이 다른 모듈을 포함하는 경우, 컴포넌트에 선언된 모듈 뿐만 아니라 포함된 모듈도 컴포넌트에 구현될 수 있도록 해야합니다. Dependencies에는 컴포넌트에 다른 컴포넌트의 의존성을 사용하는 경우, 클래스 배열을 선언합니다.

###오브젝트 그래프

Dagger에서는 컴포넌트, 모듈, 객체등의 관계를 컴포넌트 또는 오브젝트 그래프라 합니다. Hello World를 주입하는 예제에서는 오브젝트 그래프를 아래와 같이 나타낼 수 있습니다.

<img width="407" alt="image" src="https://user-images.githubusercontent.com/58676668/148331652-bee03e71-0598-48ca-95f6-8d8c6c24e26e.png">

### 컴포넌트 메소드
@Component가 붙은 모든 타입은 최소 하나 이상의 추상메소드를 가져야 합니다. 메소드의 이름은 자유이지만, 매개변수와 리턴타입을 엄격하게 따라야 합니다.
정해진 규칙에 따라 프로비전 메소드와 멤버 인젝션 메소드로 구분합니다.

#### Provision Methods

Dagget 컴포넌트에서 매개 변수를 갖지 않으면서 반환형은 모듈로부터 제공되거나 주입되는 메소드를 프로비전 메소드라고 칭합니다. 아래는 예시입니다.

```
@Component(modules = [SomeModule::class])
Interface someComponent{
	fun getSomeType() : SomeType
}
```
getSomeType메소드를 호출하면 SomeModule로부터 제공받거나 주입받은 SomeType객체를 반환합니다.

#### Member-Injection Methods

Dagger의 컴포넌트에서 하나의 매개변수를 갖는 메소드를 멤버-인젝션 메소드라고 칭합니다.
멤버-인젝션 메소드는 반환타입이 void이거나 코틀린 기준으로 Unit이어야 하며 빌더 패턴처럼 메소드 체이닝이 가능한 메소드를 만들기 위해 매개 변수 타입을 반환형으로 갖는 메소드를 선언할 수 있습니다.

아래는 쉽게 기존 HelloWorld예제를 멤버-인젝션 메소드로 구현한 방법입니다.

먼저 의존성을 주입받을 객체인 MyClass를 생성합니다. 그리고 의존성 주입을 받을 필드에 @Inject 어노테이션을 붙입니다.
```
class MyClass {
    @Inject
    lateinit var str : String
}
```
컴포넌트에도 멤버-인젝션 메소드를 추가합니다.
```
@Component(modules = [MyModule::class])
interface MyComponent {
    fun inject(myClass: MyClass)
}
```
아래와 같이 테스트코드를 작성합니다.
```
@Test
fun testMemberInjection(){
    var myClass = MyClass()
    var myComponent = DaggerMyComponent.create()
    myComponent.inject(myClass)
    assertEquals("HelloWorld",myClass.str)
}
```
이 방법 이외에도 컴포넌트에 MembersInjector를 반환하는 메소드를 이용하는 방법도 존재합니다.
```
@Component(modules = [MyModule::class])
interface MyComponent {
    fun inject(myClass: MyClass)
    fun getInjector() : MembersInjector<MyClass>
}

//Test
@Test
    fun testMemberInjection(){
        var myClass = MyClass()
        var myComponent = DaggerMyComponent.create()
        var injector = myComponent.getInjector()
        injector.injectMembers(myClass)
        assertEquals("HelloWorld",myClass.str)
    }

```
	
## 의존성 주입방법

의존성 주입에는 크게 3가지 방법이 존재합니다.
1.	필드 주입
2.	생성자 주입
3.	메소드 주입

@Inject어노테이션이 붙은 필드, 메소드, 생성자에 인스턴스를 주입하는데, 대부분 필드주입과 생성자 주입이 사용됩니다.

간단한 예제로 필드주입, 생성자 주입을 구현하였습니다.
```
class PersonA @Inject constructor(var name : String, var age : Int) {

}
```

```
class PersonB {
    @Inject
    lateinit var name : String
}
```

### 상속된 클래스에 의존성 주입

멤버-인젝션 소드를 호출할 때 서브클래스의 객체를 넣으면 해당 슈퍼클래스의 @Inject 멤버만 의존성 주입이 이루어집니다. 아래 예시에서 inject(Self)라는 멤버 인젝션 메소드가 존재하고 매개변수로 Child를 준다면, a와 b에만 의존성 주입이 실행됩니다.

```
class Parent{
@Inject
lateinit var a : A
}

class Self : Parent{
@Inject
lateinit var b : B
}

class child : Self{
@Inject
lateinit var c : C
}
```




