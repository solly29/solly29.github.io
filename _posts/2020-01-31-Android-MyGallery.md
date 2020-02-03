---
title:  "[Android] 프로바이더, 프래그먼트"
excerpt: "프로바이더, 프래그먼트"
toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Anko
  - Glide
  - kotlin
last_modified_at: 2020-02-03T13:04:00+09:00
---

이 게시글은 안드로이드를 공부하면서 생소한 것들을 정리해두었다.
## 프로바이더

```kotlin
//모든 사진 정보 가져오기
val cursor = contentResolver.query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI,//이미지를 가져올 경로(외부 저장소)
	null,       // 가져올 데이터 항목 배열
	null,        // 조건
	null,    // 조건
	MediaStore.Images.ImageColumns.DATE_TAKEN + " DESC")  // 찍은 날짜 순으로 내림차순
```
위의 코드로 안드로이드의 외부 저장소에 저장된 이미지 데이터를 가지고 온다

하지만 안드로이드에선 외부 저장소를 읽기 위해선 파일 권한이 필요하다.
```
<!--외부 저장소 읽기 권한 추가-->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```
코드를 매니페스트 파일에 추가하면 된다.

```kotlin
private val REQUEST_READ_EXTERNAL_STORAGE = 1000

override fun onCreate(savedInstanceState: Bundle?) {
	super.onCreate(savedInstanceState)
	setContentView(R.layout.activity_main)

	//권한이 부여됬는지 확인
	if(ContextCompat.checkSelfPermission(this, Manifest.permission.READ_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED){
		//권한이 허용되지 않음

		if(ActivityCompat.shouldShowRequestPermissionRationale(this, Manifest.permission.READ_EXTERNAL_STORAGE)){
			//이전에 이미 권한이거부되었을 때
			//이전에 거부가 되었으면 왜 권한이 필요한지 메시지를 띄우고 권한을 다시 요청한다.
			alert("사진 정보를 얻으려면 외부 저장소 권한이 필수로 필요합니다","권한이 필요한 이유"){
				yesButton {
					//권한 요청
					ActivityCompat.requestPermissions(this@MainActivity,
						arrayOf(Manifest.permission.READ_EXTERNAL_STORAGE),
						REQUEST_READ_EXTERNAL_STORAGE)
				}
				noButton {  }
			}.show()
		}else{
			//권한 요청
			ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.READ_EXTERNAL_STORAGE),REQUEST_READ_EXTERNAL_STORAGE)
		}
	}else{
		getAllPhotos()
	}

}
```
위의 코드는 사용자에게 권한을 요청하고 거부하면 다시 권한을 요청 하는 코드이다.


## 프래그먼트
* [프래그먼트](https://developer.android.com/guide/components/fragments)란 사용자 인터페이스의 모음이다.  
프래그먼트 여러 개를 조합하여 액티비티 하나를 구성할 수 있고 한 번 작성한 프래그먼트는 재사용할 수 있다.

* 프래그먼트 생명주기  
![프래그먼트생명주기](https://solly29.github.io/assets/images/fragment_lifecycle.png "프래그먼트 생명주기"){: .align-center}  

* 생명주기 설명  
	* onAttach() : 액티비티에 붙을 때 호출된다. 이때 부터 액티비티의 참조를 사용할 수 있다.  
	* onCreate() : 프래그먼트가 생성될 때 호출된다. 아직 레이아웃은 완성 전이다.  
	* onCreateView() : 레이아웃을 생성하기 전에 호출된다. 완성된 뷰를 반환, 아직 레이아웃 완성전이다  
	* onActivityCreated() : 액티비티의 onCreate() 매서드가 수행된 직후에 호출됨  
	* onStart() : 프래그먼트가 사용자에게 보여질 때 호출된다.  
	* onResume() : 사용자와 상호작용 시작  
	* onPause() : 프래그먼트가 일시 중지이거나 더 이상 사용자와 상호작용하지 않는다.  
	* onStop() : 프래그먼트가 중지됨  
	* onDestroyView() : 프래그먼트가 해당 자원을 정리할 수 있도록 한다.  
	* onDestroy() : 프래그먼트가 파괴될 때 호출함  
	* onDetach() : 프래그먼트가 액티비티에서 완전히 제거될 때 호출됨  


## viewPager
뷰페이저는 여러 프래그먼트들을 좌우로 슬라이드하는 뷰이다. 여기엔 세 가지 개념이 있다.  
* 데이터 : 프래그먼트(화면)  
* 어댑터 : 프래그먼트를 어느 화면에 표시할 것인지 관리하는 객체  
* 뷰 : 뷰페이저는  


## PagerAdapter
ViewPager에 표시할 내용을 정의하려면 어댑터가 필요하다.  
어댑터는 아이템의 목록 정보를 가진 객체이다. 종류로는  
* FragmentPagerAdapter : 페이지 내용이 영구적이고 페이지 갯수가 많지 않을때 적합하다.  
* FragmentStatePagerAdapter : 많은 수의 페이지가 있을때 적합하고, 적은 메모리를 차지한다.  


## Glide 라이브러리
[Glide](https://github.com/bumptech/glide)는 사진 로딩에 특화된 라이브러리이다.  
기존 이미지 표시하는 메서드 setImageURI()보다 이 라이브러리를 쓴 이유는  
* 미사용 리소스를 자동으로 해제  
* 메모리를 효율적으로 관리해준다.  
* 이미지를 비동기로 로딩하여 UI의 끊김이 없다.  
  
나머지 자세한 소스코드는 [여기](https://github.com/solly29/AndroidStudy/tree/master/MyGallery)에서 확인가능하다.