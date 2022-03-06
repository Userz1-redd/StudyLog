# ViewModel 활용하기

- Application 객체를 생성자로 받는 AndroidViewModel이 존재합니다. Context는 뷰모델에서 사용하는것을 지양하지만, 꼭 필요하다면 사용할 수 있습니다.

- 커스텀 ViewModelFactory를 구현하지 않아도 된다는 장점이 있습니다.

- SavedStateHandle은 ViewModel의 생성자의 인자로 넘겨서 사용합니다. 이또한 커스텀 ViewModelFactory를 사용하지 않아도 됩니다.

- SavedStateHandle 객체는 데이터를 맵으로 관리하며 LiveData도 맵으로 관리하여 라이브데이터를 편리하게 사용할 수 있습니다.

- 기존에 뷰모델 생성자에 repository등을 추가하려면 아래와 같이 뷰모델 팩토리를 생성하고 사용해야 합니다. 현재는 예시이기 때문에 string을 받는다고 가정합니다.

```
class DateViewModel(repository : String) : ViewModel(){  
}
```
이때, 아래와 같이 액티비티에서 뷰모델을 선언한다면, 에러가 발생합니다.

```
class DateActivity : AppCompatActivity() {
   lateinit var mBinding : ActivityDateBinding
   lateinit var dateViewModel: DateViewModel
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       dateViewModel = ViewModelProvider(this)[DateViewModel::class.java]
       mBinding = ActivityDateBinding.inflate(layoutInflater)
       setContentView(mBinding.root)
     

   }

}
```

이럴때 뷰모델 Factory를 정의하여 인자를 넘겨주어야 합니다. 아래와 같이 ViewModelProvider.Factory를 상속받아 사용합니다.

```
class DateViewModelFactory(private val string : String) : ViewModelProvider.Factory {
   override fun <T : ViewModel?> create(modelclass: Class<T>): T {
       if(modelclass.isAssignableFrom(DateViewModel::class.java)){
           return DateViewModel(string) as T
       }
       throw IllegalAccessException("unknown ViewModel class")
   }
}
```
액티비티에서 뷰모델을 사용할 때, 아래와 같이 사용하게 됩니다.

```
class DateActivity : AppCompatActivity() {
   lateinit var mBinding : ActivityDateBinding
   lateinit var dateViewModel: DateViewModel
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       val dateViewModelFactory = DateViewModelFactory("넘겨받는값")
       dateViewModel = ViewModelProvider(this,dateViewModelFactory)[DateViewModel::class.java]
       mBinding = ActivityDateBinding.inflate(layoutInflater)
       setContentView(mBinding.root)
  

   }
}
```


## SavedStateHandle

SavedStateHandle을 다루기 위해서는 viewModel의 생성자에 SavedStateHandle을 추가해야 합니다.
```
class MainViewModel(val savedStateHandle : SavedStateHandle) : ViewModel(){
	…
}
```
현재 ViewModel의 생성자 파라미터가 (Application, SavedStateHandle) 또는 SavedStateHandle이라면 이미 정의된 SavedStateViewModelFactory 클래스를 이용할 수 있습니다. 아래는 ViewModel을 인스턴스화 하는 코드입니다.

```
class MainActivity : AppCompatActivity() {
    private lateinit var viewModel: MainViewModel
    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        viewModel = ViewModelProvider(this, SavedStateViewModelFactory(application, this)).get(MainViewModel::class.java)
    }
}
```
이제 ViewModel의 SavedStateHandle을 이용하여 간단한 데이터를 저장하고 복원하는 코드를 구현할 수 있습니다.
아래는 정수형 count 변수의 상태를 저장하고 복원하는 코드입니다.

```
class MainViewModel(private val savedStateHandle: SavedStateHandle) : ViewModel() {
   var count = 0
   set(value) {
       savedStateHandle.set("count",value)
       field = value
   }
   init {
       savedStateHandle.get<Int>("count")?.run {
           count = this
       }
   }
}
```
이처럼 키- 값 형식으로 불러오는것이 가능합니다.

 SavedStateHandle 클래스에는 아래와 같이 필요한 메소드가 정의되어 있습니다.

get(String key)
contains(String key)
remove(String key)
set(String key, T value)
keys()
getLiveData(String key)

하지만 SavedStateHandle에 저장되는 데이터는 단순하고 가벼워야 합니다. 복잡한 데이터는 DB를 이용해야 합니다.

다음으로는 ViewModel에서 매번 ViewModelFactory를 생성하지 않고 Dagger를 활용하는 방법에 대해 정리하겠습니다.
