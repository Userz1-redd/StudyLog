



# Clean Architecture

- 클린아키텍쳐는 관심사에 따라 계층을 분리하는 관점입니다. 아래는 클린아키텍쳐를 설명할 때 가장 많이 등장하는 그림입니다.

<img width="615" alt="image" src="https://user-images.githubusercontent.com/58676668/148146849-091ac21b-5e13-400a-92da-4d047097508c.png">

- 클린 아키텍처는 총 4가지의 계층으로 이루어져 있습니다. 클린 아키텍처의 각 계층은 아래와 같습니다.
1. Entities
-  엔티티는 비즈니스 규칙을 캡슐화합니다. 엔티티는 메서드를 갖는 객체일 수도 있지만 데이터 구조와 함수의 집합일 수도 있습니다. 가장 일반적이면서 고수준의 규칙을 캡슐화하게 됩니다. 외부가 변경되더라도 이러한 규칙이 변경될 가능성이 적습니다.
2. Use cases
- 유스케이스는 애플리케이션의 고유 규칙을 캡슐화하며 엔티티로부터의 데이터 흐름을 조합합니다. 유스케이스 계층의 변경이 엔티티에 영향을 주어선 안되며 데이터베이스, 공통프레임워크 및 UI에 대한 변경으로부터 격리됩니다.
3. Interface Adapters(Presenters)
- 인터페이스 어댑터는 Entity 및 UseCase의 편리한 형식에서 데이터베이스 및 웹에 적용할 수 있는 형식으로 변환합니다. 이 계층에는 MVP패턴의 Presenter, MVVM 패턴의 ViewModel이 포함됩니다. 순수한 비즈니스로직만을 담당합니다.
4. Frameworks & Drivers(Web, DB)
프레임워크와 드라이버는 상세한 정보들을 두게 됩니다. 웹 프레임워크, 데이터베이스, UI, HTTP Client등으로 구성된 가장 바깥쪽 계층입니다.

- 클린 아키텍처가 잘 동작하기 위해서는 의존성 규칙을 잘 지켜야 합니다. 각각의 클래스는 한 가지 역할만을 수행하며 서로 의존 관계를 어떻게 할지 규칙이 정해져있으며 이를 지켜야합니다. 그림에서 내부로 갈수록 의존성이 낮아지며 안드로이드에서 비즈니스 로직을 담당하는 ViewModel은 로컬DB나 Web같은 세부적인 사항에 의존하지 않아야 합니다.

클린 아키텍처를 통한 관심사의 분리는 여러가지의 이점을 얻을 수 있습니다.
- 새로운 기능의 빠른 적용
- 집중화된 클래스에 따른 프로젝트 유지 관리 용이성
- 쉬운 패키지 구조 탐색
- 쉬운 테스트 코드 작성


<img width="630" alt="image" src="https://user-images.githubusercontent.com/58676668/148147524-a9258704-8515-4e43-81d2-b065992fd51b.png">

클린 아키텍처를 안드로이드에서 사용할 경우 일반적으로 Presentation, Domain, Data 총 3개의 레이어로 나누게 됩니다.

1.	Presentation
-	화면과 입력에 대한 처리 등 UI와 관련된 부분을 담당합니다. Activity, Fragment, View, Presenter와 ViewModel이 해당합니다. Presentation 계층은 Domain계층에 대한 의존성을 가지고 있습니다.
2. Domain
-	애플리케이션의 비즈니스 로직에서 필요한 UseCase와 Model을 포함하고 있습니다. UseCase는 각 개별 기능 또는 비즈니스 논리 단위이며, Presentation, Data 계층에 대한 의존성을 가지지 않고 독립적으로 분리되어 있습니다. 안드로이드의 의존성을 갖지 않고 java 및 kotlin 코드로만 구성하며 다른 애플리케이션에서도 사용할 수 있습니다. Repository 인터페이스도 포함되어 있습니다.
3. Data
- Domain 계층에 의존성을 가지고 있습니다. Domain 계층의 Repository 구현체를 포함하고 있으며, 데이터베이스, 서버와의 통신도 Data 계층에서 이루어집니다. 또한 mapper 클래스를 통해 Data 계층의 모델을 Domain 계층의 모델로 변환해주는 역할도 하게 됩니다.


# MVVM 사용해보기

## ViewModel의 개념
-	뷰모델은 UI관련 데이터를 저장하고 UI 로직을 처리하고 관리하기 위해 만들어졌습니다.
-	뷰모델은 생명주기를 고려하여 동작하도록 구현되어 있습니다.
## ViewModel의 생명주기
-	View의 생명주기는 안드로이드 프레임워크에 의해 제어됩니다.
-	화면의 Configuration이나 변경사항으로 액티비티가 재실행되는 경우, 기존의 데이터를 보존하지 못하는 경우가 있습니다. 
-	하지만 뷰모델은 뷰가 완전히 종료할 때 까지 뷰의 라이프사이클에 관계없이 동작하므로 데이터를 유지할 수 있습니다. 또한 UI데이터 저장과 로직 처리를 뷰모델이 담당하여 뷰의 역할을 분담합니다.
-	View -> ViewModel -> Repository의 구조로 의존성이 단방향으로만 생성되어 있습니다.
## ViewModel 사용 목적
-	View는 단순히 UI를 업데이트 하는 작업만을 해야합니다.
-	뷰모델의  사용으로 테스트코드 작성이 간편해지며 
-	동일한 액티비티에 종속되어 있는 프래그먼트간의 데이터 공유를 쉽게 구현할 수 있습니다.
## ViewModel 간단한 실습

먼저 모듈 단위의 gradle에 의존성을 추가합니다.
```  
implementation "androidx.lifecycle:lifecycle-viewmodel:2.4.0"
```
 간단하게 버튼을 누를때마다 숫자가 1씩 증가하는 앱에 뷰모델을 적용시켜보았습니다.
 
 ### 뷰모델 구성
 ```
 class MainViewModel : ViewModel() {
    private var _number = MutableLiveData<Int>().apply{
        value = 0
    }
    val number : LiveData<Int>
        get() = _number
    fun increaseNumber(){
        _number.value = (number.value ?: 0) +1
    }
}
```
### 액티비티 구성

```
class MainActivity : AppCompatActivity() {
    private lateinit var mBinding : ActivityMainBinding
    private val model : MainViewModel by lazy{ViewModelProvider(this)[MainViewModel::class.java]}
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        mBinding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(mBinding.root)
        btnClickListener()
    }
    fun btnClickListener(){
        mBinding.btn.setOnClickListener {
            model.increaseNumber()
        }
        //뷰모델의 number를 관찰하는 형태. number가 변하면 Observer가 수행하는 작업이 자동으로 갱신됨
        model.number.observe(this, Observer{
            mBinding.text.text = it.toString()
        })
    }
}
```

