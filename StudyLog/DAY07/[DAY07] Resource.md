# Resource

Resource id를 코드에서 정수로 다룰때에는,필수로 어노테이션을 사용하는 것이 좋습니다.

Context.getResource()를 통해 가져올 수 있으며 @LayoutRes, @AttrRes 등 을 필수로 사용해야합니다.

Inflater는 xml파일을 읽어서 약속된 구문으로 파싱하고 객체로 바꿔주는 역할을 합니다. 이에 대해 정확히 알고 있어야 리소스를 다루는데 생기는 문제들을 해결할 수 있습니다.

## 대체리소스

-	다양한 기기에 리소스를 보여주거나 다국어, 해상도별 이미지 크기를 따로 지정할 수 있습니다.
-	Res/<resources_name>의 뒤에 -<qualifier1>-<qualifier2> 등으로 사용하면 구현 가능합니다.

-	V26버전에서는 해상도에 상관없이 보여주고, 나머지는 해상도에 따라 제공한다면
Drawable-anydpi-v26 과 같이 리소스 폴더명을 지정하면 됩니다.


## Theme/Style

-	Style, Theme는 <style>태그를 사용하고 타입이 존재하지않습니다.

-	스타일, 테마는 구분없이 넣을 수 있고 컴파일도 정상적으로 되기때문에 혼동하기 쉽지만 구분해서 개념을 알 고 있어야합니다.

-	스타일은 단일 뷰 모양을 지정하는 속성입니다.

-	스타일은 하나의 특정 뷰 또는 뷰그룹에만 적용됩니다.

-	테마는 앱 전체, Activity, View계층에 적용되는 스타일입니다.

-	Status bar와 background같은 non-view에도 사용이 가능합니다.

-	스타일과 달리 하위뷰계층에 모두 적용됩니다.

-	액티비티에 적용하면 액티비티 내의 하위 계층에 모두 적용됩니다.

-	- 즉, Context에 따라 동작하며 Application Context로 사용하면 테마가 동작하지 않을 수 있기 때문에 가장 가까운 Context로 적용하는것이 좋습니다.

-	또한 테마는 중첩 적용이 가능하여 오버레이와 같은 형태로 사용할 수 있습니다.

-	?attr을 통해 사용할 수 있습니다.



# Resources 

Resouce를 가져오는 방법은 context.resources.getString(R.string.hello)
                         context.resources.getDrawable(R.drawable.world, theme)
                         context.resources.getColor(R.color.black, theme)와 같은 형태입니다.

대체리소스파일을 생성하는 법은 new resourcefile에서 생성할 수 있습니다. 되도록이면 IDE기능을 활용하는것이 좋습니다.

## Style, Theme만들어보기

우선 리소스에서, android:theme = "@style/xxx", style="@style/xxx"와 같은 형태입니다.

스타일은 단일 뷰에 속성을 지정하므로 아래와 같이 지정할 수 있습니다.


![image](https://user-images.githubusercontent.com/58676668/148951359-3a21b646-d311-4425-b38c-0ac2bf80a0b5.png)

레이아웃에서 Refactor를 활용하면 쉽게 따로 스타일로 분류할 수 있습니다.

![image](https://user-images.githubusercontent.com/58676668/148951776-4fde27d2-c54c-41dc-8bfc-7f3d9ae7fce6.png)

내부의 키는 뷰의 속성들을 지정할 수 있으며 값은 뷰와 마찬가지로 지정할 수 있습니다.

![image](https://user-images.githubusercontent.com/58676668/148951956-a77d0388-e446-45f3-9893-0dde909a2ed2.png)

style은 하위 뷰그룹에 적용되지 않습니다.

테마의 경우에는 백그라운드나 상태바와 같은 뷰가 아닌 것에도 적용이 가능합니다.

각 키값은 뷰의 값이 아니라 테마 attributes인 attrs.xml에 존재합니다.

테마는 ?attr을 이용하여 전역적으로 사용할 수 있습니다. 정의되어있다면 어디서든 사용 가능합니다.

## Color

공통된 컬러를 처리할 때 많이 사용합니다.

단, 테마내에 적용된 속성을 ?attr로 가져오는것은 불가능합니다.

![image](https://user-images.githubusercontent.com/58676668/148952876-923ac264-f4e0-4005-9c2d-66a65a073fa6.png)

단, 아래와 같이 attr을 사용하는 것은 불가능합니다.
![image](https://user-images.githubusercontent.com/58676668/148953063-f63e160e-97b0-4fbc-a123-aa2f66f70bc7.png)

## ColorStateList 색의 모음

버튼의 체크여부에 따라 색을 지정하고 싶다면, color디렉토리를 생성하고 colorstatelist를 사용하면 됩니다.

![image](https://user-images.githubusercontent.com/58676668/148953366-44ad2286-1576-425a-87d9-bd6f27e270da.png)

또한 알파값(투명도)를 바꾸려면 새로운 컬러를 정의해야 했지만 android:alpha로 조정이 가능합니다.

### 주의사항

만약 colorstatelist를 레이아웃 리소스에 background에 넣으면, 제대로 인식하지 못하므로 backgroundTint에 넣어야 합니다.



## 문자열 서식 지정

String리소스의 중간의 문자를 변경할 수 있습니다.

예시)
```
<string name = “welcome_messages”>Hello, %1$s! you have %2$d new messages.</string>
var text = getString(R.string.welcome_messages, username, mailCount)
```

### SpannableStringBuilder

코드상에서 TextView의 각 문자와 색, 크기, 스타일을 개별적으로 설정하도록 할 수 있습니다.

SpannableStringBuilder-ktx를 활용하여 쉽게 구현할 수 있습니다. 

RAINBOW라는 텍스트뷰에 각 글자에 무지개 색을 적용하고 글자 크기를 0.2배씩 늘리고 싶다면

```


SpannableStringBuilder(textView.text)
	.color(ContextCompat.getColor(Context, R.color.red){
		scale(1f){
	append(‘R’)
}
}
.color(ContextCompat.getColor(Context, R.color.orange){
		scale(1.2f){
	append(‘A’)
}
}

.color(ContextCompat.getColor(Context, R.color.yellow){
		scale(1.4f){
	append(‘I’)
}
}

.color(ContextCompat.getColor(Context, R.color.greed){
		scale(1.6f){
	append(‘N’)
}
}

.color(ContextCompat.getColor(Context, R.color.blue){
		scale(1.8f){
	append(‘B’))
}
}
.color(ContextCompat.getColor(Context, R.color.indigo){
		scale(2.0f){
	append(‘O’)
}
}
.color(ContextCompat.getColor(Context, R.color.purple){
		scale(2.2f){
	append(‘W’)
}
}
```


# Menu/Dialog/Toast

메뉴 : 화면의 옵션들의 모음을 메뉴라고합니다. 

메뉴에는 크게 옵션메뉴(appbar에 표시), 컨텍스트 메뉴(특정 아이템을 LongClick했을때 표시), 컨텍스처 액션 바, 팝업메뉴가 있습니다.

### 옵션메뉴

옵션메뉴를 추가할 때는 크게 4가지 과정으로 진행됩니다.
-	Menu xml 리소스 파일 생성 및 아이템 정의
-	액티비티의 onCreateOptionsMenu에서 menu아이템 inflate
-	Onoptionitemselected에서 menu선택을 핸들링합니다.
-	메뉴가 선택되었을 때 액션을 정의합니다.

아래는 메뉴리소스파일의 예시입니다.

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/action_settings"
        android:orderInCategory="1"
        android:title="Settings"
        app:showAsAction="never"
        />
</menu>

```
주요 속성은 크게 두가지가 있습니다.

-	android:orderInCategory : 앱바 내 아이템의 우선순위를 정할수있습니다. ex) 1, 2, 3
-	app:showAsAction
-	always : 앱 바에 항상 표시
-	ifRoom : 공간이 있으면 표시
-	never : overflow메뉴에 노출
-	withText : 텍스트와 함께 아이콘 노출


# Executor

Executor는 저수준 API인 Thread와 달리 Thread Pool을 형성하여 쓰레드를 관리할 수 있도록 도와주는 고수준 API입니다.

Executor인터페이스는 매우 간단하게 구성되어 있습니다.

```
public interface Executor{
void execute(Runnable command)
}
```

ThreadPool을 통한 이점은 스레드에 작업을 분배하는 것을 도와주며 생성되는 스레드의 개수를 통제할 수 있습니다. 또한 이미 생성된 스레드를 재사용할 수 있으므로 매번 스레드를 생성하는 것을 방지할 수 있습니다.

쓰레드풀에 대한 생명주기를 관리하는 기능이 추가된 Executor인 ExecutorService를 사용하는 것을 권장합니다.

