---
title:  "[Kotlin] 코틀린의 기초2"
excerpt: "함수, 조건문, 반복문에 대해서"
toc: true
toc_sticky: true
date: 2020-02-03T21:10:00+09:00

categories:
  - Kotlin
tags:
  - Kotlin
---

본 게시글은 코틀린을 공부하면서 정리해둔 게시글입니다.  
저번 개시글 [코틀린의 기초](https://solly29.github.io/kotlin/Kotlin-Step1/)에 이어서 **함수, 조건문, 반복문**에 대한 게시글입니다.  
코틀린 웹 컴파일러는 [여기](https://play.kotlinlang.org/)
## 조건문과 연산자
1. 연산자  
	연산자는 다른 프로그래밍 언어의 연산자와 똑같습니다.  
	**is**라는 연산자가 추가되었습니다.  
	a is Int 같이 사용하고 변수 a가 Int형이 호환이 되는지 확인 후 형변환까지 해줍니다.
	is의 반대로 **!is**도 있습니다.  
  
2. 조건문  
	조건문은 if와 **when**이 있습니다. when은 switch와 비슷하다고 생각하면 됩니다.
	when은 하나의 변수를 여러개의 값과 비교 할 수 있습니다.
	```kotlin
	var result = when(a){
        1 -> "정수이다."
        "hi" -> "문자열이다."
        is Int -> "정수형 이다."
        !is Int -> "정수형이 아니다."
        else -> "else임"
    }
	```
	여기서 when 앞에 변수를 적으면 해당하는 값을 반환해줍니다.  
	반환값이 필요없으면 앞에 변수를 안 적어도 됩니다.  
	그리고 when안에 구문은 한줄이 아니라 {}을 이용해 여러줄을 적을 수 있습니다.
	```kotlin
	when(a){
        "yes" -> {
			var str = "yes입니다."
			println(str)
		}
		"no" -> {
			var str = "no입니다."
			println(str)
		}
    }
	```
	  
## 반복문
반복문에는 **조건형**과 **범위형** 반복문이 있습니다.  
1. 조건형은 while, do while  
	while과 do while은 다른 언어와 똑같습니다.
2. 범위형은 for  
	for은 좀 자연어와 비슷하게 바뀌었습니다.
	```kotlin
	// 선언할때 자료형을 안적어도 된다. step은 증감연산자
	// 여기선 3씩 증가된다.
    for(i in 0..9 step 3){
        print(i)
    }
	// 9에서 0까지 감소, 여기에도 step을 쓸수 있다.
	// 여기선 -2씩 감소이다.
    for(i in 9 downTo 0 step 2){
        print(i)
    }
    //문자 char형도 가능하다.
    for(i in 'a'..'e'){
        print(i)
    }
	```
3. 흐름제어  
	흐름제어는 만약 중첨 반복문은 사용할때 break한번에 중첩 반복문을 나갈 수 있습니다.
	```kotlin
	loop2@for(i in 1..10){
        for(j in 1..10){
		    // 반복문을 만들때 레이블을 만들어서 
            // @레이블 이름을 쓰면 해당 레이블의 반복문까지 종료된다.
            if(i == 1 && j == 2) break@loop2
			// ""안에서 변수의 값을 출력하려면 $을 써야된다.
            println("i : $i, j : $j")
        }
	}
	```
	break 뿐만 아니라 continue도 가능합니다.  
	
## 함수
키워드 **fun**으로 선언합니다.
```kotlin
// 일반적인 함수 형식은 (파라미터들): 반환형
fun add(a: Int, b: Int, c:Int): Int{
	return a+b+c
}
// 만약 함수가 단순 계산용이라면 이렇게 해도된다.
fun add(a: Int, b: Int, c:Int) = a+b+c
```
만약 함수의 파라미터로 모든 객체를 받고 싶다면  
다른 언어의 Object와 같은 Any가 있습니다.  
```kotlin
fun anyTest(a: Any){
	println(a)
}
```
Any에서는 toString, equals, hasCode만 지원합니다.  
  
여기까지 코틀린의 기초 문법들을 알아봤습니다.  