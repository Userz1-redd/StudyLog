# 안드로이드를 위한 접근방식

안드로이드에 Dagger를 적용하려면 안드로이드의 특성에 대해 먼저 이해해야 합니다.

- 안드로이드는 하나의 애플리케이션 내에 액티비티 또는 서비스 같은 생명주기를 갖는 컴포넌트로 구성된다.

- 프래그먼트는 단독으로 존재할 수 없으며 액티비티 내에 존재한다.

- 애플리케이션을 포함한 액티비티 또는 서비스와 같은 컴포넌트는 시스템에 의해서만 인스턴스화 된다.

위 세가지 특성을 중심으로 안드로이드는 아래와 같이 구성할 수 있습니다.

<img width="645" alt="image" src="https://open.oss.navercorp.com/storage/user/900/files/ff92c480-6f9d-11ec-95a2-e65c348fe170">

애플리케이션의 생명 주기 동안 다양한 액티비티 및 서비스가 생성과 소멸을 반복하고

액티비티 내에서도 여러 프래그먼트가 생성과 소멸을 반복합니다.

가장 큰 범위인 애플리케이션에서 일어나는 일들이므로 애플리케이션의 생명주기와 Dagger컴포넌트의 생명주기를 같이하는 애플리케이션 컴포넌트를만듭니다.

액티비티 또는 서비스를 위한 Dagger 컴포넌트는 애플리케이션 컴포넌트의 서브 컴포넌트로 구성하고 프래그먼트는 액티비티 컴포넌트의 서브 컴포넌트로 구성합니다.

애플리케이션 컴포넌트 생성 예시입니다.

```
@Component(modules = [AppModule::class])
@Singleton
interface AppComponent {
    fun mainActivityComponentBuilder() : MainActivityComponent.Builder
    fun inject(app : App)
    
    @Component.Builder
    interface Builder{
        @BindsInstance
        fun app(app : App) : Builder
        fun appModule(appModule : AppModule) : Builder
        fun build() : AppComponent
        
    }
}
```

```
@Module(subcomponents = [MainActivityComponent::class])
class AppModule {
    @Provides
    @Singleton
    fun provideSharedPreferences(app : App) : SharedPreferences
    = app.getSharedPreferences("default", Context.MODE_PRIVATE)
}
```

AppComponent 는 빌더 또는 팩토리를 통해 생성됩니다. 저는 빌더를 사용하여 정의하였습니다.

```
class App : Application() {
    lateinit private var appComponent : AppComponent

    override fun onCreate(){
        super.onCreate()
        appComponent = DaggerAppComponent
            .app(this)
            .appModule(AppModule())
            .build()
    }
    fun getAppComponent() = appComponent
}
```

App클래스를 매니페스트에 등록하는 작업도 진행해야 합니다.

애플리케이션 인스턴는 시스템에 의해서만 생성될 수 있기 때문에 애플리케이션이 생성된 후 빌더의 @BindsInstance 메소드를 통해 오브젝트 그래프에 바인딩합니다.

AppModule에서는 애플리케이션의 생명주기 동안 싱글턴으로 취급할 SharedPreference를 제공하며 

싱글턴이 아닌 인스턴스를 가지고 싶다면 어노테이션을 제거하면 됩니다.

액티비티를 위한 컴포넌트는 서브컴포넌트로 구성해야 하므로 서브 컴포넌트의 클래스를 애플리케이션 모듈의 멤버로 추가합니다.

```

@Subcomponent(modules = [MainActivityModule::class])
@ActivityScope
interface MainActivityComponent {
    fun mainFragmentComponentBuilder() : MainFragmentComponent.Builder
    fun inject(activity : MainActivity)
    
    @Subcomponent.Builder
    interface Builder{
        fun setModule(module : MainActivityModule)
        @BindsInstance
        fun setActivity(activity: MainActivity)
        fun build() : MainActivityComponent
    }
}
```

```
@Module(subcomponents = [MainFragmentComponent::class])
class MainActivityModule{
  @Provides
  @ActivityScope
  fun provideActivityName()=MainActivity::class.getSimpleName()
}
```


