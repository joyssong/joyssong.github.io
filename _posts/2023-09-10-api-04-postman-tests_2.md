---
title: "Postman - API Response 테스트(검증) 하기, Tests 이용 응용편"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화]
toc: true
date: 2023-09-10T03:00:00+09:00

---

<h2 id="소개">소개</h2>
<p>지난시간에는 Postman의 Tests 기능을 이용한 API 검증의 기본을 알아보았다.</p>
<p>이번에는 csv 파일의 데이터를 읽어오고 결과를 비교하는 등 응용편을 작성할 예정이다.</p>
<p><strong>테스트</strong> 를 하여 Pass, Fail을 결정 내리는 기준은 <mark>예상 결과와 실제 결과를 비교</mark> 하는 것이다.</p>
<p>즉, 우리는 “예상 결과” 와 “실제 결과” 가 필요하다.<br>
“실제 결과” 는 API response에서 얻을 수 있다.<br>
그렇다면 API Test에서 "예상 결과"는 어디서 얻어야 할까?</p>
<p>답은 csv 에 있다.</p>
<p>기본적으로 Postman 에서는 Javascript 로 코드를 작성하면 된다.</p>
<h2 id="api-reponse-와-csv-데이터-비교하기">1. API Reponse 와 CSV 데이터 비교하기</h2>
<h3 id="예상-결과-확인">1) 예상 결과 확인</h3>
<p>이번에도 PetStore 의 createUser API를 이용해보자.<br>
값을 정상적으로 넣으면 아래와 같이 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_07.png" alt="tests_07"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>"userStatus"에 Integer가 아닌 String을 넣으면 아래와 같이 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_08.png" alt="tests_08"></th>
</tr>
</thead>
<tbody></tbody>
</table><h3 id="csv에-테스트할-예상-결과-입력">2) csv에 테스트할 예상 결과 입력</h3>
<p>이 결과 중 “code” 와 “message” 를 테스트해보고자 한다.<br>
이 전 부터 사용하던 csv에 적당한 헤더를 넣고, 각 테스트케이스 별 예상결과를 넣어주면 된다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_09.png" alt="tests_09"></th>
</tr>
</thead>
<tbody></tbody>
</table><h3 id="postman에서-csv의-예상결과-불러오기">3) Postman에서 csv의 예상결과 불러오기</h3>
<p>Tests 에서 <code>pm.iterationData.get("csv의 헤더명")</code> 을 이용하여 csv 데이터를 불러올 수 있다.</p>
<p>아래와 같이 code_actual에는 실제 결과를, code_expected에는 예상 결과를 넣어주면 된다.</p>

```js
var  code_actual  =  pm.response.json().code;
var  code_expected  =  pm.iterationData.get("statusCode");
```
<p>이렇게 하면 테스트 준비 완료!</p>
<h3 id="pm.test-를-이용하여-테스트-하기">4) pm.test~ 를 이용하여 테스트 하기</h3>
<p>이 전 포스팅에서 익힌 pm.test를 이용해 예상결과/실제결과를 비교하면된다.</p>

```js
// Response body : JSON value check
pm.test("Response code is "+  code_expected, function () {
pm.expect(code_actual).to.eql(code_expected);
});
```
<h2 id="javascript-언어-이용-상황별-결과-검증하기">2. Javascript 언어 이용, 상황별 결과 검증하기</h2>
<p>위의 결과에서 보았듯이 API Response가 200 OK 일 때는 “message” 에 내가 입력한 id가 나오지만, 500 일 때는 에러 메시지가 출력된다.<br>
200 일때는 csv의 id값과 동일한 지 검증하고, 500 일때는 csv에 입력해둔 message와 동일한지 검증할 예정이다.</p>
<p>Postman 에서 기본적으로 제공하는 구문 외의 코드를 쓰고싶다면 Javascript를 이용하면 된다!<br>
흔히 아는 if, switch, for, list 정도면 테스트코드를 작성하는데 충분한다.</p>
<h3 id="status-code-기준으로-테스트-나누기--switch">1) Status Code 기준으로 테스트 나누기  (Switch)</h3>
<p>위에서 진행한 Status code 검증이 테스트를 분류하는 기준이 된다.<br>
이때, “실제 결과” 가 아닌 "예상 결과"를 기준으로 나눠야한다.</p>
<p>아래와 같이 작성한 후, 200에서는 id검증, 500에서는 에러메시지 검증 내용을 작성하면 된다.</p>

```js
switch (code_expected) {
	case (200) :
		break;
	
	case (500) :
		break;
}
```
<p>바로 이렇게!<br>
csv에서 예상결과를 가져와 변수에 넣어주고, API Response의 데이터와 비교하면된다.</p>
<p>이 때, id가 결과에는 String 타입이지만 csv에는 Integer 타입으로 적혀있기때문에 타입을 String으로 맞춰줘야한다.<br>
<code>JSON.stringfy({value})</code> 을 해주면 쉽게 String화 할 수 있다.</p>

```js
switch (code_expected) {
	case (200) :
		var  id_expected  =  JSON.stringfy(pm.iterationData.get("id"));
		pm.test("Verify created user id. "  +  id_expected,function () {
			var  id_actual  =  pm.response.json().message;
			pm.expect(id_actual).to.eql(id_expected);
		});
		break;
	
	case (500) :
		var  msg_expected  =  pm.iterationData.get("message");
		pm.test("Verify error message. "  +  msg_expected, function () {
			var  msg_actual  =  pm.response.json().message;
			pm.expect(msg_actual).to.eql(msg_expected);
		});
		break;
}
```
<p>이렇게 작성하고 Runner로 테스트를 돌리면 아래와 같이 결과가 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_10.png" alt="tests_10"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>200 일때는 생성된 id 검증, 500 일때는 에러 메시지를 검증하고자 하는 의도대로 진행되었다.</p>
<p>이렇게 Javascript의 Switch 를 이용하면 하나의 상황에 따라 원하는 테스트케이스만 진행할 수 있다.<br>
여기서는 편의상 200, 500 이렇게만 나누었지만 업무를 할때는 에러 부분을 조금 더 상세하게 나눈다.<br>
Validation Error, API Server Error, Null Error 등으로 나눌수도 있고 API 스펙에서 정의한 Error Type에 따라서도 나눌 수 있다.</p>
<h3 id="api-response-결과의-일부분만-검증하기">2) API Response 결과의 일부분만 검증하기</h3>
<p>간혹 에러메시지가 너무 길게 나오는 경우가 있다. 그럴때는 "예상 결과"가 “실제 결과” 에 <strong>포함</strong> 되는지 여부로 테스트를 진행한다.</p>
<p>그럴때는 아래와 같이 pm의 테스트 구문에 <code>pm.expect(actual).to.eql(expected)</code> 가 아니라  <code>pm.expect(actual).to.include(expected)</code>  를 사용하면 된다.</p>
<p>가운데를 eql 이 아니라 include 로 변경하면 된다.</p>

```js
var  actualErrorMsg  =  pm.response.json().message;
var  expectedErrorMsg  =  pm.iterationData.get("message")
pm.test("Verify Error Message. "  +  actualErrorMsg , function () {
	pm.expect(actualErrorMsg).to.include(expectedErrorMsg);
	});
```

