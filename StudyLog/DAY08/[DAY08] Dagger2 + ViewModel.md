기본적으로 ViewModel을 사용할 때, 생성자에 아무런 파라미터도 던져지지 않는 형태의 ViewModel은 사용할 일이 없습니다.

그렇다면 큰 규모의 애플리케이션에서는 각 액티비티마다 뷰모델을 가지고 있고, 뷰모델의 인자로 넘겨주기 위해 매번 팩토리를 생성한다면 클래스의 크기는 매우 커지고 코드는 많아질 것입니다.

이럴때 Dagger를 통해 해결할 수 있습니다.

먼저 공통으로 사용할 ViewModelFactory를 생성합니다.

```
class ViewModelFactory @Inject constructor(
    val viewModelMap : Map<Class<out ViewModel>, @JvmSuppressWildcards Provider<ViewModel>>
    ) : ViewModelProvider.Factory{
    override fun <T : ViewModel?> create(modelClass : Class<T>): T {
        return viewModelMap[modelClass]?.get() as T
    }
}

@Module
abstract class ViewModelFactoryModule{
  @Binds
  abstract fun bindViewModelFactory(viewModelFactory : ViewModelFactory) : ViewModelProvider.Factory
}

```

ViewModelFactory의 생성자에는 Map 객체가 있습니다. 이 Map객체를 통해 이후에 @IntoMap 어노테이션으로 주입시킵니다.

즉, 팩토리 생성시 전달될 map 객체는 Dagger가 주입해주는 것입니다.

viewmodel의 생성자의 파라미터로 repository가 있다고 치면

```
class MainViewModel @Inject constructor(repository : Repository) : ViewModel(){
    fun getString(){
        repository.getName()
    }
}

class SplashViewModel @Inject constructor(repository : Repository) : ViewModel(){
    ...
}


//제공할 뷰모델의 타입이 같으므로 구분하기 위한 Key설정
@MapKey
@Target(AnnotationTarget.FUNCTION, AnnotationTarget.PROPERTY_GETTER, AnnotationTarget.PROPERTY_SETTER)
annotation class ViewModelKey(val value : KClass<out ViewModel>)



//하나의 모듈에서 객체 제공한다고 가정

@Module
class ViewModelModule{

  
  //applicationmodule에서 singleton으로 Repository 객체 제공한다고 가정
  
  @Provides
  @IntoMap
  @ViewModelKey(MainViewModel::class)
  fun provideMainViewModel(repository : Repository) : ViewModel {
    return MainViewModel(repository)
  }
  
  
  
  @Provides
  @IntoMap
  @ViewModelKey(SplashViewModel::class)
  fun provideSplashViewModel(repository : Repository) : ViewModel {
     return SplashViewModel(repository)
  } 
}
```


```
@Component(modules = [MainModule::class, ViewModelModule::class, ViewModelFactoryModule::class])
interface MainComponent {
   fun inject(mainActivity: MainActivity)
}

@Module
class MainModule{
  ...
}
```

```
class MainActivity : AppCompatActivity() {
    var TAG = "Main"
    @Inject
    lateinit var viewModelFactory: ViewModelFactory
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        DaggerMainComponent.builder()
            .mainModule(MainModule())
            .build()
            .inject(this)
        val viewModel = ViewModelProviders.of(this, viewModelFactory)
            .get(MainViewModel::class.java)

        Log.d(TAG, "my name is " + viewModel.getMyName())
    }
}
```

실제 

