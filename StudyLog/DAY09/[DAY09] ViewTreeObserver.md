# ViewTreeObserver

ViewTreeObserver는 ViewTree에 대한 옵저버 역할을 합니다. 예를들어 어떤 뷰의 높이를 가져와야 하는 일이 생겼을때,

View.height를 사용해서 높이를 가져오려고 하면 0이 나오게 됩니다.

이는 뷰가 화면에 그려지기 이전에 높이를 가져오려고 하기 때문입니다.

ViewTreeObserver는 화면에 그려졌다는 알림을 받을 수 있습니다.

## 사용방법

- ViewTreeObserver는 View.getViewTreeObserver를 통해 가져올 수 있습니다. 아래는 뷰트리에서 사용할 수 있는 리스너 목록입니다.

- interface ViewTreeObserver.OnDrawListener 뷰를 그릴 때

- interface ViewTreeObserver.OnGlobalFocusChangeListener 전체 뷰의 포커스가 바뀔 때

- interface ViewTreeObserver.OnGlobalLayoutListener 전체 뷰가 그려질 때

- interface ViewTreeObserver.OnPreDrawListener 뷰가 그려지기 전

- interface ViewTreeObserver.OnScrollChangedListener 스크롤 상태의 변경시

- interface ViewTreeObserver.OnTouchModeChangeListener 터치 모드 변경시

- interface ViewTreeObserver.OnWindowAttachListener 뷰의 계층구조에 붙을 때와 떨어져 나갈때

- interface ViewTreeObserver.OnWindowFocusChangeListener 윈도우 포커스 변경시

## 주의사항

- 옵저버 리스너를 등록한 후 변경이 있을 때 1회만 사용하는 경우, 이후에 리스너를제거해야 합니다. 제거하지 않는 경우 무한 리스너 호출이 발생할 수 있습니다.
