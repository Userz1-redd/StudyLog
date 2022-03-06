# 컴포넌트 객체 생성

컴포넌트를 객체화하는 방법에는 생성된 빌더나 팩토리를 사용할 수 있습니다.

컴포넌트 내의 @Component.Builder 또는 @Component.Factory 타입 선언을 통해 빌더 또는 팩토리를 생성할 수 있습니다.

만약 빌더나 팩토리 어노테이션이 존재하지 않으면 Dagger는 @Component 어노테이션에 선언된 모듈 및 의존성을 참조하여 빌더를 자동으로 생성합니다.

만약 기존 HelloWorld 예제에서 직접 빌더 구조를 작성한다면 아래와 같이 작성할 수 있습니다.

```
@Component(modules = [MyModule::class])
interface MyComponent {
    ...
    
    @Component.Builder
    interface Builder{
        fun setMyModule(myModule: MyModule) : Builder
        fun build() : MyComponent
    }
}
```
### 컴포넌트 빌더를 선언할 때는

1. 매개변수가 없는 컴포넌트 타입 또는 컴포넌트의 부모타입을 리턴하는 추상 메서드를 하나 포함해야 하고 이를 빌드메소드라고 합니다.
2. 빌드메소드 이외의 메소드를 세터메소드라고 합니다.
3. Component 어노테이션에 modules, dependencies로 선언된 요소들은 세터메소드로 선언해야 합니다.
4. 세터메소드는 하나의 매개 변수만 가져야 하며 반환형으로는 Unit, 빌더 또는 빌더의 슈퍼타입이 가능합니다.
5. 세터메소드에 @BindsInstance를 붙이면 해당 컴포넌트에 인스턴스를 넘겨 바인드시킵니다.


### 컴포넌트 팩토리를 선언할 때는

1. 컴포넌트 타입 또는 컴포넌트의 슈퍼타입을 반환하는 하나의 추상 메소드만 존재해야 합니다.
2. 팩토리 메소드에는 Component어노테이션에 지정한 modules, dependencies로 지정된 속성들을 반드시 매개변수로 가져야 합니다.
3. @BindsInstance 어노테이션이 붙은 매개변수를 해당 컴포넌트에 인스턴스를 넘겨 바인드시킵니다.

생성되는 컴포넌트 타입에는 factory()라는 정적메소드를 갖게 되며 이는 팩토리 인스턴스를 반환합니다. 이 팩토리 인스턴스로 컴포넌트를 초기화 할 수 있습니다.

# Lazy, Provider주입

### Lazy
객체가 초기화되는데 시간이 필요하다면 바인드된 타입을 제네릭으로 갖는 Lazy<T>타입으로 선언할 수 있습니다.
Lazy<T>의 get()메소드를 호출하기까지 객체가 초기화되는 것을 늦출 수 있습니다.

### Provider

매번 새로운 인스턴스를 주입받고 싶다면 Provider<T>를 사용할 수 있습니다. Provider<T>는 get()메소드를 호출할 때 마다 새로운 객체를 제공받게됩니다.

## 한정자 지정하기

### @Named

반환형만으로 바인드된 객체를 식별하기 어려울때, 또는 같은 타입의 객체를 반환하는 @Provides 메소드가 두개 이상일때 이를 구분하는데 @Named() 어노테이션을 사용할 수 있습니다. 아래는 간단한 예시입니다.

```
@Module
class MyModule {
    @Provides
    fun provideHello() : String = "HelloWorld"
    
    @Provides
    fun provideBye() : String = "Bye"
}
```
현재 모듈에서 반환하는 String 타입이 두개 이므로 에러가 발생할 것입니다. 이를 @Named를 이용하면 구분할 수 있습니다.

```
@Module
class MyModule {
    @Provides
    @Named("Hello")
    fun provideHello() : String = "HelloWorld"
    
    @Provides
    @Named("Bye")
    fun provideBye() : String = "Bye"
}

class MyClass {
    @Inject
    @Named("Hello")
    lateinit var str : String
} 

```

### 사용자 정의 한정자

@Named가 아닌 고유한정자를 사용하고자 한다면 @Qualifier를 사용하여 직접 한정자를 만들 수 있습니다.

```
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@interface Hello{
}
```
이렇게 하면 기존의 @Named("Hello")대신 @Hello를 사용할 수 있습니다.



# Scope

@Scope 어노테이션은 범위를 지정하도록 도와줍니다. 컴포넌트의 구현과 함께 각 컴포넌트 인스턴스는 의존성의 제공 방법에 대한 동일성을 보장받을 수 있습니다.

즉 하나의 인스턴스만 만들어서 참조하는 싱글턴 패턴과 비슷한 개념이지만,

애플리케이션의 생명 주기와 달리 생명주기를 따로 관리할 수 있습니다.

예를들어 애플리케이션, 프래그먼트, 액티비티 등의 인스턴스에 대한 범위 지정을 다르게 하여 오브젝트 그래프의 생성과 소멸을 각자 관리할 수 있습니다.

컴포넌트와 @Provides에 @Singletond을 추가하는 간단한 예제입니다.

```
@Module
class MyModule {
    @Singleton
    @Provides
    fun provideObject() : Object = Object()
}

//컴포넌트

@Component(modules = [MyModule::class])
@Singleton
interface MyComponent {
    fun getObject() : Object
}
```
## Reusable

@Reusable은 싱글턴과 비슷하지만 이전 객체를 재사용가능하다면 재사용하고, 아니면 새로 생성합니다.

항상 동일한 객체를 사용해야 하는 상황이 아니라면 메모리 관리 측면에서 좀 더 효율적입니다.

## Scope 확장하기

아래처럼 커스텀 스코프를 정의하는 것 또한 가능합니다.

```
@Scope
@Retention(RetentionPolicy.RUNTIME)
@interface UserScope{}
 ```
 
 
# 서브컴포넌트

서브컴포넌트는 두개의 컴포넌트를 연관짓는 가장 쉬운 방법입니다.

서브컴포넌트는 컴포넌트와 거의 비슷한 방식으로 동작하지만 상위 컴포넌트 내에 구현됩니다.

서브컴포넌트의 상위 컴포넌트는 또다른 서브컴포넌트가 될 수도있는것처럼 계층 관계를 구성할 수 있습니다.

<img width="508" alt="image" src="https://open.oss.navercorp.com/storage/user/900/files/f3ffb900-6f1d-11ec-9612-884be0704083">

그림에서 서브컴포넌트 B는 서브컴포넌트 A, 컴포넌트가 가진 모듈로부터 의존성을 제공받을 수 있습니다. 

하지만 서브컴포넌트 A는 서브컴포넌트 B의 의존성을 제공받을 수 없습니다.

서브컴포넌트의 선언은 @Component대신 @SubComponent를 선언하는 것으로 정의할 수 있습니다. 

마찬가지로 modules에 바인딩하려는 모듈 클래스들을 추가하며 서브컴포넌트는 컴포넌트와 달리 빌더 또는 팩토리를 반드시 정의해야 합니다.

서브컴포넌트 쪽은 이론으로 확실한 이해가 가지않아 실제 적용 코드를 보며 학습하려고 합니다.
