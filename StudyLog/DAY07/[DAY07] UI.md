## ImageButton

- 이미지를 버튼으로 사용할 수 있는 버튼입니다. 버튼에 나타나는 이미지는 프로젝트 기본으로 보유한 "ic_launcher_foreground"를 사용합니다.

## 뷰의 포커스를 코드로 설정하기

- setFocusable()은 해당 뷰가 포커스를 가질 수 있게 설정합니다.

- requestFocus()는 특정한 뷰에게 포커스를 설정합니다.

- setOnFocusChangeListener()는 뷰가 포커스를 얻거나 잃는 상황에 대한 리스너입니다.

- onFocusChanged()는 뷰의 포커스가 변화되면 불리는 콜백메소드입니다.

## InputType

- InputType은 다양한 종류가 있습니다. 주요 InputType만을 정리하였습니다.

- 기본이 MultiLine이므로 한줄로 나타내려면 textLongMessage or textPersonName
또는 minline = "1"로 설정하면 됩니다.

- phone으로 설정 시 휴대폰 전화번호 입력 모양이 나타나게 됩니다.

## CheckBox

- 체크박스의 경우 CompoundButton을 상속받은 클래스입니다. 

- CompoundButton.OnCheckedChangeListener를 사용하여 체크를 처리할 수 있습니다.

## RadioGroup

- RadioGroup.onCheckedChangelistener를 사용하여 처리합니다.

## GestureDetector

- 스크롤 관련 콜백함수입니다. GestureDetector.OnGestureListener를 상속받고 GestureDetectorCompat(baseContext, this)로 객체를 생성합니다.

각 콜백이 어떤 것들을 하는지 아래와 같이 정리하였습니다.
```
public class GestureDetector{
    public interface OnGestureListener
    public interface OnDoubleTapListener
    public static class SimpleOnGestureListener implements OnGestureListener, OnDoubleTapListener
  
}

public interface OnGestureListener{
      //터치하려고 손을 대면 발생하는 이벤트
      boolean onDown(MotionEvent e);
      
      //짧은시간동안 누르면 발생하는 이벤트
      void onShowPress(MotionEvent e);
      
      //한 번 터치 했을 때 발생하는 이벤트
      boolean onSingleTapUp(MotionEvent e);
      
      //스크롤 시에 발생하는 이벤트
      boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY);
      
      //스크롤에서 끝을 살짝 튕기는 동작에서 발생하는 이벤트
      boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY);
}

public interface OnDoubleTapListener {
      // 한 번 터치하고 다음 이벤트가 돌아오지 않을때, 한번 터치가 확실함을 확인해주는 이벤트
      boolean onSingleTapConfirmed(MotionEvent e);
      
      //두 번 터치
      boolean onDoubleTap(MotionEvent e);
      
      //두 번 터치, 두 번째 터치에 Down, Move, Up 이벤트를 포함하는 경우
      boolean onDoubleTapEvent(MotionEvent e);
}
