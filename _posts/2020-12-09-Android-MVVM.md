---
title:  "[Android] MVVM 패턴이란?"
excerpt: "MVVM 패턴"
toc: true
toc_sticky: true
date: 2021-01-20T23:00:00+09:00
published : true

categories:
  - Android
  - Architecture
tags:
  - Android
  - MVVM
  - Kotlin
---

안드로이드에서 자주 쓰이는 디자인 패턴에 대해서 이야기 하는 시간을 가져보자.

웹에서 많이 쓰는 MVC와 같이 안드로이드도 이와 같은 디자인 패턴이 있다.  
안드로이드에서는 MVC, MVP, MVVM 패턴이 있고 보통 많이 쓰이는 패턴은 MVP, MVVM이다.

본 게시글에서는 **MVVM** 패턴에 대해서 알아보자.

# 자주 사용하는 기술

MVVM에 대해서 알아보기 전에 먼저 디자인 패턴에서 자주 쓰이는 기술 부터 한번 보자.
참고로 이 기술들을 쓰지 않아도 디자인 패턴을 구현 하는데는 아무 문제가 없다.  
이 기술들은 디자인 패턴을 보다 편하고 효율적으로 구현하기 위한 기술들이다.

디자인 패턴에서는 **LiveData, DataBinding, ViewModel, Android DI(Koin, Dagger2)**을 자주 사용한다. 하나씩 간단하게 알아보도록 하자

## LiveData
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

## DataBinding
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

## ViewModel

MVVM을 공부를 할때 블로그를 전전하며 공부를 했다. 블로그를 돌아다녀 보면 MVVM의 VM과 AAC(Android Architecture Component)의 ViewModel이 같다 라는 곳이 있고 다르다 라는 곳이 있다(보통은 다들 다르다고함). 결론은 VM과 ViewModel은 다르다. MVVM의 VM은 밑에서 이야기 하겠지만 View와 Model을 연결시켜 주고, Model에서 데이터를 가져와 저장하는 곳이다. 이때 액티비티는 LifeCycle이라는 것이 있고 액티비티가 완전히 종료될때까지 데이터를 저장해야 할때가 있다. 이때 ViewModel을 사용한다. ViewModel은 데이터 관리를 안전하게 할 수 있고 VM을 좀 더 쉽게 구현 할 수 있게 하는 기술이다.

![MVVM - https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm](/assets/images/viewmodel_lifecycle.png "ViewModel 생명주기"){: .align-center} 

위 사진에서 보시다시피 ViewModel은 액티비티의 LifeCycle안에서 finished가 되기전까지 살아있다. 이 덕분에 ViewModel이 안전하게 데이터를 관리 할 수 있다. 위 사진을 예로 들어보자면 어플은 가로, 세로화면을 지원하고 자동회전 기능이 켜져있으면 화면이 회전하게된다. 이때 액티비티는 화면을 그냥 회전 시키는 것이 아니라 아예 액티비티를 새로 그려주게된다. 이때 VM에 있는 데이터들이 저장이 되어야 되는데 ViewModel을 사용하면 데이터를 안전하게 지킬 수 있다.

## Android DI

여기에선 먼저 DI라는 것이 무엇인지 부터 보고 가자. **DI**는 **Dependency Injection**의 약자이고 **의존성 주입**이라고 한다. 의존성 주입을 코드로 간단하게 이해해보자.

```kotlin
class B(){
  val a = "test"
}

class A(val b: B){
  fun print() = println(b.a)
}

fun main(){
  val b = B()
  val a = A(b)
  a.print()
}
```

위 코드를 보면 A, B 클래스가 있고 A 클래스는 B 클래스에 의존성을 가지고 있고, main() 함수에서 B 객체를 생성해 A 객체를 생성할때 B 객체를 넘겨서(주입)해서 객체를 생성하고 있다. 이렇게 A클래스에서 B 객체를 직접 생성하지 않고 외부에서 **주입**해주는 것이 **의존성 주입(DI)**이다.

안드로이드에서는 의존성 주입을 도와주는 라이브러리가 있다. 대표적으로 **Dagger2, Koin** 가 있다. 여기서는 Koin에 대해서 알아보도록 하자. **Koin**은 순수 코틀린으로 개발이 되었고 Kotiln 환경에 쉽게 적용할 수 있고 어렵지 않아 쉽게 익힐 수 있다. 코드를 보며 이해를 하도록 하자.

```kotlin
class TestViewModel(repository: TestRepository): ViewModel(){}
class TestRepository(service: TestService){}
interface TestService {}
```
이렇게 클래스들이 있다고 가정하자. 이 클래스들에 의존성을 주입하기 위한 Module을 만들어 보자.

```kotlin
val testModule = module {
  viewModel { TestViewModel(get()) }
  single { TestRepository(get()) }
  single { TestService() }
  factory { TestClass() }
}
```
module안에는 viewModel, single 등등 있는데 하나씩 보면
- `viewModel{}`은 AAC의 ViewModel을 위한 것이고, viewModel 인스턴스를 생성해준다.
- `single{}`은 싱글톤 타입으로 지정한다.
- `factory{}`는 매번 새로운 인스턴스를 생성한다.
- `get()`은 module들 안에서 필요한 컴포넌트를 찾아서 의존성을 주입해준다.

module들을 사용하려면 Koin 초기화를 해야된다.

```kotlin
startKoin {
  // 먼저 안드로이드의 Context를 설정한다.
  androidContext(Context)

  // 만들어둔 Module들을 가져온다. 리스트 혹은 vararg 이다.
  modules(testModule)
}
```
보통 위의 코드는 `Application` 파일에 `onCreate()` 안에 작성한다. 앱을 실행하고 가장 먼저 실행이되도록 하게한다.

위의 기술들은 추후에 자세하게 정리할 예정이다.

# MVVM
안드로이드 디자인 패턴에 자주 쓰이는 기술들을 알아보았으니 MVVM에 대해서 알아보도록 하자.  
  
MVVM은 Model-View-ViewModel의 약자이다. 하나씩 보도록 하자.  
1. View - 일반적인 MVC, MVP와 같다. 안드로이드에서 화면에 표시되는 레이아웃을 말하며, 액티비티, 프래그먼트 등을 View라고 한다. View는 비즈니스 로직을 제외한 UI 로직을 수행해야된다. 
2. ViewModel - View와 Model을 연결한다고 생각하면된다. Model에서 데이터를 가져와 저장하고 View에서는 ViewModel의 데이터를 가져와 사용한다. Observer 패턴으로 View는 ViewModel을 관찰하고 있다가 데이터가 변경되면 UI를 갱신한다. 이때 보통 LiveData를 사용한다.
3. Model - Model은 Local 또는 Remote에서 데이터를 가지고온다. 여기서 비즈니스 로직을 수행하고 내부 디비(Room, Realm, SQLite 등)에 접근을 하거나 외부 서버에 통신(Retrofit, HttpUrlConnection 등)을 한다. Model에서는 UseCase, Repository 패턴 등이 있지만 이 부분은 추후에 정리할 예정이다.

![MVVM - https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm](/assets/images/mvvmImage.png "MVVM 구조"){: .align-center} 

MVVM 구조에서는 View는 ViewModel을 알지만 반대로 ViewModel은 View를 몰라야한다. 그래서 View가 ViewModel을 관찰하면서 UI를 갱신을  하게된다.

MVVM을 공부하면서 계속 MVP와 비슷하다는 생각이 들었다. 왜냐하면 MVP도 Presenter와 View가 Contract 인터페이스를 두고 1:1로 매칭이 되어있다. MVVM은 인터페이스는 없지만 View와 ViewModel이 1:1로 매칭이 되어 있는 형태를 가지고있어서 비슷하다는 생각이 들었다.

MVVM의 이론적인 부분은 여기까지하고 코드를 보며 이해를 해보자.