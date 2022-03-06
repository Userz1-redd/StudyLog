# Kotlin

기존에 잘 알고있는 정보들을 제외하고, 강의를 들으며 몰랐던 사항들을 별도로 정리할 페이지입니다.

코틀린에는 삼항연산자는 존재하지않고 Elvis Operator가 존재합니다.

? : (null인경우의 값)

lateinit var 

=> Primitive타입에 사용이 불가능

by lazy

=> variable에 사용 불가능하고 value에만 사용가능

=> by lazy와 코드블럭을 함께 사용

=> ex) val testView : TextView by lazy{
            activity.findViewById<TextView>(R.id.title)
        }
        즉 코드블럭 내의 객체가 활성화 될때 testView에 주입
        
Property

- set, get field 설정 가능, value는 불변이기 때문에 set이 존재하지 않습니다.
```
var capName : String = "
  set(value) {
      field = value.toUpperCase()
  }
  get() = field
```

## Kotlin Object

Class

- 기본적으로 final class이며 상속을 위해선 open class로 선언해야 합니다.

- 인터페이스는 open이 기본으로 내재되어 있습니다.

Data Class

- equals()/hashCode() 쌍이 자동으로 생성되어 있습니다.

- toString()메소드가 자동으로 오버라이딩 되어있습니다.

- copy()가 생성되어 있습니다.

- 선언된 프로퍼티에 상응하는 componentN() 함수들을 생성합니다.

Sealed Class

- 여러 개의 자식 객체를 묶어놓은 집합 클래스 입니다.

- 내부 클래스는 부모클래스를 반드시 상속받아야 합니다.



