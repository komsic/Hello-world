# SuperCalendar
## ChangeLog
* Release 1.6 stable version
* 发布1.5 修改了一些BUG
* 发布1.4稳定版
* 清除了dev污染
* 添加手动切换上一月下一月功能
* 解决神奇的日历尺寸问题，确保日历尺寸正确
## Introduction
* [Possibly the tenth best Android open source calendar] (http://www.jianshu.com/p/8e3fc46e5a80)
* Bloggers are now working in an education company. Recently, the company’s product dog has thrown over a demand, saying that it is possible to do a weekly schedule that can be switched over to show the user’s class schedule on a particular day. After receiving this task I studied a lot of calendar controls and pulled out a calenderlib. Take a look at the effects in the final project:

<div style = "float:center">
    <img src="http://osnftsiae.bkt.clouddn.com/syllabus_1.png" width="240">
    <img src="http://osnftsiae.bkt.clouddn.com/syllabus_2.png" width="240">
<div/>
<div style = "float:center">
    <img src="http://osnftsiae.bkt.clouddn.com/supercalendarexample.gif" width="240">
<div/>

* The classmates who saw this article are also expected to need a calendar sheet for the experimental class or project requirements. When I received this request, I didn’t even think about my mind at the time. With such universal controls, GitHub had a lot of searches. Is not it. However, when it is really done, the old iron is tied, and the god of GitHub is abnormally unappealing. They all realize the basic functions. It is difficult to switch the month and find an open source library that implements the function of switching between months and months. When I finally tried my best to find a project that could be switched perfectly, the data after your switchover from week to week was a mess! ! !
* Do it yourself! ! !

## 项目链接 [SuperCalendar][Tags]

[Tags]: https://github.com/MagicMashRoom/SuperCalendar

* If you feel helpful to you, welcome to star
* If you feel confused about the code, or need to modify the place, welcome issue

## Key Features
* Calendar style completely customized, strong expansion
* Swipe left and right to switch between upper and lower week months, and slide up and down to switch to week month mode
* Drawer week month switching effect
* Mark the specified marker
* Jump to the specified date

## ideas

## Instructions

#### XML layout
* Create a new XML layout

RecyclerView of layout_behavior for com.ldf.calendar.behavior.RecyclerViewBehavior

```xml
 <android.support.design.widget.CoordinatorLayout
        android:id="@+id/content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1">

        <com.ldf.calendar.view.MonthPager
            android:id="@+id/calendar_view"
            android:layout_width="match_parent"
            android:layout_height="300dp"
            android:background="#fff">
        </com.ldf.calendar.view.MonthPager>

        <android.support.v7.widget.RecyclerView
            android:id="@+id/list"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_behavior="com.ldf.calendar.behavior.RecyclerViewBehavior"
            android:background="#c2c2c2"
            android:layout_gravity="bottom"/>

    </android.support.design.widget.CoordinatorLayout>
    
```
#### Custom Calendar Styles
* New CustomDayView inherits from DayView and overrides refreshContent and copy

```java
	@Override
    public void refreshContent() {
        //Your code You can define your display rules here
        super.refreshContent();
    }

    @Override
    public IDayRenderer copy() {
        return new CustomDayView(context , layoutResource);
    }
```

* Create a new CustomDayView instance and build a CalendarViewAdapter as a parameter


```java
	CustomDayView customDayView = new CustomDayView(context, R.layout.custom_day);
        calendarAdapter = new CalendarViewAdapter(
                context,
                onSelectDateListener,
                CalendarAttr.CalendarType.MONTH,
                CalendarAttr.WeekArrayType.Monday,
                customDayView);
```

#### Initialize View


* At present, compared to the Dialog select calendar My control is more suitable for Activity / Fragment In the Activity's `onCreate` Or Fragment `onCreateView` You need to implement these two methods to start the calendar and load the data

```java
@Override
   protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_syllabus);
        initCalendarView();
    }
    
    private void initCalendarView() {
        initListener();
        CustomDayView customDayView = new CustomDayView(context, R.layout.custom_day);
        calendarAdapter = new CalendarViewAdapter(
                context,
                onSelectDateListener,
                CalendarAttr.CalendarType.MONTH,
                CalendarAttr.WeekArrayType.Monday,
                customDayView);
        initMarkData();
        initMonthPager();
    } 
```


Use this method to call back a calendar click event
```java
private void initListener() {
        onSelectDateListener = new OnSelectDateListener() {
            @Override
            public void onSelectDate(CalendarDate date) {
                //your code
            }

            @Override
            public void onSelectOtherMonth(int offset) {
                //offset -1 for the previous month, 1 for the next month
                monthPager.selectOtherMonth(offset);
            }
        };
    }
```
 
 
Use this method to initialize calendar tag data
```java
private void initMarkData() {
       HashMap markData = new HashMap<>();
        //1 means red dot, 0 means gray dot
       markData.put("2017-8-9" , "1");
       markData.put("2017-7-9" , "0");
       markData.put("2017-6-9" , "1");
       markData.put("2017-6-10" , "0");
       calendarAdapter.setMarkData(markData);
   }
```
 Use this method to add related listeners to MonthPager
```java
monthPager.addOnPageChangeListener(new MonthPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
            }

            @Override
            public void onPageSelected(int position) {
                mCurrentPage = position;
                currentCalendars = calendarAdapter.getAllItems();
                if(currentCalendars.get(position % currentCalendars.size()) instanceof Calendar){
                    //you code
                }
            }

            @Override
            public void onPageScrollStateChanged(int state) {
            }
        });
```

Override onWindowFocusChanged method, use this method to know the calendar and day size

```java
	@Override
    public void onWindowFocusChanged(boolean hasFocus) {
        super.onWindowFocusChanged(hasFocus);
        if(hasFocus && !initiated) {
            CalendarDate today = new CalendarDate();
        	calendarAdapter.notifyDataChanged(today);
            initiated = true;
        }
    }
```


* You're done, if you're not sure, download DEMO

## Download
--------
Gradle:
Step 1. Add it in your root build.gradle at the end of repositories:
```groovy
allprojects {
    repositories {
	...
	maven { url 'https://www.jitpack.io' }
    }
}
```
Step 2. Add the dependency

```groovy
	dependencies {
	        compile 'com.github.MagicMashRoom:SuperCalendar:1.6'
	}

```
[![](https://www.jitpack.io/v/MagicMashRoom/SuperCalendar.svg)](https://www.jitpack.io/#MagicMashRoom/SuperCalendar)

## Licence

      Copyright 2017 MagicMashRoom, Inc.