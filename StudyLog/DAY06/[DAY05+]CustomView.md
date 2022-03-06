# CustomView

커스텀뷰를 그리기 위한 기본적인 방법은 2차원의 경우 안드로이드에서 제공하는 View를 상속받아 재정의하는 방법, 3차원의 경우 OpenGL을 활용합니다.

2차원 그래픽 처리는 크게

1. 캔버스 객체에 그리기

- 코드로 그리기 메소드를 직접 호출

- 규칙적으로 스스로 다시 그리기를 해야하는 응용 프로그램에 적합

2. 레이아웃 파일의 뷰 객체에 정의

- 뷰 계층 구조 그리기 절차에 의해 처리되도록 뷰 안에 그래픽이 포함되도록 정의

- 동적 변화가 거의 없는 간단한 그래픽에 사용

과 같은 방법이 존재합니다. 

### 캔버스에 그리기

1. 커스텀 뷰를 생성하여 그리는 방법

2. 그리기 전용의 화면을 제공하는 SurfaceView와 스레드를 이용하여 그리기

- 메인스레드와 독립적으로 그래픽을 처리하여 invalidate()를 호출할 필요가 없습니다.

프로젝트를 위한 커스텀뷰는 기존에 존재하지 않는 뷰이므로 View자체를 상속받아 모든것을 새로 그려야 합니다.

만약 여러 뷰를 합쳐서 반복적으로 사용한다면, ViewGroup을 상속받아 사용하면 될 것입니다.

커스텀뷰 예시 작성

커스텀뷰 작성을 위해서 View를 상속받습니다. 하지만 View를 상속받으면 기본 생성자가 없기 때문에 상위 클래스의 생성자를 호출해야합니다.

예시에서는 가장 기본 생성자를 추가하였습니다.
```
class CustomView : View {
    constructor(context: Context?) : super(context)
}
```
코틀린에서 커스텀뷰를 작성할 때 생성자는
```
constructor(context: Context?) : super(context) -> 코드에서 생성할 때 사용

ex) 
var customView : CustomView = CustomView()
setContentView(customView)

constructor(context: Context?, attrs: AttributeSet?) : super(context, attrs) 
-> xml에서 view를 Inflate할 때 사용, attrs 정의


constructor(context: Context?, attrs: AttributeSet?, defStyleAttr: Int) : super(
    context,
    attrs,
    defStyleAttr
)

constructor(
    context: Context?,
    attrs: AttributeSet?,
    defStyleAttr: Int,
    defStyleRes: Int
) : super(context, attrs, defStyleAttr, defStyleRes)
```

defStyleAttr =  뷰의 기본적인 속성들을 지정하기 위한 파라미터

defStyleAttr / defStyleRes는 R.attr.*와 R.style.* 를 넘기느냐의 차이입니다.

뷰를 그리기 위해 onDraw메소드를 override합니다.

onDraw메소드 내부에 paint(펜)과 canvas(종이)에 그린다고 생각하고 선을 그린다면
```
 override fun onDraw(canvas : Canvas){
        super.onDraw(canvas)

        var paint = Paint()
        paint.setColor(Color.GRAY)
        canvas.drawColor(Color.WHITE)
        canvas.drawLine(0.0f,0.0f,0.0f,10.0f,paint)

    }
```
와 같은 형태로 될 것입니다.

빨간색 원을 만드는 커스텀뷰를 생성한다면

```
class CustomView @JvmOverloads constructor(
    context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0
) : View(context, attrs, defStyleAttr){

    override fun onDraw(canvas: Canvas?) {
        super.onDraw(canvas)
        var paint = Paint()
        paint.color= Color.RED
        canvas?.drawCircle(200f,200f,150f,paint)
    }
}
```

여기서 @JvmOverloads 어노테이션은 생성자를 개별적으로 정의하지 않아도 사용할 수 있도록 오버로딩 해줍니다.

이 뷰를 xml에서 사용하기 위해서는 res -> values 경로에 attrs.xml파일을 생성해야합니다.

그리고 	<declare-styleable>태그를 생성하고, name 항목에는 커스텀뷰의 클래스명을 입력해줍니다.

그리고 attr태그에 원하는 속성과 형태를 입력하면 속성을 정의할 수 있습니다.

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="CustomView">
        <attr name="myShapeColor" format="color" />
        <attr name="myTextColor" format="color" />
        <attr name="myTextString" format="string" />
        <attr name="myTextSize" format="string" />
        <attr name="myStrokeColor" format="color" />
        <attr name="myStrokeWidth" format="string" />
    </declare-styleable>
</resources>
```

attrs.xml에 정의한 속성을 코드에서 참조할 땐, context.obtainStyledAttributes() 메소드를 사용하면 속성들이 typedArray라는 객체로 반환됩니다.

속성에서 정의한 format에 따라 typedArray객체에서 getString, getInt, getColor등을 사용하여 속성값을 반환할 수 있습니다.

매개변수는 R.styleable.CustomView_myShapeColor 와 같은 형태로 넣어주면 됩니다.

예시

```
var typedArr = context.obtainStyledAttributes(attrs,R.styleable.CustomView)
var myShapedColor = typedArr.getColor(R.styleable.CustomView_myShapeColor,Color.BLACK)
```
여기서 getColor의 두번째 값은 디폴트값을 설정하는 매개변수 입니다.

참조한 속성값을 이용하여 onDraw 메소드 등에 자유롭게 사용할 수 있습니다.

위에서 참조한 myShapedColor를 현재 그린 원의 색으로 연결시킨다면

```
override fun onDraw(canvas: Canvas?) {
    super.onDraw(canvas)
    var paint = Paint()
    paint.color= myShapedColor
    canvas?.drawCircle(200f,200f,150f,paint)
}

```
와 같이 사용할 수 있으며

Xml 내에서

```
<com.example.custemtest.CustomView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:myShapeColor="@color/black"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    />
```
app:myShapedColor 태그를 통해 접근할 수 있게 되었습니다.

