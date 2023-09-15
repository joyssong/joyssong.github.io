---
title: "Postman - Console 이용, 내용 콘솔 출력하기"
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