---
title: "Postman - Environment 환경설정 방법"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화]
toc: true
date: 2023-09-03T20:00:00+09:00

---

<p>Postman의 url 입력란을 잘 이용하면 한번의 request에서 다양한 서버로 호출을 할 수 있다.</p>
<p>QA를 할 때에는 stage존, 배포를 하고나면 Prod존에 호출을 해야하는데 보통 url에 작은 차이만 있고 body는 다 동일하다.<br>
url이 다르게다고 같은 request를 두 번 만들 필요는 없다.</p>
<h3 id="postman-environment-설정-방법">Postman Environment 설정 방법</h3>
<ol>
<li>
<p>왼쪽의 Environment 클릭</p>
</li>
<li>
<p>+ 를 눌러서 stage용 이름 입력</p>
</li>
<li>
<p>key에 “url”, value에는 stage만 달라지는 url 입력<br>
<img src="/assets/images/postman_env_1.png" alt="stage 추가"></p>
</li>
<li>
<p>+ 를 눌러서 prod용 이름 입력</p>
</li>
<li>
<p>stage와 동일한 key “url” 입력, value에는 prod용 url 입력<br>
<img src="/assets/images/postman_env_2.png" alt="prod 추가"></p>
</li>
<li>
<p>이후 원래의 request로 이동, url 부분에서 value로 입력한 부분만 {{ }} 로 씌어서 key값을 입력</p>
</li>
<li>
<p>우측 상단의 Environment에서 서버 설정<br>
<img src="/assets/images/postman_env_3.png" alt="request에 설정"></p>
</li>
</ol>
<p>url에는 {{ }} 를 이용하여 변수를 넣을 수 있다.<br>
지금은 포스트맨의 Environment 기능을 이용해서 넣었지만, postman의 collection variable, global variable, 일반 변수를 넣어도 가능하다.</p>
<p>이렇게 Environment를 설정해두면, url이 다르다고 request를 여버러 만들 필요 없이 우측의 버튼만 변경해서 dev, qa, stage, prod 등 다양한 서버를 쉽게 테스트할 수 있다.</p>

