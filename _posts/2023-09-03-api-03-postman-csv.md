---
title: "Postman - csv 와 Runner를 이용한 API 테스트 자동화"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화]
toc: true
date: 2023-09-03T22:30:00+09:00

---

<h2 id="소개">소개</h2>
<p>보통 API 테스트라하면 하나의 uri에 parameter 혹은 변수만 조금씩 바꿔서 결과를 확인하는 것이다.<br>
그러면 테스트할 파라메터개수 (=tc개수) 만큼 request (postman에서 파일 단위)가 필요할까?</p>
<p>전혀 그렇지 않다!</p>
<p>csv 파일을 이용하면 하나의 파일로 수천번의 request를 다 다른 파라메터(변수)로 날릴 수 있다.</p>
<p>Postman을 사용하면서 이를 모르는 개발자, QA도 생각보다 많았다.<br>
앞으로는 csv를 활용해보자.</p>
<h2 id="단계---파라메터의-변수화">1단계 - 파라메터의 변수화</h2>
<p><a href="https://petstore.swagger.io/#/">Swagger Petstore</a> 의 POST /user 을 예로 들었다.</p>
<p>1명의 user를 등록하기 위해서는 body에 아래와 같이 입력해야한다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_01.png" alt="csv_01"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>만약 수십명의 user를 등록해야한다면, 이렇게 하나 입력하고 send하고, 또 하나 입력하고 send 하고를 반복해야할까?</p>
<p>그렇지않다.</p>
<p><mark>Body 에서 변수처리하고 싶은 부분을 {{  }} 로 감싸고 변수명을 입력한다.</mark></p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_02.png" alt="csv_02"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>원래의 key값과 동일하게 하면 편하다.</p>
<h2 id="단계---csv에-데이터-입력">2단계 - csv에 데이터 입력</h2>
<p>사용하는 컴퓨터에 엑셀이 없어서 구글 스프레드시트에서 생성 후 csv로 다운로드를 하였다.</p>
<p><mark>헤더에 해당하는 행에 Postman의 Body에 적었던 변수명을 입력하고, 그 아래에는 입력할 데이터를 넣으면 된다.</mark><br>
<strong>데이터를 넣을 때, String이라면 “” 로 감싸줘야 한다.</strong></p>
<p>맨 왼쪽에 num 은 데이터 개수를 세기 위해 일부러 넣은 값이다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_03.png" alt="csv_03"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>이렇게 csv와 Postman의 Body에 동일하게 이름을 넣으면 별다른 사전조건없이도 알아서 1열부터 데이터를 읽어온다.</p>
<h3 id="여기서-잠깐-csv-데이터에서--를-안-넣고싶다면"><strong>여기서 잠깐, csv 데이터에서 " 를 안 넣고싶다면?</strong></h3>
<p>데이터를  " 로 감싸는게 귀찮다면, 아래 방법이 있다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_06.png" alt="csv_06"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>위 이미지에서는 username을 " 로 감싸지않고 데이터만 입력하였다.<br>
(단, csv는 , (쉼표) 로 데이터를 구분하니 데이터에 , 가 포함되면 안된다)</p>
<p>Postman으로 돌아와 Body 옆의 <strong>Pre-request Script</strong>로 이동한다.<br>
여기서 아래와 같이 입력한다.</p>
<pre><code>pm.variables.set("username", JSON.stringify(pm.iterationData.get("username")))
</code></pre>
<p>나중에 Pre-request Script 및 pm.xxx에 대해 별도로 포스팅할 예정이다.</p>
<p><code>pm</code> 은 postman 의 약자로 postman에서만 쓰이는 명령어이다.<br>
보통 Body, Params 에 들어가는 변수를 variables로 칭한다.</p>
<p><code>pm.variables.set("A", B)</code> 는 Body 에서 {{A}} 로 입력한 변수에 B데이터를 넣겠다는 뜻이다.</p>
<p><code>pm.iterations.get("C")</code> 는 이터레이션을 수행할 csv에서 C 라는 이름을 가진 열의 데이터를 불러온다.</p>
<p>이 때, <code>JSON.stringfy</code> 를 해줘야 String 형식으로 불러오므로 에러가 나지 않는다.</p>
<h2 id="단계---runner로-수행">3단계 - Runner로 수행</h2>
<p>Body 자체로는 어디서 데이터를 읽어와야하는지 모르므로 수행할 수 없다.<br>
Postman 하단에 보면 Runner, Start Proxy, Cookies 등 버튼이 있는데 여기서 <mark>Runner 을 클릭</mark>한다.</p>
<p><strong>Runner</strong> 는 여러 request를 한 번에 수행하게 해주는 기능이다.<br>
(최근 유료화가 강해지면서 Runner을 하루에 25회만 돌릴 수 있게 바뀌었다.)</p>
<p>Runner에 진입 후, drag and drop으로 원하는 collection (폴더 단위) 를 불러오고 체크/언체크를 해준다.</p>
<p>지금은 1개만 돌리지만, 실제 테스트를 할때는 위에 기입한 것과 같이 여러 API를 돌릴때가 더 많다.</p>
<p>오른쪽의 Run Configuration을 보자.</p>
<p>먼저 <mark>Data &gt; Select File로 아까 생성한 csv를 선택</mark>한다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_04.png" alt="csv_04"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>파일을 선택하면 Iterations에 수행할 개수가 저절로 나온다.<br>
이게 testcase 의 개수가 된다.<br>
만약 전체가 아닌 일부를 돌리고 싶다면 원하는 개수만큼 바꾸면 된다.</p>
<p>Data File Type 도 text/csv로 자동 선택되고, 옆에 Preview 버튼을 통해 데이터가 어떻게 생겼는지 볼 수 있다.</p>
<p>이후 <mark>Run SwaggerPetstore 을 클릭</mark>하면 된다.<br>
<strong>주의! Run 하기 전 Request 파일들을 모두 저장해주어야 한다.</strong></p>
<p>아래와 같이 결과가 나온다!</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_csv_05.png" alt="csv_05"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>5개의 iteration을 돌렸고, 아직 본격적으로 test를 한 것은 없으므로 All tests 는 0으로 나온다.<br>
대신, 모든 항목이 200ok 로 나왔음을 알 수 있다.</p>
<h2 id="csv--runner-조합-활용법">csv + Runner 조합 활용법</h2>
<p>이렇게 csv와 Runner을 이용하면, 하나의 API Request + 하나의 csv파일로 수천개의 응답을 만들어 낼 수 있다.</p>
<p>예를 들면 CRUD 기본 동작을 이용해 Backend Regression Tet를 할 수 있다.  아이템을 생성하고(POST), 조회하고(GET), 업데이트(PUT) 후 삭제(DELETE)하는 4개의 API를 준비한다.</p>
<p>아이템 생성시에는 각 변수마다 true / false / null 값을 넣고 Response 을 확인한다.</p>
<p>false (혹은 null) 일 때는 Postman의 Tests를 이용해 응답 코드 및 에러 메시지를 확인한다.<br>
true 일 때는 200ok 를 확인하고 Reponse의 데이터를 가지고 이후에 조회, 업데이트 삭제에 활용한다.</p>
<p>필자는 CRUD API를 이용해 회사의 도메인 일부에 대해 테스트케이스를 생성하였고, iteration 개수는 1만개를 넘어간다. Postman 에서 직접 수행하지는 않고 Jenkins에 올려서 수행하는데 결과보고서까지 20분이면 충분하다.</p>
<p>세팅은 힘들었지만 이 자동화를 통해 2년 가까이 사용하고 있으며 크리티컬한 이슈도 많이 발견할 수 있었다.</p>

