# Dagger Hilt

Dagger Hilt는 기존의 높은 러닝커브를 가진 Dagger2를 보완하여 Dagger 사용을 단순화하고

표준화된 컴포넌트 세트와 스코프로 설정과 가독성/이해도를 쉽게 만들고

쉬운 방법으로 다양한 빌드 타입에 대해 다른 바인딩을 제공하기 위해 만들어졌습니다.

저는 [코드랩](https://developer.android.com/codelabs/android-hilt#1)을 따라하며 공부해보고자 합니다.

코드랩에서 코드를 다운받습니다.

```
class LogApplication : Application() {

    lateinit var serviceLocator: ServiceLocator

    override fun onCreate() {
        super.onCreate()
        serviceLocator = ServiceLocator(applicationContext)
    }
}

```
시작 코드를 살펴보면 LogApplication 클래스에 저장된 ServiceLocator클래스 인스턴스를 확인할 수 있습니다.
ServiceLocator는 요구되는 클래스에서 요청에 따라 가져온 종속 항목을 만들고 저장합니다.

이 클래스는 앱이 소멸될 때 함께 소멸되므로 앱의 생명주기에 연결되는 종속 항목의 컨테이너라고 생각하면 됩니다.

### 컨테이너
컨테이너는 코드베이스에 종속 항목을 제공하는 클래스로, 다른 유형의 앱 인스턴스를 만드는 방법을 알고 있습니다.

이러한 인스턴스를 생성하고, 수명 주기를 관리하여 인스턴스를 제공하는 데 필요한 종속 항목 그래프를 관리합니다.

컨테이너는 컨테이너에서 제공하는 유형의 인스턴스를 가져올 수 있는 메소드를 노출합니다. 이러한 메소드는 항상 다른 인스턴스 

또는 동일한 인스턴스를 반환할 수 있습니다. 메소드에서 항상 동일한 인스턴스를 제공한다면 인스턴스 유형은 컨테이너로 범위가 지정됩니다.

Hilt는 개발자가 직접 작성해야 하는 코드를 생성해주고 Android 애플리케이션에서 수동으로 DI 또는 서비스 로케이터 패턴을 사용해야하는 불필요한 보일러 플레이트 코드를 삭제합니다.

Hilt를 사용을 위해서는 프로젝트 단위의 build.gradle에 의존성을 추가합니다.
```
 dependencies {
        ...
        classpath "com.google.dagger:hilt-android-gradle-plugin:$hilt_version"
    }
```

app 모듈에도 의존성을 추가합니다.
```
apply plugin: 'kotlin-kapt'
apply plugin: 'dagger.hilt.android.plugin'

android {
    ...
}

dependencies {
    ...
    implementation "com.google.dagger:hilt-android:$hilt_version"
    kapt "com.google.dagger:hilt-android-compiler:$hilt_version"
}

```

LogApplication 클래스의 ServiceLocator 인스턴스를 사용하고 초기화하는 방식과 마찬가지로 

앱의 수명 주기에 연결된 컨테이너를 추가하려면 @HiltAndroidApp으로 Application클래스에 주석을 달아야 합니다.

```
@HiltAndroidApp
class LogApplication : Application() {
...
}
```
@HiltAndroidApp은 종속 항목 삽입을 사용할 수 있는 애플리케이션의 기본 클래스가 포함된 Hilt 코드 생성을 트리거합니다. 

애플리케이션 컨테이너는 앱의 상위 컨테이너이므로 다른 컨테이너는 이 상위 컨테이너에서 제공하는 종속 항목에 액세스할 수 있습니다. 

Hilt를 사용할 대상이 되는 Activity, Fragment 등에 @AndroidEntryPoint 어노테이션을 작성합니다.
또한 Dagger2와 마찬가지로, 주입받을 인스턴스에 @Inject 어노테이션을 작성합니다. 예제에서는 필드주입 방법을 사용하였습니다.
```
@AndroidEntryPoint
class LogsFragment : Fragment() {
    @Inject lateinit var logger : LoggerLocalDataSource
    @Inject lateinit var dateFormatter : DateFormatter
}
```

즉 기존에 아래와 같이 작성했던 코드를 @HiltAndroidApp 하나로 해결할 수 있는 것입니다.
```
@Singleton
@Component(
    modules = [ViewModelModule::class, ViewModelFactoryModule::class, AppSubComponentsModule::class, RepositoryModule::class, LocalDataModule::class, RemoteDataModule::class]
)
interface AppComponent {
    @Component.Factory
    interface Factory {
        fun create(@BindsInstance context: Context): AppComponent
    }

    fun mainComponent(): MainComponent.Factory

}
```



마찬가지로 DateFormatter, LoggerLocalDataSource의 생성자에 @Inject를 생성자 주입으로 작성합니다.

```
class DateFormatter @Inject constructor() {}

class LoggerLocalDataSource @Inject constructor(private val logDao : LogDao){
}
```

Dagger와 마찬가지로 Scope를 통해 특정 유형에 관해 동일한 인스턴스를 제공할 수 있습니다.
‘
예를들어 LoggerLocalDataSource 인스턴스를 제공하는데에 항상 동일한 인스턴스를 제공한다면 @Singleton을 작성하고, @ActivityScope를 통해 따로 관리하는 것도 가능합니다.

기존에 커스텀 스코프를 모두 작성하여 사용했던 것과 달리 자주 사용하는 표준화된 컴포넌트 세트와 스코프를 제공합니다.

<img width="689" alt="image" src="https://user-images.githubusercontent.com/58676668/148501243-9e27bf83-e8e9-4ad4-b47c-b6afb7f5a897.png">

## 모듈

Hilt모듈은 Dagger2와 달리 @Module 어노테이션 이외에 @InstallIn 주석이 추가됩니다. 

@InstallIn은 어느 컨테이너에서 Hilt구성요소를 지정하여 결합을 사용할 수 있는지 Hilt에게 알려줍니다.

또한 Hilt에서는 InstallIn 주석에 참조할 수 있는 관련 Hilt 구성요소들을 기본적으로 제공합니다.

<img width="725" alt="image" src="https://user-images.githubusercontent.com/58676668/148500094-76bac3a5-407a-477b-a117-eddec305c1d2.png">

애플리케이션 컨테이너 범위로 지정되는 Databased경우, 아래와 같이 작성할 수 있습니다.

```
@InstallIn(ApplicationComponent::class)
@Module
object DatabaseModule{
...
}
```
LoggerLocalDataSource의 생성자로 주입받는 LogDao를 주입시키려 한다면 모듈에 아래와 같이 작성합니다.

```
@InstallIn(ApplicationComponent::class)
@Module
object DatabaseModule{
    @Provides
    fun provideLogDao(database : AppDatabase) : LogDao = database.logDao
}
```

### 인터페이스 주입

인터페이스는 모듈 내의 @Binds 어노테이션을 사용하여 구현할 수 있습니다. 예제 코드에서는 아래와 같은 NavigationModule 추상클래스를 만들어보았습니다.

```
@InstallIn(ActivityComponent::class)
@Module
abstract class NavigationModule {
    @Binds
    abstract fun bindNavigator(impl: AppNavigatorImpl)
}
```

### 한정자 사용

Dagger2 에서 사용했던 지정 한정자 또한 간편하게 사용할 수 있습니다.
```
@Qualifier
annotaion class DatabaseLogger
```
