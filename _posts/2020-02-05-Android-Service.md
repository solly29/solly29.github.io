---
title:  "[미완][Android] 서비스 정리"
excerpt: "서비스란?"
toc: true
toc_sticky: true
date: 2020-02-05T22:00:00+09:00

categories:
  - Android
tags:
  - Android
---

본 게시글은 안드로이드를 공부하면서 정리해둔 게시글이다.  
그날 배운 내용을 정리하는 목적으로 적었다.  
책에 서비스의 내용이 많지 않아서 구글링 후 추가 수정할 예정이다.   

## 서비스
서비스는 화면이 없고 백그라운드에서 수행하는 작업을 하는 컴포넌트이다.  
서비스는 두개로 나뉘어 진다.  
**Bound Service** - 연결을 유지하며 데이터를 주고받을수 있다.  
**UnBound Service** - 시작과 종료에만 관여한다.  

## 서비스 생명주기
가장 먼저 다른 액티비티나 컴포넌트에서 startService() 메소드로 서비스를 생성하게 된다.  
1. 서비스가 생성이 되면 onCreate()가 호출이되고  
2. 서비스에서 수행 할 작업은 onStartCommand()에서 적으면 된다.  
	이 메소드는 반환값이 있는데 3가지가 있다.  
	start_sticky : null 인텐트로 다시 시작한다. 명령을 실행하지는 않지만 무기한으로 실행중이다.  
	start_not_sticky : 다시 시작하지 않는다.  
	start_redeliver_intent : 마지막에 실행된 인텐트로 다시시작한다.  
	이부분에 대해서는 좀더 찾아봐야 된다.
3. 서비스 안에서 stopSelf()를 호출하거나 stopService()로 서비스를 종료하면 호출한다.  

작업을 한 코드는 [손전등 예제](https://github.com/solly29/AndroidStudy/tree/master/Flashlight)에 있다.  