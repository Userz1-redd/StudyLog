온보딩 강의를 들으며 확실하게 알지 못했던 부분들을 정리하였습니다.

# Activity

Configuration에서 기본적으로 화면을 전환할때 액티비티가 재실행 됩니다. 이것을 막는 방법은

AndroidManifest파일 내의 액티비티 태그 안에

android:configChanges = "orientation|screenSize"로 막을 수 있습니다.

이 태그로 인해 onDestroy가 호출되어 액티비티가 재시작되는것 대신 onConfigurationChanged 콜백이 불리게 됩니다.

startActivityForResult => Deprecated

registerForActivityResult로 대체되었습니다.

```
val launcher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()){
    if(it.resultCode ==Activity.RESULT_OK){
      Log.d("Log",it.data)
    }
}
```

activity 태그 내에 테마를 통해 액티비티를 다이얼로그 테마로 적용할 수 있습니다.

```
android : theme = "@style/Theme.Appcompat.Dialog"
```

## Context?

- Context는 액티비티, 서비스, 애플리케이션의 상태를 나타내며 리소스에 접근할때도 사용합니다.

- 리눅스에서 프로세스를 ID로 관리하듯이, 안드로이드에서 액티비티를 Context로 관리하는 것과 비슷합니다.

- Context는 크게 ActivityContext, baseContext(Activity에서 들고있는 ContextImpl인스턴스), applicationContext가 있습니다.

- View또한 Context를 가지고 있습니다. 이것은 뷰가 속한 액티비티의 Context입니다.

## Permission

- 안드로이드에서 permission이 필요한경우, 사용자에게 권한 요청을 해야합니다. Manifest에 먼저 user-permission을 명시하고

아래와 같은 코드로 권한을 요청해야 합니다. 이때도 런처를 이용합니다

```
val launcher = registerForActivityResult(ActivityResultContracts.RequestPermission()){
isGranted -> 
//승인여부
if(isGranted)
  ...
}
else{
  ...
}
```

실행할때는 launcher.launch(Manifest.permission.CALL_PHONE)로 권한요청을 할 수 있습니다.

## PendingIntent

- 보류중인 Intent라는 뜻으로 다른 Application에서 나의 Applicationd의 프로세스에서 앱 컴포넌트들을 실행하는 것 처럼 intent를 wrapping하고있는 class입니다.

- 즉, Notification에서 특정한 작업을 수행할 때, 선언한 Intent가 실행될 수 있도록 하는 것입니다.

사용예시

```
val intent = Intent(this,MainActivity::class.java)
val pendingIntent = PendingIntent.getActivity(
  this, //Context
  requestCode, //PendingIntent의 ID
  intent, // 전달하는 Intent
  PendingIntent.FLAG_UPDATE_CURRENT
)
```
일반적으로 FLAG_UPDATE_CURRENT를 사용합니다.

## BroadcastReceiver

특정 이벤트가 발생했을 때, Android OS나 다른 앱 간에 메세지를 송, 수신받을수 있습니다.

이 메세지는 Intent객체에 이벤트 정보를 포함하여 전달합니다.

전달되는 Broadcast는 크게 시스템과 커스텀으로 나눌 수 있습니다.

시스템의 경우 충전기가 연결되거나, 부팅이 성공하는 등의 시스템 단에서의 메세지이고,

커스텀의 경우 앱에서 보내는 메세지로 앱 간의 정보를 교환하는 용도로 사용됩니다.

Broadcast 메세지를 보내는 방법은 크게 3가지가 존재합니다.

Ordered broadcast, Normal broadcast, Local broadcast가 있지만 안드로이드 프레임워크 단에서 사용하는 Ordered braodcast를 제외하고 살펴보면

Normal broadcast의 경우 정해진 순서 없이, 등록된 모든 Receiverd에 전달됩니다. 주로 사용하는 방법이며 Receiver는 결과를 다음 Receiver에게 전달하지도 방송을 중단할수도 없습니다.

Local broadcast의 경우 앱 내의 Receiver에게만 전달하는 방법이지만 현재 deprecated되었습니다.

BroadcastReceiver는 크게 2가지 방법으로 등록할 수 있습니다.

1.	Static Receiver로 AndroidManifest.xml에 직접 receiver 태그로 등록하는 방법이 있으며
2.	Dynamic Receiver로 코드 내에서 Context를 통해 BroadcastReceiver를 동적으로 등록하는 방식이 존재합니다.

하지만 Android 8.0부터 일반적으로 Dynamic Reveiver만을 사용해야 합니다.

Dynamic Receiver 구현방법

Receiver 생성

```

//Receiver 생성
Class CustomReceiver : BroadcastReceiver {
	
	Override fun onReceive(context : Context, intent : Intent){
		Var action = intent.getAction()
}
}

//Receiver 등록/해제
Class MainActivity : AppCompatActivity{
	Private car mReceiver = CustomReceiver()
	Var filter = IntentFilter(Intent.ACTION_POWER_DISCONNECTED)
	This.registerReceiver(mReceiver, filter)

	This.unregisterReceiver(mReceiver)
	
}
```
Broadcast는 중요한 정보가 외부에 노출되는 등 보안 취약점이 발생할 가능성이 있기때문에, 제한적으로 사용해야 합니다.

또한 BroadcastReceiver 내부에서 오래걸리는 작업을 수행해서는 안됩니다.



## Content Provider

Content Provider는 앱 내부의 데이터에 접근하고 데이터를 가져오는 역할을 하게 됩니다.

ContentProvider는 ContentResolver를 통해서 접근하며 API의 CRUD기능을 제공합니다.

getContentResolver().query(..)와 같은 형태로 Cursor가 반환되면, 데이터를 읽어올 수 있습니다.

## ConstraintLayout

- goneMargin -> 앞의 View가 사라져도 현재 마진값을 유지하고 싶을때 사용합니다.

- width, height가 모두 wrap_content일때, max_width, min_width, min_height, max_height를 설정할 수 있습니다.

- view의 가로,세로를 wrap_content로 적용할 때, 뷰가 화면 끝까지 차지하는 경우가 발생하면 다른 뷰들이 밀리게 됩니다.

- 이런경우를 대비하려면 constrainedWidth = true로 설정하면 됩니다.

- app:layout_constraintDimensionRatio = "16:9"와 같이 사용하면 가로, 세로 비율을 조정할 수 있습니다. 이 경우, width 또는 height에 최소 1개의 match_constraint가 있어야 합니다.

### ConstraintLayout CircularPositioning

기준이 되는 뷰로부터 각도와 반지름으로 특정 뷰의 위치를 정할 수 있습니다.

- 기준이 되는 뷰를 layout_constraintCircle = "@id/textView" 와 같이 설정하고

- layout_constraintCircleAngle= "45"와 같이 각도를 설정하고

- layout_constraintRadius = "100dp"와 같이 설정하면 됩니다.

### Chain

- ConstrainLayout내의 뷰들을 연결시키면 체인이 형성되게 됩니다.

- 체인 스타일을 따로 지정할 수 있으며 layout_constraintHorizontal_chainStyle = "스타일" 로 지정할 수 있습니다.

- 체인 스타일에는 default스타일인 spread와 spread_inside(양 측에서 간격을 없애고 각 뷰 간의 간격을 균등하게하는 것), pack(양 측면을 기준으로 view들이 가운데로 묶이게 되는 것) 등이 있습니다.

- LinearLayout처럼 weight로 비율을 설정하는 것도 가능합니다. 단 weight기준이라면 weight가 모두 0dp(match_constraint)를 사용해야 합니다.

- layout_constraintHorizontal_weight="1"과 같이 사용할 수 있습니다.

### bias

- constraintHorizontal_bias = "0.8"과 같이 좌측 여백의 비율도 설정 가능합니다. 값은 0.0~1.0 사이 또는 0~100까지의 값을 사용합니다.

- Chain에서 사용하려면, 헤드뷰에서 속성값을 적용시키면 사용할 수 있습니다.

### 가상 object

- GuideLine : 가이드라인으로 constraint의 대상을 가상으로 만들 수 있습니다.

- Barrier : 가이드라인과 비슷하지만 View를 기준으로 동적으로 라인설정이 가능합니다. 언어별로 뷰의 크기가 바뀌는 경우 사용할 수 있습니다.


### ConstraintSet

- 코드를 이용하여 constraint를 제어할 수 있습니다. 애니메이션을 만들 때 사용합니다.

# User Interaction

- UI디자인은 명확하고, 쉽고, 일관성이 있어야 합니다.

	- 어떻게 앱을 사용할지를 생각해야 합니다.
	
	- UI 요소들을 유저들이 쉽고, 간단하게 사용할 수 있게 개발해야합니다.
	
	- 한 화면에 하나의 목적으로 화면을 구성해야합니다.
	
	- 안드로이드의 Best practices를 따라야 합니다.
	

