# android.dagger.*

기존의 오브젝트 그래프를 설계하듯이 사용하는 방법은 리팩토링이 어렵고 보일러플레이트 코드가 많다는 단점이 있습니다.

이를 해결하기위해 Dagger에서 안드로이드를 위한 dagger.android패키지를 제공합니다.

액티비티에 의존성을 주입한다고 가정하고 코드를 작성하면

가장 먼저 AppComponent의 모듈에 AndroidInjectionModule을 추가합니다. 안드로이드 인젝션 모듈은 안드로이드 프레임워크 관련 클래스에 의존성 주입을 위임할 

AndroidInjector<?> 의 팩토리를 멀티 바인딩으로 관리합니다.

```
@Component(modules = [AndroidInjectionModule::class,AppModule::class])
@Singleton
interface AppComponent : AndroidInjector<App> {

    @Component.Factory
    interface Factory : AndroidInjector.Factory<App>{
    }
}
```

AndroidInjector는 멤버 인젝션을 위한 inject 메서드가 포함되어 있고

AndroidInjector.Factory는 App인스턴스를 그래프에 바인딩하고 컴포넌트를 반환하는 create()메소드가 이미 포함되어 있으므로 별도로 정의할 필요가 없습니다.

AppModule에서는 MainActivity의 인스턴스에 멤버 인젝션을 담당할 MainActivity Subcomponent를 서브 컴포넌트로 연결합니다.

```
abstract class AppModule {
    @Named("App")
    @Provides
    @Singleton
    fun provideString() = "String from AppModule"

    @Binds
    @IntoMap
    @ClassKey(MainActivity::class)
    abstract fun bindAndroidInjectorFactory(factory : MainActivityComponent.Factory) : AndroidInjector.Factory<?>
}
```
## @ContributesAndroidInjector

서브 컴포넌트의 팩토리가 다른 메소드나 클래스를 상속하지 않는다면 서브컴포넌트를 정의하는 코드를 @ContributesAndroidInjector로 대체할 수 있습니다.

이외에도 Dagger에서 제공하는 DaggerBase클래스가 존재합니다.

- DaggerAppCompatActivity

- DaggerActivity

- DaggerFragment

- DaggerService
...등등

