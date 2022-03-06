커스텀뷰 제작을 위해 ViewGroup 내에서의 터치 이벤트를 공식문서를 참고하여 학습하였습니다.

# ViewGroup에서의 터치이벤트 관리

ViewGroup에서의 터치 이벤트는 ViewGroup내의 다른 View들과 함께 존재하므로, 사용자의 의도대로 터치 이벤트를 처리하기 위해 내부 동작을 이해해야 합니다.

우선 각 뷰의 터치이벤트가 제대로 동작하기 위해서는 OnInterceptTouchEvent()를 이해해야 합니다.

그 전에 View에서 사용할 수 있는 이벤트 리스너들에 대해 정리하였습니다.

- View.OnClickListener / onClick()  : 사용자가 뷰를 터치하거나 탐색 키 또는 트랙볼을 사용하여 해당 항목에 포커스를 맞추고 적절한 Enter키, 또는 트랙볼을 누르면 호출됩니다.

- View.OnLongClickListener / onLongClick() : onClick과 비슷하지만, 약 1초간 길게 누르면 호출됩니다.

- View.OnFocusChangeListener / onFocusChange() : 사용자의 포커스가 탐색 키 또는 트랙볼을 이용하여 항목 쪽으로 이동하거나 멀어지면 호출됩니다.

  - 예시 : 이메일을 입력하는 edittext에 접근 또는 벗어날 때 문자열에 "@"가 존재하는 지 판별하는 등의 작업을 할 수 있습니다.

- View.onKeyListener / onKey() : 사용자가 항목에 포커스를 맞추고 있으면서 기기에 있는 하드웨어 키를 누르거나 키에서 손을 떼면 호출됩니다.

- View.onTouchListener / onTouch() : 누르기, 손떼기와 같은 화면에서 이루어지는 모든 움직임 동작이 포함됩니다.

  - onClick/onTouch? : 마우스의 경우를 예로 들면 onClick()은 마우스를 클릭했다가 떼어낼때 반응하고 onTouch()는 클릭할 때, 뗴어낼 때, 드래그할 때 등이 해당합니다.
  
  - onTouch()는 boolean을 리턴값으로 반환합니다. 만약 onTouch를 오버라이딩하여 true를 리턴한다면 시스템에 의해 전달되는 터치이벤트는 여기서 끊겠다는 의미입니다. 만약 false를 리턴한다면 정상적으로 이벤트가 전달됩니다.
  
  - 어떠한 뷰를 클릭했을때, onTouch -> onClick -> onLongClick(길게 클릭했을 경우에만) 순으로 전달됩니다. 길게클릭해도 onLongClick이벤트가 발생되지 않도록 하고싶다면 onClick에서 true를 리턴하면 됩니다.
  
또한 여러 이벤트를 처리하도록 구현되어 있는 몇가지 보편적인 콜백(핸들러)이 있습니다

- onKeyDown(int, KeyEvent) : 새로운 키 이벤트가 발생할 때 호출됩니다.

- onKeyUp(int, KeyEvent) : 키 업 이벤트가 발생할 때 호출됩니다.

- onTrackballEvent(MotionEvent) : 트랙볼 모션 이벤트가 발생할 때 호출됩니다.

- onTouchEvent(MotionEvent) : 터치스크린 모션이벤트가 발생할 때 호출됩니다.

- onFocusChanged(boolean, int, Rect) : 뷰가 포커스를 얻거나 잃을때 호출됩니다.

레이아웃 내에서 복잡한 이벤트를 관리하는 경우 사용하는 메소드도 존재합니다.

- Activity.dispatchTouchEvent(MotionEvent) : 모든 터치이벤트가 창에 전달되기 이전에 Activity가 가로채 이를 처리할 수 있습니다.

- ViewGroup.onInterceptTouchEvent(MotionEvent) : 터치이벤트가 뷰그룹의 하위 뷰로 전달될 때 해당 이벤트를 뷰그룹이 관찰할 수 있도록 합니다.

- ViewParent.requestDisallowInterceptTouchEvent(boolean) : onInterceptTouchEvent(MotionEvent)가 있는 터치 이벤트를 가로채면 안 된다고 나타내고자 할 때 상위 뷰에서 이 메소드를 호출합니다.

## OnInterceptTouchEvent()

이 메소드는 뷰그룹의 표면에서 터치이벤트가 감지될 때마다 호출됩니다. onInterceptTouchEvent()가 true를 반환하면, onTouch와 마찬가지로 MotionEvent가 가로채기 되어 하위 뷰에 터치이벤트가 전달되지 않습니다. 이 경우 하위뷰에 전달되어야 하는 터치이벤트들은 뷰그룹의 onTouchEvent()에 전달됩니다.

OnInterceptTouchEvent() 메소드는 뷰그룹의 상위뷰가 하위뷰보다 먼저 터치이벤트를 확인할 기회를 제공합니다.

만약 OnInterceptTouchEvent()에서 true를 반환하면 이전에 터치 이벤트를 처리하던 하위 뷰가 ACTION_CANCEL을 수신하고, 이 시점 이후로는 이벤트가 상위요소의 OnTouchEvent()메소드로 전달되어 일반적인 처리가 이루어집니다.

OnInterceptTouchEvent()는 false를 반환하고 이벤트가 뷰 계층 구조대로 하위 뷰로 이동하는 것을 지켜보는것만 하는것도 가능합니다.

