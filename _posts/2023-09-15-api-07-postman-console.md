---
title: "Postman - Console 이용 방법: 콘솔에 데이터 출력, Request, Response 확인하기"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화, newman,]
toc: true
toc_sticky: true
date: 2023-09-15T03:00:00+09:00
---

## 소개

일반적으로 코드를 작성하다보면 완성하기 전 결과를 확인하고싶을 때가 있다.  
Java에서는 System.out.print.in(~) 주로 이 구문을 이용해 콘솔(혹은 터미널 등등)에 중간 결과를 출력한다.

그렇다면, Postman 에서도 동일한 기능이 있을까?  
있다! 바로 **Console** 을 이용하는 것이다.  



## Postman에서 Console 실행 방법

Postsman에서 콘솔을 켜는 방법은 2가지 이다. 

1) 내부에 실행 - 왼쪽 하단에 Console 버튼 클릭  
2) 외부에 실행 - 상단 View > Show Postman Console 클릭

내부에서 실행하면 아래와 같이 보인다.  
![postman_console_01](/assets/images/postman_console_01.png)

Request, Response와 함께 보여서 한 눈에 볼 수 있지만,   
노트북에서는 한 화면에서 다 보기에 약간 부족하다.

외부에서 실행하면 아래와 같이 별도의 창에서 보인다.   
![postman_console_02](/assets/images/postman_console_02.png)

## Console 출력 방법 & Console 커맨드 종류

이제 원하는 내용을 출력해보자!

**Pre-request Script** 나 **Tests** 에 입력하면 출력시킬 수 있다. 

*console.* 을 입력하면 아래와 같이 추가 가능한 옵션들이 나온다.   
![postman_console_03](/assets/images/postman_console_03.png)

이 중 하나를 선택하고, ( ) 안에 String을 넣어주면 된다.  
`console.log("console 출력")` 이런 식이다.

Postman에서 제공하는 옵션들을 해본 결과!  
![postman_console_04](/assets/images/postman_console_04.png)

`console.log` 는 아무런 마크없이 평범하게 출력되지만,   
`consloe.info` `console.warn` `console.error`는 각각의 마크를 달고 출력된다. 

모두가 예상했다시피 상황에 맞게 적당한 구문을 선택해서 사용하면 된다.

## Console 출력 - API Request 전? 후?

**Pre-request Script** 는 API Request 전,  **Tests** 는 API Request 후 실행된다.

콘솔도 마찬가지로 필요한 상황에 따라 입력하면 된다.

* API Request 전, 무조건 출력하고 싶다면 > **Pre-request Script** 에 입력 
* API Request 후, 결과에 따라 출력하고 싶다면 >  **Tests** 에 입력



아래 이미지와 같이 ` > POST http:// ~` 전/후로 콘솔로 입력한 값이 출력되는 것을 알 수 있다.  
![postman_console_04](/assets/images/postman_console_04.png)

## Console 활용 방법

API 테스트 자동화를 수행할 때, 수십~수백개의 호출을 날리므로 콘솔을 통해 각 tc를 구분해준다.

**Pre-request Script** 에는 수행할 TC 내용을 적어주고,   
**Tests** 에는 수행한 결과를 바로 확인할 수 있는 주요 결과를 적어준다.   
![postman_console_05](/assets/images/postman_console_05.png)

보통 Runner로 수행하면 테스트 결과만 나오므로,   
어떤 내용의 테스트를 수행했는지 어떤 결과가 나왔는지 확인하기 어렵다.   
적절하게 Console을 활용하면 원하는 값을 쉽게 출력할 수 있다.

## Console로 Request, Response 상세 확인하기

보통 Reponse는 Postman 내에서 바로 볼 수 있지만,   
실제 호출한 Request Body는 보기 어렵다.

콘솔에서 `> POST http:// ~`을 클릭하면 상세 내용이 펼쳐진다.   
실제로 API를 호출한 url, network, Request/Response heaser, Request/Reponse Body 등을 보여준다.   
![postman_console_06](/assets/images/postman_console_06.png)

여기서 `> Request Body`를 클릭하면, 상세 내용이 펼쳐진다.  
![postman_console_07](/assets/images/postman_console_07.png)

현재는 Request가 짧아서 작은 화면으로도 충분하지만,   
Request가 길~다면 오른쪽 화살표를 클릭하면 단독 화면으로 볼 수 있다.  
![postman_console_08](/assets/images/postman_console_08.png)

분명이 올바른게 작성했는데 당췌 왜 Fail이 발생하는가!  
할 때,  콘솔의 Request Body를 꼼꼼히 살펴보면 대개 답이 나온다.   
String으로 들어가야하는게 그냥 들어갔다던가, 변수처리한게 제대로 안들어갔다던가 등등의 이유가 많다.



쉬어가는 코너로(벌써?) 알아본 Postman의 Console 활용법.  
정말 쉽지만 제대로 알아두면 API 테스트가 정말 편해진다!