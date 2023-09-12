---
title: "Postman - Newman 으로 test runner, report 생성"
categories:
 - API테스트자동화
tags:
 - [postman, 포스트맨, api테스트자동화, newman, api테스트레포트]
toc: true
toc_sticky: true
date: 2023-09-12T15:00:00+09:00

---

<h2 id="소개">소개</h2>
<p><em>Postman에서 생성한 API 테스트 스크립트는 꼭 Postman에서만 돌려야할까?<br>
Postman은 html report가 없던데 어떻게 만들 수 있을까?<br>
Postman이 없으면 스크립트를 수행하지못하나?</em></p>
<p>등의 고민을 해본 적 있다면 <mark>newman</mark> 을 이용하면된다.</p>
<h2 id="newman-이란">Newman 이란?</h2>
<p>Postman을 CLI로 수행할 수 있게 해주는 도구이다.<br>
(Mac에선 Terminal, Windows에선 CMD(커맨드))<br>
Jenkins나 CircleCI 같은 CI/CD 에서도 newman을 이용해 Postman 파일을 수행할 수 있다.</p>
<p>npm 사이트에서 newman 설명이 잘 되어 있다. <a href="https://www.npmjs.com/package/newman?activeTab=readme">npm-newman</a><br>
설치는 npm, brew 등으로 가능하다.</p>
<p><code>newman -v</code> 을 했을 때 버전명이 나온다면 설치가 잘 된것이다.<br>
현재(2023.09) 최신 버전은 5.3.2</p>
<h3 id="newman-설치-에러">Newman 설치 에러</h3>
<p>전에 업무용 맥북에서 설치 후 <code>newman -v</code> 을 해도 버전이 안나오고 아래 에러가 발생했다.<br>
<code>Command not found after npm install in zsh</code><br>
이 (<a href="https://stackoverflow.com/questions/12743928/command-not-found-after-npm-install-in-zsh">Link</a>) 를 참고하여  <code>/.zshrc 에 export PATH="$HOME/.npm-packages/bin:$PATH"</code> 을 추가하였더니 수정할 수 있었다.</p>
<h2 id="newman으로-postman-테스트-수행-준비">Newman으로 Postman 테스트 수행 준비</h2>
<p>수행방법도 위 npm 사이트의 Usage에 매우 잘 적혀있지만,<br>
제일 먼저 테스트를 수행할 파일들을 Postman에서 Export 해야한다.</p>
<h3 id="collection-폴더-export">1) Collection 폴더 Export</h3>
<p>아래 이미지와 같이 테스트를 수행할 Collection Folder을 Export 해줘야 한다.<br>
API 파일 하나하나가 아니라 컬렉션 폴더를 통째로 Export 해줘야 한다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_01.png" alt="newman_01"></th>
</tr>
</thead>
<tbody></tbody>
</table><h3 id="environment-global-variable-export">2) Environment, Global Variable Export</h3>
<p>Environment와 Global Variable도 위와 유사한 방법으로 Export를 해주어야 한다.<br>
(테스트에 사용하지 않는다면 하지않아도 된다.)</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_02.png" alt="newman_02"></th>
</tr>
</thead>
<tbody></tbody>
</table><h3 id="하나의-폴더에-모아두기">3) 하나의 폴더에 모아두기</h3>
<p>테스트할 때 사용했던 csv 파일까지 모두 모아 한 폴더에서 관리하는게 편하다.<br>
아래 사진과 같이 ~collection.json, ~environment.json, ~globals.json, csv 파일까지 하나로 모아지면 준비 끝</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_03.png" alt="newman_03"></th>
</tr>
</thead>
<tbody></tbody>
</table><h2 id="cli-에서-newman으로-수행하기">CLI 에서 Newman으로 수행하기</h2>
<p>(본인은 맥북이므로) 터미널을 켜고, cd 명령어로 파일들을 모아두었던 폴더로 이동한다.</p>
<h3 id="newman-명령어-입력하기">1) newman 명령어 입력하기</h3>
<p>기본 포맷은 <code>newman run &lt;collection-file-source&gt; [options]</code> 이다.<br>
수행할 collection 폴더, 거기서 수행할 파일, Environment, Global, IterationData, Report 등을 차례대로 추가해주면 된다.</p>

<p><strong>컬렉션은 필수값</strong>이지만 <strong>아래값은 모두 옵션값</strong>이다.</p>
<p>내가 주로 사용하는 옵션에 대한 설명이다.<br>
상세한 설명은 위의 npm-newman 에 적혀있으니 참고!</p>
<p><code>--folder</code> : 컬렉션 폴더에서 원하는 파일만 원하는 순서로 수행할 때. 컬렉션 전체를 수행하고싶을때는 제거<br>
<code>--environment</code> : (<code>-e</code> 로 대체 가능) 수행할 환경 설정<br>
<code>--globals</code> : (<code>-g</code> 로 대체 가능) 수행할 Global variables 설정.<br>
<code>--iteration-data</code> : (<code>-d</code> 로 대체 가능) 테스트 데이터로 사용하는 csv 파일<br>
<code>--iteration-count</code> : (<code>-n</code> 로 대체 가능) csv 파일에서 상위 n 개만 돌리고 싶을때 (전체를 돌릴때는 위처럼 제거)<br>
<code>--reporters</code> :  생성할 레포트 파일 형식<br>
<code>--reporter-htmlextra-export</code> :  htmlextra 라는 html report 를 생성해주고, 저장할 위치 + 이름 설정</p>
<h3 id="newman-수행하기">2) newman 수행하기</h3>
<p>모든 명령어를 입력하고 엔터를 치면(입력하면?) 커맨드창에서 타라라락 테스트가 수행되고, 결과가 출력된다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_04.png" alt="newman_04"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>Postman의 Runner에서 수행한 것 처럼, Test 결과 및 Report가 보기좋게 출력된다.</p>
<p>사실…<br>
<strong>Postman의 유료화</strong> 정책이 강화되면서 <strong>Runner를 무료로 수행할 수 있는 횟수를 한 달 25회</strong>로 줄여버렸다.<br>
어느날 평상시와 같이 Runner을 마구 돌리다가 갑자기 뜨는 얼럿에 매우 당황했다.<br>
csv를 이용해 포스트맨을 수행하려면 Runner를 사용해야만 하는데, 한달 25회는 택도 없는 횟수이다.<br>
그래서 당분간은 newman을 써야할 것 같다.<br>
매번 최신 파일을 export를 해야하는 불편함이 있지만, 무료니까!<br>
아마도 언젠간 Postman을 유료로 구독할 것 같긴한데, 당분간은 로컬에서 csv로 돌릴때는 newman을 써야겠다.</p>
<h3 id="html-report-추가하기---newman-reporter-htmlextra">3) HTML Report 추가하기 - newman-reporter-htmlextra</h3>
<p>아직은 테스트 결과를 나만 볼 수 있을 뿐, 파일로 공유 및 관리가 어렵고 Report라고 내놓기엔 뭔가 아쉽다.<br>
그럴 때는 기본으로 제공하는 cli 레포트 외에 별도로 Report를 추가하면 된다.</p>
<p>마치 library를 추가하듯이 옵션에 추가하면 되는데, 여러 형식의 레포트가 있어서 원하는 것을 추가하면 된다.<br>
newman-reporter-html, newman-reporter-htmlextra, xml 파일, junit report 등 여러가지가 있다.</p>
<p>가장 많이 쓰는것은 <code>newman-reporter-htmlextra</code> 이다.<br>
newman과 동일하게 npm으로 설치할 수 있다.<br>
<code>npm install -g newman-reporter-htmlextra</code></p>
<p>이후 위에 적었던 newman 명령어의 <code>--reporters</code> 에 <code>htmlextra</code> 을 추가하고, <code>--reporter-htmlextra-export "reports/report.html"</code> 을 추가하면 된다.</p>

<p><code>--reporters</code> :  생성할 레포트 파일 형식<br>
<code>--reporter-htmlextra-export</code> :  htmlextra 라는 html report 를 생성해주고, 저장할 위치 + 이름 설정</p>
<p>이렇게 수행하고나면 파일들을 모아두었던 폴더 하위에 reports 폴더가 생성되고, report.html 파일이 생성된다.<br>
html 파일이므로 크롬같은 인터넷 브라우저으로 드래그앤드롭하여 실행시키면 아래 화면이 나온다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_05.png" alt="newman_05"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>첫번째 탭 (Summary)에서는 위와 같이 결과 요약을 볼 수 있다.</p>
<p>두번째 탭 (Total Requests) 에서는 아래와 같이 각 request별 상세를 볼 수 있다.</p>

<table>
<thead>
<tr>
<th><img src="/assets/images/postman_newman_06.png" alt="newman_06"></th>
</tr>
</thead>
<tbody></tbody>
</table><p>API request, response body 뿐 만 아니라 Test 내용과 결과까지 상세하게 나온다.<br>
UI도 괜찮고 실패시에 분석도 쉬워서 제일 잘 사용하는 API Test Report이다.</p>
<p>이렇게 newman 과 htmlextra report 까지 알아보았다.<br>
로컬에서 짧은 스크립트를 수행할 때는 이정도만 써도 충분하다.</p>
<p>하지만!<br>
환경변수를 설정하거나, 여러 collection을 돌리거나, 다른 레포트 (allure report)를 생성하는 듯 추가적으로 활용할 방법이 무궁무진하다.<br>
이 부분은 나~중에 포스팅 해야겠다.</p>

