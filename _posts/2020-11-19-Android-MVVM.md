---
title:  "[Android] MVVM 패턴이란?"
excerpt: "MVVM 패턴"
toc: true
toc_sticky: true
date: 2020-11-19T23:00:00+09:00
published : true

categories:
  - Android
  - Architecture
tags:
  - Android
  - MVVM
---

안드로이드에서 자주 쓰이는 디자인 패턴에 대해서 이야기 하는 시간을 가져보자.

웹에서 많이 쓰는 MVC와 같이 안드로이드도 이와 같은 디자인 패턴이 있다.  
안드로이드에서는 MVC, MVP, MVVM 패턴이 있고 보통 많이 쓰이는 패턴은 MVP, MVVM이다.

본 게시글에서는 **MVVM** 패턴에 대해서 알아보자.

## 자주 사용하는 기술

MVVM에 대해서 알아보기 전에 먼저 디자인 패턴에서 자주 쓰이는 기술 부터 한번 보자.
참고로 이 기술들을 쓰지 않아도 디자인 패턴을 구현 하는데는 아무 문제가 없다.  
이 기술들은 디자인 패턴을 보다 편하고 효율적으로 구현하기 위한 기술들이다.

디자인 패턴에서는 **LiveData, DataBinding<!--, ViewModel, Android DI(Koin, Dagger2)-->**을 자주 사용한다.
하나씩 간단하게 알아보도록 하자

### LiveData
**LiveData는 안드로이드의 LifeCycle을 가지고있는 Data Type이다.**  LifeCycle을 가지고 있어서 LifeCycle 안에서만 동작을 한다. 그리고 Observer 객체를 사용해서 LiveData안에 Data가 변경되면 Observer 객체로 값을 업데이트할 수 있다. 업데이트는 LifeCycle이 활성상태일때만 데이터를 업데이트가 되지만 LiveData의 값은 변경이된다. 이럴경우 LifeCycle이 다시 활성상태가 되면 Observer 객체로 업데이트가 된다.
```kotlin
// String의 값을 가지는 LiveData이다.
val liveData = LiveData<String>("초기값은 생략해도 된다.")
// observer 객체로 업데이트
// LifeCycleOwner은 액티비티나 프래그먼트의 LifeCycleOwner를 적으면 된다.
liveData.observe(LifeCycleOwner, Observer{
	// 여기서 값이 변경되었을때 행동을 적으면 된다.
})
```
LiveData는 값이 변경되면 자동으로 값을 갱신해 주기 때문에 개발자가 직접 관리하지 않아도 되고, 이어서 나오는 DataBinding과 함께 쓰게되면 손쉽게 뷰의 값을 갱신할 수 있다.

### DataBinding
**DataBinding은 쉽게 말해서 xml에서 데이터를 바인딩해 사용할 수 있다.** 보통 view에 데이터를 넣을때는 java또는 kt파일에서 하는데, 이러한 작업들과 불필요한 코드들을 줄일 수 있다. 그래서 소스코드에 있는 데이터를 xml에서 바로 사용 가능하기 때문에 LiveData와 함께 쓰인다. LiveData와 함께 쓰게되면 Observer를 사용할 필요가 없고 Data가 변경시 바로 xml에 반영된다.
  
  
+ 추가로 BindingAdapter를 사용하면 뷰에 관련된 코드(리사이클러뷰, Glide, View 관련 로직 등)를 액티비티, 프래그먼트에서 분리 할 수 있다.

```xml
<layout>
   <data>
     <variable
       name="str"
       type="java.lang.String" />
   </data>

   <LinearLayout>
     <TextView
       ...
       android:text="@{str}"/>
     <EditText
       ...
       android:text="@={str}"/>
   </LinearLayout>
</layout>
```
DataBinding을 사용하려면 xml에서 ```<layout>, <data>, <variable>``` 태그를 사용한다.  여기서 잘 봐야되는 것은 **variable 태그**이다. 이 태그로 data를 사용할 수 있다. variable 태그에는 name, type 속성이 있는데 name은 xml파일 안에서 사용할 변수 이름이고, type는 그 변수의 dataType이다. dataType에는 패키지 이름을 모두 적어줘야한다.   
ex) java.lang.String, java.lang.Integer, com.example.test.TestData
  
이렇게 DataBinding을 사용하기 위한 세팅을 했으면 view에 데이터를 넣어야된다. 데이터를 view에 넣으려면 **"@{변수}"**를 사용하면 data를 view에 넣을 수 있다. 하지만 editText의 경우 값을 표시하면서 값을 변경했을때 바로 소스에 반영이 되야하는 경우가 있다. 이럴때는 **"@={변수}"**를 사용하면 editText에서 입력되는 데이터가 변경될 때 마다 소스코드에 데이터가 바로 변경된다.
<!--### ViewModel
이건 나중에 공부하고 하기로-->
<!--### Android DI --> 
  
위의 기술들은 추후에 자세하게 정리할 예정이다.

## MVVM
안드로이드 디자인 패턴에 자주 쓰이는 기술들을 알아보았으니 MVVM에 대해서 알아보도록 하자.  
  
MVVM은 Model-View-ViewModel의 약자이다. 하나씩 보도록 하자.  
1. View - 일반적인 MVC, MVP와 같다. 안드로이드에서 화면에 표시되는 레이아웃을 말하며, 액티비티, 프래그먼트 등을 View라고 한다. View는 비즈니스 로직을 제외한 UI 로직을 수행해야된다. 
2. ViewModel - View와 Model을 연결한다고 생각하면된다. Model에서 데이터를 가져와 저장하고 View에서는 ViewModel의 데이터를 가져와 사용한다. Observer 패턴으로 View는 ViewModel을 관찰하고 있다가 데이터가 변경되면 UI를 갱신한다. 이때 보통 LiveData를 사용한다.
3. Model - Model은 Local 또는 Remote에서 데이터를 가지고온다. 여기서 비즈니스 로직을 수행하고 내부 디비(Room, Realm, SQLite 등)에 접근을 하거나 외부 서버에 통신(Retrofit, HttpUrlConnection 등)을 한다. Model에서는 UseCase, Repository 패턴 등이 있지만 이 부분은 추후에 정리할 예정이다.

![MVVM - https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm](https://solly29.github.io/assets/images/mvvmImage.png "MVVM 구조"){: .align-center}