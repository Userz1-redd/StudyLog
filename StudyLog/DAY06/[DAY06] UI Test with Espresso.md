
# UI Test는 왜 필요할까?

UI테스트가 필요한 이유는 아래와 같이 정리되어 있습니다.

- 단일 앱 내에서 사용자 상호작용을 테스트하면 예상치 못한 결과가 발생하거나 불만족스러운 경험을 하지 않도록 할 수 있습니다.

- 앱의 UI가 올바르게 작동하는지 확인하려면 UI테스트를 만드는 습관을 들여야 합니다.

- AndroidX 테스트에서 제공하는 Espresso 테스트 프레임워크는 단일 타겟 앱 내에서 사용자 상호작용을 시뮬레이션 하는 UI테스트를 작성하기 위한 API를 제공합니다.

- Espresso는 테스트 중인 앱의 UI와 테스트 작업을 자동으로 동기화합니다. 

- Espresso는 기본스레드가 유휴상태인 시점을 감지하므로 적절한 시간에 테스트 명령어를 실행하여 테스트 신뢰성을 향상시킬 수 있습니다.

- 이 기능을 사용하면 Thread..sleep()과 같은 타이밍 해결방법을 테스트 코드에 추가하지 않아도 됩니다.


먼저 관련 Dependency를 추가합니다.

```
androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.0'
```



Espresso는 크게 3가지 컴포넌트를 가집니다.

-	ViewMatchers : View를 찾음

-	ViewActions : View에 Action을 수행함

-	ViewAssertions : View상태에 대한 assert(검증)를 수행함

아래는 Espresso를 통해 특정 View를 가져오고, 동작을 지정하고 확인하는 예제입니다.

```
onView(withId(R.id.text_email))
	.perform(ViewActions.typeText(test@naver.com),ViewActions.closeSoftKeyboard())

onView(withId(R.id.submit_btn))
	.perform(ViewActions.click())
```
ViewMatcher는 withId(), withText()와 같은 메소드들이 있습니다.


ViewActions에는 click(), typeText(), pressKey(), cleartext(), scrollTo()등의 대표적인 메소드들이 있습니다.

ViewAssertions 객체에서 대표적으로 제공하는 메소드는 다음과 같습니다.
-	matches() - 파라미터로 ViewMatchers 객체가 사용됩니다.
-	doesNotExist()
참고로, Assertion 의 역할을 onView() 에서 해결하는 것은 좋은 예시가 아닙니다.
 
예를 들어 특정 View가 "Hello!" 라는 문자열을 갖는지 테스트 할 때에 좋은 예와 나쁜 예를 비교 해보겠습니다.
좋은 예시 )
```
onView(...).check(matches(withText("Hello!")))
```
나쁜 예시 )
```
onView(allOf(withId(...), withText("Hello!"))).check(matches(isDisplayed()))
```

## ActivityTestRule

안드로이드 UI테스트에서는 유닛테스트의 @Before처럼 각 테스트마다 전처리해주어야 할 작업을 @Rule어노테이션을 통해 사용할 수 있습니다.

@Rule의 대표적인 사용처인 ActivityTestRule클래스는 단일 액티비티에서 테스트할 때 사용됩니다.

ActivityTestRule 객체를 통해서 액티비티를 실행하거나 종료할 수 있습니다.

-	launchActivity(Intent) : 파라미터로 넘어온 Intent를 통해 액티비티를 실행합니다.

-	finishActivity() : 액티비티를 종료합니다.

-	getActivity() : 현재 액티비티의 인스턴스를 가져옵니다.

@Rule 어노테이션은 @Before보다 먼저 수행되며 @After어노테이션이 달린 메소드까지 수행을 마치면 제거됩니다.

간단한 테스트 예제입니다.

```
@RunWith(AndroidJUnit4.class)
@MediumTest
class ExampleEspressoTest {
 
    @Rule
    var activityRule : ActivityTestRule<SimpleActivity> =
            ActivityTestRule<>(SimpleActivity.class)
 
    @Test
    fun testHelloEspresso() {
            onView(withId(R.id.button_simple)).perform(click());
            onView(withId(R.id.text_simple)).check(matches(withText("Hello Espresso!")))
    }
}
```

버튼을 클릭하면 텍스트를 Hello Espresso!로 설정하는 간단한 예제입니다. 

여기서 @MediumTest라는 어노테이션을 볼 수 있습니다. 이런것들을 FiltersAnnotaion이라고 하며 아래와 같은 것들이 존재합니다.

@FlakyTest

-	concurrency, caching 등 같은 수행에 대해 매번 같은 성공/실패 결과를 내지 않을 수 있는 테스트를 할 때 사용됩니다. 종종 버그를 찾는데 사용됩니다.

@LargeTest

-	class or method 수준에 적용되는 어노테이션입니다.

-	큰 단위의 테스트에 사용되는 어노테이션입니다. (수행 시간 > 1000 ms)

-	주로 데이터베이스나 네트워크 등 고비용 자원을 사용해야 할 때 사용됩니다.

@MediumTest

-	@LargeTest 보다는 작은 단위의 테스트에 사용되는 어노테이션입니다. (수행 시간 < 1000 ms)

- 단일 컴포넌트나 작은 규모의 컴포넌트들을 테스트할 때 주로 사용됩니다.

- 데이터베이스나 ContentProvider 등은 접근이 가능하지만 네트워크 사용은 제한됩니다. long-runnging 작업의 경우 mocking 하여 테스트 해야합니다.

@SmallTest

- @MediumTest 보다도 작은 단위의 테스트에 사용되는 어노테이션입니다. (수행 시간 < 200 ms)

-	사실상 유닛 테스트에 가깝습니다. @SmallTest 는 특정 논리적인 조건문에 집중하여 검증해 나가야 합니다.

-	파일 시스템, 데이터베이스, 네트워크 등 리소스에 대한 권한이 주어지지 않기 때문에 외부 리소스에 대해서는 mocking 하여 테스트해야 합니다.

@RequiresDevice

-	실제 물리적인 디바이스에서만 테스트가 동작합니다. 에뮬레이터에서는 동작하지 않습니다.

@SdkSuppress

-	테스트가 수행될 minimum or maximum API Level 을 설정할 수 있습니다.

-	class or method 단위에 적용할 수 있습니다.

@Suppress

- 테스트에서 제외시키는 어노테이션입니다.

- 이 어노테이션이 붙은 테스트는 테스트 되지 않습니다.

