---
title: "Postman - API Response 테스트(검증) 하기, Tests 이용 기본편"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화, api테스트, api응답테스트]
toc: true
date: 2023-09-05T01:00:00+09:00

---

<h2 id="소개">소개</h2>
<p>Postman 으로 API를 호출하면 API Response 라고 해서 호출 결과가 나온다.</p>
<p>보통은 ‘Response’ 라고 부른다.<br>
이 Reponse는 응답코드(http 상태코드) 와 Body로 구성되어 있다.</p>
<p>API 테스트를 하기 위해서 HTTP 상태코드(혹은 HTTP 응답코드) 는 미리 공부해두자. 초간단하게 200이면 OK, 400이면 클라 에러, 500이면 서버 에러로 구분한다.</p>
<p><strong>RestAPI의 경우</strong>, <mark>API Response에서 가장 먼저 확인해야할 건 바로 이 ‘응답코드’</mark> 부분이다. 원하는대로 200ok 가 나왔는지 혹은 원하는대로 에러코드 (400, 500 등) 이 나왔는지 확인 후 Body를 검증한다.</p>
<p><mark>두번째는 Response Body의 데이터</mark>이다.<br>
API 설계시 응답에 담기도록 정해둔 데이터가 출력된다. Body의 데이터를 호출하는 입장에서 선택하거나 설정할 수 없고 정해진 결과를 받아야만 한다.</p>
<p><strong>GraphQL의 경우</strong>, (별도의 설정이 없다면) 대부분 200 OK로 떨어지기에 상태코드의 큰 의미는 없다. 하지만 Response Body 에서 어떤 데이터를 받을 것인지 미리 정할 수 있다는게 RestAPI와 차이가 있다.</p>
<p>어쨌든 둘 다 <strong>상태코드와 Response Body 의 데이터를 검증</strong>해야하는데 이는 <strong>Postman의 Tests 라는 기능으로 쉽게 검증</strong>할 수 있다.</p>
<h2 id="단계---postman-에서-response-body-확인">1단계 - Postman 에서 Response Body 확인</h2>
<p>API 를 호출하고 나면 아래와 같이 HTTP 상태코드, Response Body가 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_01.png" alt="tests_01"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>이제 상단의 Tests로 이동해서 API 응답을 검증해볼 차례다.</p>
<h2 id="단계---http-상태코드-검증">2단계 - HTTP 상태코드 검증</h2>
<p>Tests 로 이동하면 오른쪽에 Snippets 이 보이는데, 사실 여기만 잘 이용하면 코드를 몰라도 대부분의 것을 텍스트할 수 있다.</p>
<h3 id="하나의-응답코드-확인하기">1) 하나의 응답코드 확인하기</h3>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_02.png" alt="tests_02"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>여기서 <code>Status code : Code is 200</code> 을 선택하면, Tests 에 자동으로 아래 코드가 입력된다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript">  pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Status code is 200"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span>to<span class="token punctuation">.</span>have<span class="token punctuation">.</span><span class="token function">status</span><span class="token punctuation">(</span><span class="token number">200</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>pm. 은 postman의 약자이다.<br>
pm.test 를 하면 테스트 결과가 Pass/Fail로 나오게되는데 아래 구성으로 되어있다.</p>
<pre><code>pm.test( "결과에 노출할 테스트 케이스", function() { "테스트할 내용" });
</code></pre>
<p>사실 이 구조의 반복이다.</p>
<p>위 코드에서는 <code>pm.response.to.have.status(200)</code>을 통해 status 가 200 인지 확인한다. 200 말고 원하는 응답코드로 변경하여 테스트도 가능하다.</p>
<p>하지만 성공 케이스라도 항상 200 으로 나오는 건 아니다. 간혹 201, 202로 나오기도 하기 때문이다.</p>
<p>그럴때는 아래 방법을 이용하자.</p>
<h3 id="여러개의-응답코드-확인하기">2) 여러개의 응답코드 확인하기</h3>
<p>Snippets에서 <code>Status code : Successful POST request</code>을 클릭하자.</p>
<p>아래의 코드가 추가된다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"> pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Successful POST request"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
   pm<span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span>code<span class="token punctuation">)</span><span class="token punctuation">.</span>to<span class="token punctuation">.</span>be<span class="token punctuation">.</span><span class="token function">oneOf</span><span class="token punctuation">(</span><span class="token punctuation">[</span><span class="token number">201</span><span class="token punctuation">,</span> <span class="token number">202</span><span class="token punctuation">]</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
   <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>pm.test 로 시작하는 구성은 아까와 동일하다.<br>
테스트할 내용이 아까와 달라지고, [201, 202] 라는 배열이 추가되었다. 이 배열에 200을 추가하면 1)의 테스트까지 커버할 수 있다.</p>
<p>이렇게 두개의 테스트케이스를 추가하고 호출해보면, 아래와 같이 결과가 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_03.png" alt="tests_03"></th>
</tr>
</thead>
<tbody></tbody>
</table><h2 id="단계---api-response-body-데이터-검증하기">3단계 - API Response Body 데이터 검증하기</h2>
<p>이제는 Body를 검증할 차례이다. 역시 Snippets 을 적극 활용할 예정이다.</p>
<p>위에서 CreateUser 를 호출하고 아래의 Body 를 받았다.</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
<span class="token string">"code"</span><span class="token punctuation">:</span> <span class="token number">200</span><span class="token punctuation">,</span>
<span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"unknown"</span><span class="token punctuation">,</span>
<span class="token string">"message"</span><span class="token punctuation">:</span> <span class="token string">"200910"</span>
<span class="token punctuation">}</span>
</code></pre>
<p>여러 방법이 있겠지만 가장 쉽고 간단한 3가지 방법을 소개하려한다.</p>
<ol>
<li>Response Body : Contain String</li>
<li>Response Body : JSON Value Check</li>
<li>
<ol start="3">
<li>Response Body : Is equal to a string</li>
</ol>
</li>
</ol>
<p>아래 이미지는 미리보기!</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_04.png" alt="tests_04"></th>
</tr>
</thead>
<tbody></tbody>
</table><h3 id="response-body--contain-string">1) Response Body : Contain String</h3>
<p>응답의 어디든 특정 String이 있으면 Pass를 낸다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">// Body 검증 1 - Contain String</span>
pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Verify contain 'unknown'"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  pm<span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span><span class="token function">text</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">.</span>to<span class="token punctuation">.</span><span class="token function">include</span><span class="token punctuation">(</span><span class="token string">"unknown"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>여기서는 Response body에 “unknown” 이란 단어가 있다면 Pass가 된다.</p>
<p>데이터의 위치를 정확히 파악하기 어려울 때, 혹은 데이터의 위치가 유동적일 때 사용하면 좋겠다.</p>
<h3 id="response-body--json-value-check">2) Response Body : JSON Value Check</h3>
<p>개인적으로 제일 많이 사용하는 구문이다.</p>
<p>API 결과는 거의 항상 JSON 데이터로 나오는데, map 처럼 key : value로 구성되어 있다.<br>
그래서 데이터의 key값에 원하는 value가 나왔는지 정확하게 확인할 수 있기에 제일 많이 사용한다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">// Body 검증 2 - JSON Value Check</span>
pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Verify created id"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">var</span>  id_result  <span class="token operator">=</span>  pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span>message<span class="token punctuation">;</span>
  pm<span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>id_result<span class="token punctuation">)</span><span class="token punctuation">.</span>to<span class="token punctuation">.</span><span class="token function">eql</span><span class="token punctuation">(</span><span class="token string">"200919"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p><code>pm.response.json().~~</code> 을 이용하면 원하는 데이터를 정확하게 불러올 수 있다.<br>
위 응답에서는 “message” 라는 key의 value를 얻어오기 위해 <code>pm.response.json().message</code> 의 값을 <code>id_result</code> 에 넣어주었다.</p>
<p>이후 <code>pm.expect(id_result).to.eql("200919")</code> 구문으로 <code>id_result</code> (결과값)이 “200919” (예상값)과 동일한지 비교한다.</p>
<p>하지만 언제나 항상 id가 200919 일리는 없다.</p>
<p>API 호출에 따라 동적으로 변하는 데이터를 검증하기 위해서는 여러가지 방법이 있는데, 그 중 csv에 예상값을 입력해두고 Tests에서 가져오면 매우 편리하다.<br>
<code>pm.expect(id_result).to.eql(pm.iterationData.get("id"))</code><br>
이 방법은 다음 포스팅에서 좀 더 상세히 작성 할 예정이다.</p>
<p>혹은 collection variable, environment variable, glolbal variable 등과 비교할 수도 있다.</p>
<p><strong>Pre-request Script</strong> 에 아래와 같이 미리 collection variable을 설정한다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">let</span>  user_id  <span class="token operator">=</span>  <span class="token string">"200919"</span>
pm<span class="token punctuation">.</span>collectionVariables<span class="token punctuation">.</span><span class="token keyword">set</span><span class="token punctuation">(</span><span class="token string">"user_id"</span><span class="token punctuation">,</span> user_id<span class="token punctuation">)</span>
</code></pre>
<p>이후 <strong>Tests</strong> 에서 아래와 같이 비교할 수 있다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">// Body 검증 2 - JSON Value Check 응용. Pre-request Script 변수와 비교</span>
pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Verify created id - collection variable"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">var</span>  id_result  <span class="token operator">=</span>  pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span><span class="token function">json</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span>message<span class="token punctuation">;</span>
  pm<span class="token punctuation">.</span><span class="token function">expect</span><span class="token punctuation">(</span>id_result<span class="token punctuation">)</span><span class="token punctuation">.</span>to<span class="token punctuation">.</span><span class="token function">eql</span><span class="token punctuation">(</span>pm<span class="token punctuation">.</span>collectionVariables<span class="token punctuation">.</span><span class="token keyword">get</span><span class="token punctuation">(</span><span class="token string">"user_id"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>이렇게 하면 Tests는 고정시키고, Pre-request Script만 바꾸면서 테스트를 할 수 있다.</p>
<p>사실 업무에선 응답이 위 처럼 단순하지 않은 경우가 더 많다. 그런 경우를 대비해 아래에 <strong>다양한 Response Body  구조에서 데이터 읽어오기</strong> 를 적어두었다.</p>
<h3 id="response-body--is-equal-to-a-string">3) Response Body : Is equal to a string</h3>
<p>위와 다르게 응답을 아예 통째로 검증하는 방법이다. 응답이 단순한 경우에는 유용하다.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">let</span>  body_expected  <span class="token operator">=</span> <span class="token punctuation">{</span>
  <span class="token string">"code"</span><span class="token punctuation">:</span> <span class="token number">200</span><span class="token punctuation">,</span>
  <span class="token string">"type"</span><span class="token punctuation">:</span> <span class="token string">"unknown"</span><span class="token punctuation">,</span>
  <span class="token string">"message"</span><span class="token punctuation">:</span> <span class="token string">"200919"</span>
<span class="token punctuation">}</span>

<span class="token comment">// Body 검증 3 - Is equal to a string</span>
pm<span class="token punctuation">.</span><span class="token function">test</span><span class="token punctuation">(</span><span class="token string">"Body has '200'"</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
  pm<span class="token punctuation">.</span>response<span class="token punctuation">.</span>to<span class="token punctuation">.</span>have<span class="token punctuation">.</span><span class="token function">body</span><span class="token punctuation">(</span>body_expected<span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Body를 통째로 검증하므로 미리 예상값(body_expected) 를 선언해주는게 코드가 깔끔하다.</p>
<p>사실 업무에서 써본적이 거의 없는 구문이다.</p>
<p>이 밖에도 다양한 Snippets이 있다. 응답속도 검증, 헤더검증 등등!<br>
내가 업무에서 쓰는것은 응답코드 확인(1) 2) 둘 다)과 Body 확인시는 2)를 많이 사용한다.</p>
<h2 id="단계---테스트-결과-확인하기">4단계 - 테스트 결과 확인하기</h2>
<p>이렇게 총 6개의 테스트케이스를 생성해보았다.<br>
수행하고 나면 아래와 같이 보인다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_05.png" alt="tests_05"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>Save as Example을 통해 결과 저장이 가능하긴하지만, QA가 추구하는 보존가능한 테스트 결과라고 하기에는 아쉬움이 있다. 나중에 포스팅하겠지만 html-extra, allure report 등을 이용해 html 형식의 Test Report도 만들 수 있다.</p>
<p>Pass만 보여주면 아쉬우니 Fail 이미지도 올려본다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_tests_06.png" alt="tests_06"></th>
</tr>
</thead>
<tbody></tbody>
</table><h2 id="tips-다양한-response-body--구조에서-데이터-읽어오기">Tips!) 다양한 Response Body  구조에서 데이터 읽어오기</h2>
<p>위의 경우 응답이 단순하지만, 단계로 구성된 경우는 상위 key 값을 이어서 적어주면 된다. 예를 들어 아래의 구조에서 message를 얻고싶다면 <code>pm.response.json().error.message</code> 이렇게 적으면 된다.</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"error"</span> <span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"code"</span> <span class="token punctuation">:</span> <span class="token string">"error-01"</span><span class="token punctuation">,</span>
    <span class="token string">"message"</span> <span class="token punctuation">:</span> <span class="token string">"empty name"</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>만약 아래처럼 배열이 포함되어 있을 때, 두번째 에러 메시지를 불러오고 싶다면, <code>json().error[1].message</code> 이런식으로 숫자를 표시해주어야 한다.</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"error"</span> <span class="token punctuation">:</span> <span class="token punctuation">[</span>
	  <span class="token punctuation">{</span>
	    <span class="token string">"code"</span> <span class="token punctuation">:</span> <span class="token string">"error-01"</span><span class="token punctuation">,</span>
	    <span class="token string">"message"</span> <span class="token punctuation">:</span> <span class="token string">"empty name"</span>
	  <span class="token punctuation">}</span><span class="token punctuation">,</span><span class="token punctuation">{</span>
	    <span class="token string">"code"</span> <span class="token punctuation">:</span> <span class="token string">"error-02"</span><span class="token punctuation">,</span>
	    <span class="token string">"message"</span> <span class="token punctuation">:</span> <span class="token string">"empty phoneNumber"</span>
	  <span class="token punctuation">}</span>	  
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span>
</code></pre>
<p>이렇게 포스트맨의 Tests 기능을 이용해 API 응답을 테스트하고 검증하는 방법을 적어보았다.</p>
<p>눈으로 200 ok 를 보는것에 만족하지 않고, 데이터를 검증하는 테스트 구문만 살짝 추가하면 괜히 좀 멋있어보이는 테스트 결과를 볼 수 있다.</p>
<p>하지만 아직 아쉬움이 있다.<br>
입력값에 따라 결과값이 계속 달라질텐데, 그때마다 Tests 코드를 수정해야할까?</p>
<p>그렇지 않다!<br>
앞서 포스팅했던 csv 와 연동을 활용하면, 고정된 Tests 코드로 수천개를 테스트할 수 있다. 이는 다음 포스팅에서 소개하기로 한다.</p>

