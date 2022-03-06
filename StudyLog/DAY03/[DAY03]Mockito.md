
# Mockito
Mockito는 객체를 Mocking하는데 사용되는 자바 기반의 라이브러리입니다. Junit과 함께 유닛 테스트 작성시에 사용됩니다. 안드로이드에서도 Unit Test를 작성할 때 공식적으로 Mockito를 사용하도록 권장하고 있습니다.

여기서 모킹이라는 것은 쉽게 말해, 흉내낸다는 것을 의미합니다.
코틀린 사용을 기준으로 아래와 같은 dependency를 추가합니다.

```
androidTestImplementation("org.mockito:mockito-android:2.24.5")
testImplementation 'org.mockito:mockito-inline:2.13.0'
```

간단하게 나이와 이름이 존재하는 Person객체를 사용하여 모킹하는 테스트입니다.
```
data class Person(var age : Int, var name : String) {
}
```

```

class PersonTest{
    @Test
    fun personTest(){
        var person = mock(Person::class.java)
        assertTrue(person!=null)

        `when`(person.name).thenReturn("MyName")
        `when`(person.age).thenReturn(26)

        assertTrue("MyName"==person.name)
        assertTrue(26==person.age)
    }

}

```
테스트의 진행순서는 다음과 같습니다.
1.	wock을 통해 person객체를 생성
2.	when()함수를 통해서 person객체의 name, age반환값 설정
3.	반환값 일치여부 확인

간단하게 mock을통한 객체 생성 후 유닛테스트를 진행할 수 있었습니다.
