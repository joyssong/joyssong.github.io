---
title: "Postman - 동적 변수 입력 : 오늘 날짜, Request Body에서 값 얻기 "
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화, newman, api테스트레포트]
toc: true
toc_sticky: true
date: 2023-09-13T03:00:00+09:00
---

## 소개

API를 호출하다보면 csv의 고정값을 입력하기도 하지만,

오늘 날짜를 넣거나 다른 api의 결과를 받아와서 입력할 일이 생기곤 한다.

이번 포스팅에서는 이렇게 고정되지않은 동적 변수를 입력하고 다른 API 호출에 활용하는 방법을 살펴본다.



## 1. id에 오늘 날짜 입력 - JavaScript Date 이용

이전과 동일하게 Swaager Petstore의 Create User API를 이용한다.

"id" 에 오늘 날짜와 이름을 넣어주고 싶다면 어떻게 해야할까?

csv에 오늘 날짜를 매번 넣어줄 수도 없으니, 이럴땐 **Javascript 의 Date함수**를 이용해야한다.


API 호출 전에 설정해야 하므로 **Postman의 Pre-request Script에 작성**해야한다.

단순히 `let today = new Date();` 로 입력하고 출력을 확인해보면,

`Wed Sep 13 2023 02:06:28 GMT+0900 (Korean Standard Time)` 이 출력된다.

현재 시간은 맞지맞 id에 넣기에 썩 보기 좋진않다.


**YYYYMMDD 형식으로 출력하려면 아래 함수를 이용**하면 된다.

``` js
// 날짜 input이 들어오면 YYYYMMDD 형식으로 출력
function dateFormatter(date) {

    year = date.getFullYear();
    // month는 1 더해줘야한다고 함 
    month = new String(date.getMonth()+1);
    day = new String(date.getDate());

    // M, D가 한 자리 수일경우 0을 채워줌
    if (month.length ==1) {
        month = "0" + month;
    }
    if (day.length ==1) {
        day = "0" + day;
    }
    // YYYY_MM_DD 형식으로 변환
    var resultDate = year + month + day;
    return resultDate;
}
```

별도의 function으로 작성한 이유는, 다른 곳에서도 활용하기 위함이다.


 `pm.variables.set("user_id", JSON.stringify(dateFormatter(new Date())))`
Pre-request Script에 위 내용까지 추가하고, 

Body의 "id"부분은 `"id": {{user_id}}` 이렇게 입력해준다.


그 결과, id에 "20230913" 과 같이 들어갈 것이다.

id를 조금 더 유니크한 값으로 주기 원한다면, 시/분/초를 추가해도 좋을것같다.


## 2. Response Body의 결과 데이터 확인

앞서 Tests 에서 수없이 했던 내용이다.

`pm.response.json().{key}` 을 이용해서 원하는 값을 가져올 수 있다.


## 3. Request Body의 입력값 받아오기

Create User API 수행할 때 "**username**"을 입력하는데, 이 변수를 이용해 Get User API를 수행할 수 있다.

Swagger Petstore 에서도 확인할 수 있다.
![postman_pre-test_01](/assets/images/postman_pre-test_01.png)

이미 Create User API 의 Request Body "**username**" 을 입력했으므로, 

이 값을 collection variable 혹은 global variable에 저장 후 Get User API에서 불러올 수 있다.


아래 코드로 Request Body에서 특정 Key에 해당하는 Value를 확인할 수 있다.
`JSON.parse(pm.request.body.raw).{key}`

Creaet User API의 **Tests** 에 아래를 입력한다.

```js
pm.collectionVariables.set("username_c",JSON.parse(pm.request.body.raw).username)
```

Collection > Variables에서 Collection Variable에 잘 들어갔는지 확인할 수 있다.
![postman_pre-test_02](/assets/images/postman_pre-test_02.png)



## 4. Collection Variable 을 url에 입력하기

이렇게 얻은 Collection Variable은 다양하게 활용할 수 있다.

다른 API의 url, request body 등 입력값으로도 쓸 수 있고,

response body의 결과값을 검증할 때 활용할 수 있다.


이름이 Collection Variable 이므로, 해당 Collection 내 에서만 유효한 변수이다.


컬렉션에 Get User API를 추가하고, url에서 username이 들어가는 부분에 {{username_c}} 를 입력하였다.

url에는 Environment Variable, Global Variable, Collection Variable 등을 {{ }} 을 통해 변수로 입력할 수 있다.

![postman_pre-test_03](/assets/images/postman_pre-test_03.png)


그 결과, Pre-reqest Script에 아무것도 써주지않고 url에 Collection Variable만 입력한다면 결과를 얻을 수 있다.


이렇게 Javascript 함수와 Collection Variable 등을 잘 활용하면 

중복된 코드를 줄이고 고생을 덜 하면서 API 스크립트를 작성할 수 있다.



