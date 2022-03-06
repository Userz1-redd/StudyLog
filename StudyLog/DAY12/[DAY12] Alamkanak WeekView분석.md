# Alamkanak WeekView

커스텀뷰를 제작해야 하는 상황에서 참고할만한 오픈소스를 발견하여 구현 방법 등을 개인적으로 분석한 문서입니다.

<img width="838" alt="image" src="https://open.oss.navercorp.com/storage/user/900/files/56dc4300-7882-11ec-8e08-fc064be3e1ec">

이 오픈소스는 View자체를 상속하여 아예 커스텀하게 모든것을 구현하였습니다. 뷰를 제어할 수 있는 여러 Attribute를 제공하고 클릭을 제어할 Interface를 제공합니다.

alamkanak week view 라이브러리의 attrs.xml

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="WeekView">
        <attr name="firstDayOfWeek" format="enum">
            <enum name="sunday" value="1"/>
            <enum name="monday" value="2"/>
            <enum name="tuesday" value="3"/>
            <enum name="wednesday" value="4"/>
            <enum name="thursday" value="5"/>
            <enum name="friday" value="6"/>
            <enum name="saturday" value="7"/>
        </attr>
        <attr name="hourHeight" format="dimension"/>
        <attr name="minHourHeight" format="dimension"/>
        <attr name="maxHourHeight" format="dimension"/>
        <attr name="textSize" format="dimension"/>
        <attr name="eventTextSize" format="dimension"/>
        <attr name="headerColumnPadding" format="dimension"/>
        <attr name="headerRowPadding" format="dimension"/>
        <attr name="columnGap" format="dimension"/>
        <attr name="headerColumnTextColor" format="color"/>
        <attr name="noOfVisibleDays" format="integer"/>
        <attr name="showFirstDayOfWeekFirst" format="boolean"/>
        <attr name="headerRowBackgroundColor" format="color"/>
        <attr name="dayBackgroundColor" format="color"/>
        <attr name="hourSeparatorColor" format="color"/>
        <attr name="todayBackgroundColor" format="color"/>
        <attr name="todayHeaderTextColor" format="color"/>
        <attr name="hourSeparatorHeight" format="dimension"/>
        <attr name="eventTextColor" format="color"/>
        <attr name="eventPadding" format="dimension"/>
        <attr name="headerColumnBackground" format="color"/>
        <attr name="dayNameLength" format="enum">
            <enum name="length_short" value="1"/>
            <enum name="length_long" value="2"/>
        </attr>
        <attr name="overlappingEventGap" format="dimension"/>
        <attr name="eventMarginVertical" format="dimension"/>
        <attr name="xScrollingSpeed" format="float"/>
        <attr name="eventCornerRadius" format="dimension"/>
        <attr name="showDistinctPastFutureColor" format="boolean"/>
        <attr name="showDistinctWeekendColor" format="boolean"/>
        <attr name="futureBackgroundColor" format="color"/>
        <attr name="pastBackgroundColor" format="color"/>
        <attr name="futureWeekendBackgroundColor" format="color"/>
        <attr name="pastWeekendBackgroundColor" format="color"/>
        <attr name="showNowLine" format="boolean"/>
        <attr name="nowLineColor" format="color"/>
        <attr name="nowLineThickness" format="dimension"/>
        <attr name="horizontalFlingEnabled" format="boolean"/>
        <attr name="verticalFlingEnabled" format="boolean"/>
        <attr name="allDayEventHeight" format="dimension"/>
        <attr name="scrollDuration" format="integer"/>
    </declare-styleable>
</resources>
```

여러 attribute를 정의해서 사용자가 직접 설정할 수 있도록 하였습니다.


또한 WeekView의 생성자 중 일부를 보면

<img width="892" alt="image" src="https://open.oss.navercorp.com/storage/user/900/files/2ea11400-7883-11ec-84d0-5458a0e96340">

각 애트리뷰트를
TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.WeekView, 0, 0);로 받고
각 애트리뷰트를 변수로 받아서 내부에서 설정하는 것을 볼 수 있습니다.


내부 코드를 보니 각 칸을 뷰로 사용하지 않고, paint를 이용하여 drawRect로 해당 일정의 영역을 그리고 그린 사각형의 터치 이벤트를 받아 이것을 리스너로 구현하는 등 매우 복잡하게 구현되어 있습니다.

참고용 자료로 사용하면 좋을것 같아 내부 코드를 보았습니다.
