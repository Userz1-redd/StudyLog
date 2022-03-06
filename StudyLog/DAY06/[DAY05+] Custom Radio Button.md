커스텀 Radio Button을 만들기 위해서는

1.	RadioButton 생성: XML파일에 RadioButton생성
2.	Selector 생성및 적용 : 버튼이 체크되어 있을때와 해제되었을 때 버튼의 색/테두리/글자색/이미지 설정
작업만 하면 쉽게 커스텀 라디오 버튼을 생성할 수 있습니다.

라디오 버튼에 적용할 selector파일을 생성하여 아래와 같이 체크되었을때, 체크해제일때 각각 버튼의 색상, 테두리를 설정해줍니다.


## Selector_radio_btn.xml
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_checked="true">
        <shape android:shape="rectangle">
            <solid android:color="#000000" />
            <stroke android:width="1dp" android:color="#000000" />
        </shape>
    </item>
    <item android:state_checked="false">
        <shape android:shape="rectangle">
            <solid android:color="#FFFFFF" />
            <stroke android:width="1dp" android:color="#000000" />
        </shape>
    </item>
</selector>

```

이미지를 사용하고 싶다면 shape항목 대신 아래와 같이 item 항목을 사용하면 됩니다.

<item android:drawable="@drawable/이미지경로">

## selector_radio_text.xml

마찬가지로 체크되었을때와 해제되었을때의 텍스트 색깔을 지정합니다.

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="#FFFFFF" android:state_checked="true"></item>
    <item android:color="#00DE7A" android:state_checked="false"></item>
</selector>

```
### Radio버튼에 배경 및 텍스트 적용

라디오 버튼의 background 속성을 @drawable/selector_radio_btn으로 지정하고

텍스트를 @drawable/selector_radio_text로 지정하고

버튼의 동그라미를 제거하려면 button태그를 @null로 설정합니다.

예시코드
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    >
   <RadioGroup
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:orientation="horizontal"
       >

      <RadioButton
          android:id="@+id/radioButton"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:background="@drawable/selector_radio_btn"
          android:button="@null"
          android:checked="true"
          android:paddingLeft="30dp"
          android:paddingRight="30dp"
          android:text="First"
          android:textAlignment="center"
          android:textColor="@drawable/selector_radio_text" />

      <RadioButton
          android:id="@+id/radioButton2"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:background="@drawable/selector_radio_btn"
          android:button="@null"
          android:paddingLeft="15dp"
          android:paddingRight="15dp"
          android:text="Second"
          android:textAlignment="center"
          android:textColor="@drawable/selector_radio_text" />

      <RadioButton
          android:id="@+id/radioButton3"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:background="@drawable/selector_radio_btn"
          android:button="@null"
          android:paddingLeft="25dp"
          android:paddingRight="25dp"
          android:text="Third"
          android:textAlignment="center"
          android:textColor="@drawable/selector_radio_text" />


   </RadioGroup>
</RelativeLayout>

```
결과

<img width="188" alt="image" src="https://media.oss.navercorp.com/user/29230/files/994fa280-7153-11ec-9934-af6b62c01933">
