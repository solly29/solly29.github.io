---
title:  "[Android] Android Architecture"
excerpt: "안드로이드 아키텍처 MVC, MVP, MVVM 등등"
toc: true
toc_sticky: true
date: 2021-02-23T22:00:00+09:00
published : true

categories:
  - Android
  - Architecture
tags:
  - Android
  - Kotlin
---

이번에는 안드로이드에서 자주 사용하는 아키텍처입니다.

프로그래밍에서 아키텍처는 중요하다고 생각합니다. 특히 다른 개발자와 협업을 하거나 유지보수를 할때 그 중요성을 느끼게 되는데요
학교 프로젝트에서 서로 다른 코딩 스타일을 가진 사람들이 모여 개발을 할때..  
또는 회사에서 프로젝트 유지보수 할때 정해진 아키텍처가 없으면 좀 힘들어요...

그래서 안드로이드 개발자인 만큼 안드로이드의 아키텍처에 대해서 알아보고 이 다음에는 **Clean Architecture**에 대해서 포스팅 하도록 하겠습니다.


# Android Architecture
**일단 Android에서 아키텍처가 왜 필요할까요?** Android는 크게봐서 UI, 비즈니스 로직이 모두 포함이 되어 하나의 앱이 만들어집니다. 여기서 UI는 온전한 디자인 영역, 비즈니스 로직은 간단히 말해서 UI를 제외한 보이지 않는 부분에서 처리되는 로직을 의미합니다. 예를들어 서버에 통신을 한다거나, 내부 디비의 데이터를 가져오거나, 가져온 데이터를 가공하는 부분 등을 말합니다.
<br/>
<br/>
Android에서는 이런 UI나 비즈니스 로직이 모두 Activity, Fragement에서 작성되고 관리를 합니다. 이렇게 할 경우 간단한 앱이면 상관이 없겠지만 큰 프로젝트를 하게된다면 하나의 Activity Class에 1000줄, 2000줄 심하면 그 이상의 코드가 작성되어 있을겁니다..  
이 프로젝트에서 나중에 기능을 수정하거나 해야된다면 어떻게 될까요..? 또는 이런 프로젝트를 인수인계를 받고 유지보수를 해야된다면..? 저는 정말 화가 많이 날 것 같아요..
<br/>
<br/>
저는 이런 상황들 때문에 Android에서 아키텍처가 필요하다고 생각해요. 아키텍처는 개발 시간이 조금 단축되고 개발이 편리해지며, 유지보수가 쉬우며, 코드의 가독성이 좋아집니다. 그리고 개발을 할 때 필수적인 Test Code를 작성을 할 때 단위 테스트에 용이합니다. 그럼 이제 Android에서 아키텍처를 적용하기 위한 Android Architecture Pattern에 대해서 알아보도록 하죠.


# Android Architecture Pattern
**Android Architecture Pattern**의 종류에 대해서 알아볼게요. 

아키텍처 패턴에는  
1. MVC
2. MVP
3. MVVM
4. MVI
5. SVC
6. 기타 등등

이렇게 있어요. 여기서 제가 아는 것은 **MVC, MVP, MVVM** 정도에요. 나머지 SVC, MVI, 기타 등등은 추후에 공부 할 예정입니다...ㅎ

이제 하나씩 알아보도록 하겠습니다. 여기서는 **MVP**와 **MVVM**을 중점으로 설명하겠습니다.


## MVC
MVC는 많이 들어보셨을겁니다. MVC는 **Model-View-Controller**의 약자입니다. 보통 웹에서 MVC 패턴을 많이 사용하죠. Android에서도 MVC를 적용할 수 있어요.

일단 Model-View-Controller에 대해서 간략하게 설명하겠습니다.  
- View : 일반적으로 화면에 보이는 부분을 말합니다. 보통 Android에서는 레이아웃, 액티비티, 프래그먼트 등을 View라고 해요. View는 Model이나 Controller를 몰라야됩니다.
- Model : Model은 Local 또는 Remote에서 데이터를 가지고오는 친구에요. 여기서 비즈니스 로직을 수행하고 내부 디비(Room, Realm, SQLite 등)에 접근을 하거나 외부 서버에 통신(Retrofit, HttpUrlConnection 등)을 합니다. Model에서는 UseCase, Repository 패턴 등이 있지만 이 부분은 추후에 정리할 예정입니다. Model은 View나 Controller에 대한 정보를 알면 안됩니다.
- Controller : 사용자의 Event를 받으며 그에 따라 UI 로직을 처리하거나 Model에게 데이터를 요청하는 부분입니다. 이 친구는 View와 Model을 모두 알고 있으며 Model에서 데이터를 받아 View에 보여주는 역할을 합니다.

하지만 Android에서는 MVC를 적용하기가 애매한 부분이 있어요. 바로 View와 Controller의 구분이에요.

먼저 웹 MVC를 볼게요.

<figure style="display:block; text-align:center;">
  <img src="https://solly29.github.io/assets/images/MVCPatternWeb.png"
       style="margin:0px auto">
  <figcaption style="text-align:center; font-size:15px; color:#808080">
    웹 MVC 동작 방식이에요
  </figcaption>
</figure>

웹은 사용자 요청이 들어오면 바로 Controller으로 가게되고 위의 역할대로 수행한 뒤 View에 데이터를 전달 후 응답하게 됩니다.
  
Android 같은 경우는 View에서 사용자 Event 요청을 받고 응답하고, 처리하게됩니다. 이 때문에 Android에서 MVC를 적용하기가 애매하고 Controller가 특별히 하는일이 없으면 MV(Model-View)의 형태로 만들기도 합니다. 

> 만약 Controller를 구현한다면 View에서 받은 Event를 Controller으로 넘겨 Controller에서 처리하면 됩니다.

이런 MVC에도 장점과 단점이 있습니다.

### 장점
1. View와 Model이 분리되며 Model의 비종속성으로 Model 재사용이 가능합니다.
2. 구현이 가장 쉽고 간단합니다.

### 단점
MVC 같은 경우 장점보다 단점 부분이 더 많은데요..
1. Model과 View사이에 의존성이 발생합니다.
2. 점점 Controller의 코드가 비대화되어서 유지보수가 힘듭니다.
3. Controller가 Context에 종속되어서 Unit Test가 어렵습니다.


이렇게 Android에는 MVC가 애매모호하고 적절하지 못하다고 생각해서 단점들을 보완해서 등장한것이 MVP입니다.


## MVP
MVP는 제가 아키텍처 패턴을 알게되고 가장 처음으로 프로젝트에 적용한 패턴입니다.  
MVP는 **Model-View-Presenter**의 약자입니다. MVC의 Model, View 부분은 같고 Controller가 Presenter으로 변경되었습니다.

Model-View-Presenter에서 하나씩 살펴보면
- View : MVC의 View와 같습니다. MVP에서 조금 다른점은 View는 온전히 화면에 보여주는 역할과 Presenter에 데이터를 요청하는 역할만 합니다..
- Model : MVC의 Model과 같습니다. 이후에 나오는 MVVM도 똑같습니다.
- Presenter : 간단하게 View와 Model 사이의 통로, 연결해주는 부분? 이라고 보면됩니다. View에 데이터 요청을 받고 Model에서 데이터를 받아 가공 후 View를 업데이트합니다. View와 Presenter는 1:1 매칭됩니다.

이제 MVP의 동작 방식을 보겠습니다.

<figure style="display:block; text-align:center;">
  <img src="https://solly29.github.io/assets/images/MVP.png"
       style="margin:0px auto">
  <figcaption style="text-align:center; font-size:15px; color:#808080">
    MVP 동작 방식
  </figcaption>
</figure>

MVP의 동작 방식은
1. View에서 User Event가 발생
2. View는 Presenter에 User Event 전달 또는 데이터 요청
3. Presenter는 Model에 데이터 요청 또는 수정
4. Presenter에서 CallBack으로 Model에서 변경된 데이터를 받음
5. Model에서 받은 데이터를 가공해서 View에 UI 업데이트 요청
6. View UI 업데이트

이러한 동작 방식을 가지고 있어서 MVVM을 공부하기 전에는 가장 Android에 적합한 아키텍처 패턴이라고 생각했습니다.

아키텍처 패턴 같은 경우는 이론 보다는 코드를 보며 설명 하는것이 가장 좋기 때문에 추후에 코드로 설명하겠습니다.

이제 MVP의 단점과 장점을 알아보겠습니다.

### 장점
1. View와 Model사이에 의존성이 없습니다.
2. Presenter에 Context를 가지지않아서 단위 테스트를 할 수 있습니다.

### 단점
1. View와 Presenter가 Interface를 가지며 1:1 매칭이 되어서 Interface가 많아집니다.
2. View와 Presenter사이에 의존성이 높아집니다.
3. View에서 하는 일이 많아질수록 Presenter가 비대해집니다.
4. View에 RecyclerView의 어댑터를 사용한다면 View와 Presenter의 구조가 복잡해집니다.

이렇게 MVP에 대해서 알아보았고 이것을 좀더 보완한 MVVM이 등장합니다.

## MVVM
요즘 많이 사용하고있는 아키텍처 패턴인 MVVM입니다.
  
MVVM은 Model-View-ViewModel의 약자입니다. 하나씩 보도록 하죠.  
1. View - MVP와 같습니다.
2. ViewModel - View와 Model을 연결한다고 생각하면되요. Model에서 데이터를 가져와 저장하고 View에서는 ViewModel의 데이터를 가져와 사용합니다. Observer 패턴으로 View는 ViewModel을 관찰하고 있다가 데이터가 변경되면 UI를 갱신하는데, 이때 보통 LiveData를 사용합니다.
3. Model - MVP와 같습니다.

<figure style="display:block; text-align:center;">
  <img src="https://solly29.github.io/assets/images/mvvmImage.png"
       style="margin:0px auto">
  <figcaption style="text-align:center; font-size:15px; color:#808080">
    MVVM - https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm
  </figcaption>
</figure>

MVVM의 동작 방식을 알아볼게요.
1. View에 Event가 발생
2. Event가 DataBinding 또는 Listener를 통해서 ViewModel에 전달
3. ViewModel은 Model에 데이터 요청
4. ViewModel은 CallBack으로 데이터를 수신
5. ViewModel은 받은 데이터를 가공해 저장
6. View는 ViewModel의 데이터를 관찰해(Observe) UI 갱신

이런 동작 방식을 가지고있습니다.

**MVVM 구조에서는 View는 ViewModel을 알지만 반대로 ViewModel은 View를 알면안되요.** 그래서 View가 ViewModel을 관찰하면서 UI를 갱신을 하게됩니다.

MVVM의 단점과 장점을 알아볼까요?

### 장점
1. View와 Model, View와 ViewModel의 의존성이 없습니다.
2. 각 부분 모듈화를 할 수 있고, 단위 테스트, Unit Test 등 테스트가 가능합니다.

### 단점
1. AAC와 같은 기술을 사용해야 그나마 쉽게 구현 할 수 있습니다.
2. AAC도 그렇고 MVVM를 설계하는 것이 어렵습니다.. 

어렵지만 MVVM을 공부하면서 계속 MVP와 비슷하다는 생각이 들었어요. 왜냐하면 MVP도 Presenter와 View가 Contract 인터페이스를 두고 1:1로 매칭이 되어있습니다. MVVM은 인터페이스는 없지만 View와 ViewModel이 1:1로 매칭이 되어 있는 형태를 가지고있어서 비슷하다는 생각이 들었어요.

이렇게 MVC, MVP, MVVM 세개의 아키텍처 패턴을 알아보았습니다. 지금 생각해보면 안드로이드에 가장 적합한 패턴은 MVVM인거 같습니다. MVI, SVC는 공부해보겠습니다.