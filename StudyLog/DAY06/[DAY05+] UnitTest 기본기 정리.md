# UnitTest in Android

안드로이드의 유닛테스트는 크게 계측테스트(androidTest)와 로컬단위의 테스트(test)가 있습니다.

androidTest의 경우 안드로이드 프레임워크에 종속성이 있는 테스트이고

test는 안드로이드의 프레임워크와 관계없이 테스트할 수 있습니다,

## Local 단위의 테스트

[테스트 대상 클래스]


간단히 반지름을 입력하면 지름, 넓이를 구하는 함수를 포함한 클래스를 작성합니다.
```
class MyCalc {
    private val pi = 3.14

    fun calculateCircumference(radius : Double) = 2 * pi * radius
    
    fun calculateArea(radius : Double) = pi * radius * radius
}
```

[테스트 코드]

```
class MyCalcTest{
    private lateinit var myCalc: MyCalc

    @Before
    fun setUp(){
        myCalc = MyCalc()
    }

    @Test
    fun calculateCircumference_radiusGiven_returnsCorrectResult(){
        val result = myCalc.calculateCircumference(2.1)
        assertThat(result).isEqualTo(13.188)

    }

    @Test
    fun calculateCircumference_zeroRadius_returnsCorrectResult(){
        val result = myCalc.calculateCircumference(0.0)
        assertThat(result).isEqualTo(0.0)

    }


    @Test
    fun calculateArea_radiusGiven_returnsCorrectResult(){
        val result = myCalc.calculateArea(2.1)
        assertThat(result).isEqualTo(13.8474)

    }

    @Test
    fun calculateArea_zeroRadius_returnsCorrectResult(){
        val result = myCalc.calculateCircumference(0.0)
        assertThat(result).isEqualTo(0.0)

    }

}

```
테스트로 truth 라이브러리의 assertThat을 통해 테스트를 실행할 수 있습니다.

Junit4의 Assert로도 테스트 검증이 가능합니다.

테스트 코드에서는 유닛테스트에서 실행 전과 끝낼때 실행될 함수를 @Before과 @After로 지정할 수 있습니다. 

유닛테스트 작성시 테스트 함수의 이름을 정하는 컨벤션도 따로 존재했습니다. 참고사항으로 추가했습니다.
```
MethodName_StateUnderTest_ExpectedBehavior
cons: should be renamed if method change name
example: isAdult_AgeLessThan18_False

MethodName_ExpectedBehavior_StateUnderTest
cons: should be renamed if method change name
example: isAdult_False_AgeLessThan18

testFeatureBeingTested
cons: “test” prefix is redundant
example: testIsNotAnAdultIfAgeLessThan18

FeatureToBeTested
cons: no clue what result is expected from name
example: IsNotAnAdultIfAgeLessThan18

Should_ExpectedBehavior_When_StateUnderTest
cons: duplicates `should` and `when`, long name
example: Should_ThrowException_When_AgeLessThan18

When_StateUnderTest_Expect_ExpectedBehavior
cons: duplicates `when` and `expect`
example: When_AgeLessThan18_Expect_isAdultAsFalse

Given_Preconditions_When_StateUnderTest_Then_ExpectedBehavior — Behavior-Driven Development (BDD)
cons: duplicates `given`, `when`, `then`; really long names
example: Given_UserIsAuthenticated_When_InvalidAccountNumberIsUsedToWithdrawMoney_Then_TransactionsWillFail

```

## ViewModel, LiveData, Mockito, JUnit4, Truth

안드로이드에서 자주 사용하는 ViewModel내부의 로직 또한 테스트할 수 있습니다. 

테스트 과정은 ViewModel의 함수가 호출된 후, LiveData에 값이 정상적으로 세팅되는지 확인하는 작업으로 테스트하게 됩니다.


뷰모델로 계산 로직을 이동시킵니다. 위의 예제를 뷰모델, 라이브데이터에 세팅하였습니다.
```
class CalcViewModel(private var calculations: Calculations) : ViewModel() {
    var radius = MutableLiveData<String>()

    var area = MutableLiveData<String>()
    val areaValue: LiveData<String>
        get() = area

    var circumference = MutableLiveData<String>()
    val circumferenceValue: LiveData<String>
        get() = circumference


    fun calculateArea(radius: Double) {
        val calculatedArea = calculations.calculateArea(radius)
        area.value = calculatedArea.toString()
    }

    fun calculateCircumference(radius: Double) {
        val calculatedCircumference = calculations.calculateCircumference(radius)
        circumference.value = calculatedCircumference.toString()
    }

}
```

이후 test에 유닛테스트를 작성합니다.
다만 테스트시 이전 유닛테스트와 다르게 여러 코드를 추가해야 합니다.

```@get:Rule
var instantTaskExecutorRule = InstantTaskExecutorRule()
```
rule은 규칙을 추가한다는 것을 어노테이션으로 명시하며 현재 규칙은

백그라운드 작업과 연관된 모든 아키텍쳐 컴포넌트들을 한개의 스레드에서 동기적으로 실행되도록 하는 것입니다.

모든 작업들을 동기적으로 실행해주기 때문에 LivaData 테스트 시 필수로 사용됩니다.

두번째로 Mockito입니다. Mockito에 대한 자세한 설명은 추후에 추가할 예정입니다.

Mock은 호출에 대한 기대를 명세하고, 해당 내용에 따라 동작하도록 프로그래밍됩니다.

즉, 다른 연관된 로직에 관계없이 쉽게 테스트하기 위해 필요한 객체를 Mocking해서 이 객체의 어떤 함수를 호출하면 어떤것을 반환할지 지정할 수 있습니다.(파라미터나 타입 등은 맞추어야 합니다.)

그리고 Mockito에는 보통 구현객체가 아닌 인터페이스를 사용합니다. 테스트에 사용할 설정을 위한 Calculations를 모킹합니다.

```
@Before
fun setUp() {
    calculations = Mockito.mock(Calculations::class.java)
    Mockito.`when`(calculations.calculateArea(2.1)).thenReturn(13.8474)
    Mockito.`when`(calculations.calculateCircumference(1.0)).thenReturn(6.28)
    calcViewModel = CalcViewModel(calculations)
}

```
현재 Calculations 클래스를 모킹했고, calculation내의 메소드에서 호출시 반환할 값을 기존 calculations클래스와 관계 없이 선언할 수 있습니다. 

현재 4번째 라인의 코드는 calculations의 calculateArea라는 메소드의 인자로 2.1을 넣어주면, 13.8474를 반환하라고 명시해준 것입니다.

예를들어 어떤 메소드를 테스트 하는데 외부의 A라는 객체가 필요하고

A의 인터페이스(또는 클래스)내에 메소드 B,C가 있다면

실제 A라는 인터페이스의 구현체(클래스)내에 정의한 메소드를 호출하여 테스트 하는것이 아니라

A라는 가짜객체(목객체)에 B함수의 인자로 1을 넣으면 5를 리턴해라! 라고 임의로 명시할 수 있는 것입니다.

Mock객체에 대한 자세한 설명은 [여기](https://oss.navercorp.com/jihoon97/AndroidStudy/blob/master/StudyLog/DAY06/%5BDAY05%2B%5D%20Mock.md) 에 정리하였습니다.

이후 모킹한 객체를 CalcViewModel의 인자로 사용합니다.

테스트는 아래와 같이 작성할 수 있습니다.
```
    @Test
    fun calculateArea_radiusSent_updateLiveData(){
       calcViewModel.calculateArea(2.1)
       val result = calcViewModel.areaValue.value
       assertThat(result).isEqualTo("13.8474")
    }

    @Test
    fun calculateCircumference_radiusSent_updateLiveData(){
        calcViewModel.calculateCircumference(1.0)
        val result = calcViewModel.circumferenceValue.value
        assertThat(result).isEqualTo("6.28")
    }

```
추후에 외부 Api또는 내부 Room DB에 대한 단위테스트도 작성할 예정입니다.
