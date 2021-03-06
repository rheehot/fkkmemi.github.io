---
layout: splash
permalink: /nuxt/
title: "NUXT로 혼자 웹사이트 만들기 제작 강좌"
author_profile: false
---

![alt vue+fire](/images/talk/2019-06-26_20.29.06.png)

[모던웹](/nemv/) 의 심화과정입니다.

기존의 복잡했던 백&프론트엔드를 묶어서 다른 디비와 연결해 보는 것이 핵심입니다.

{% include toc %}

# 기본편

## 1 소개

항상 고민하던 부분이 있습니다.

백엔드, 프론트엔드 어떻게 나눠야할까?

백엔드와 프론트엔드의 통신 이질감을 최대한 없애기 위해 한 저장소(리포지토리)에 놓고 여러가지 방법을 동원해도..

복잡함을 피할 수 없었습니다.

많은 이가 고민하는 곳에는 항상 해결된 방법이 존재하기 마련입니다.

바로 넉스트(NUXT)가 그런 고민을 조금 덜어주고 생산성을 높여줄 수 있다고 봅니다.

[바로가기](/nuxt/nuxt-001-intro/){: .btn .btn--success}

## 2 설치

NUXT를 설치하고 간단히 구조를 알아봅니다. 

[바로가기](/nuxt/nuxt-002-install/){: .btn .btn--success}

## 3 라우팅

서버사이드 렌더링도 중요하지만, 기존에 하던 동적 화면 전환이 잘 되는 것이 더 중요합니다.

간단하게 axios를 이용해서 서버 미들웨어와의 통신을 해보겠습니다.

[바로가기](/nuxt/nuxt-003-routing/){: .btn .btn--success}

## 4 플러그인 설치

전역으로 사용할 플러그인 설치에 대해 알아봅니다.

[바로가기](/nuxt/nuxt-004-plugin/){: .btn .btn--success}

## 5 firebase로 hosting해보기

넉스트(nuxt) 소스를 파이어베이스 호스팅(google firebase hosting)을 이용해서 배포(deploy)해봅니다.

[바로가기](/nuxt/nuxt-005-firebase-hosting/){: .btn .btn--success}

## 6 firebase로 데이터베이스(firestore) 사용하기 

파이어베이스의 noSQL 데이터베이스인 firestore를 파이어베이스 호스팅 환경에서 사용해봅니다.

[바로가기](/nuxt/nuxt-006-firebase-firestore/){: .btn .btn--success}

## 7 닷엔브(dotenv)로 파이어베이스 프로젝트 정리하기 

넉스트 프로젝트에서 닷엔브로 파이어베이스를 사용하기 위한 설정을 해보겠습니다.

[바로가기](/nuxt/nuxt-007-firebase-dotenv/){: .btn .btn--success}

## 8 파이어베이스(firebase) 펑션스(functions) 시작하기

파이어베이스 펑션스를 이용해서 API를 간단히 구현해봅니다.

[바로가기](/nuxt/nuxt-008-firebase-functions-start/){: .btn .btn--success}

## 9 파이어베이스(firebase) 파이어스토어(firestore) CRUD 해보기

파이어스토어를 이용해서 CRUD 를 시험해보겠습니다.

[바로가기](/nuxt/nuxt-009-firebase-firestore-crud/){: .btn .btn--success}

## 10 파이어베이스(firebase) 파이어스토어(firestore) 페이징(paging)처리하기

파이어스토어를 이용해서 페이징처리를 해봅니다.

[바로가기](/nuxt/nuxt-010-firebase-firestore-paging/){: .btn .btn--success}

## 11 파이어베이스(firebase) 인증 기능(email&password) 사용하기

파이어베이스 인증기능중 이메일+비밀번호를 사용해서 로그인해봅니다.

[바로가기](/nuxt/nuxt-011-firebase-auth/){: .btn .btn--success}

## 12 파이어베이스(firebase) 인증 기능 토큰 알아보기

파이어베이스 인증 기능중 토큰에 대해 알아봅니다.

[바로가기](/nuxt/nuxt-012-firebase-auth-token/){: .btn .btn--success}

## 13 파이어베이스(firebase) 인증 기능 화면 표시하기

파이어베이스 인증 기능을 넉스트 저장소(vuex)를 이용해 표현해봅니다.

[바로가기](/nuxt/nuxt-013-firebase-auth-vuex/){: .btn .btn--success}

## 14 파이어베이스(firebase) 인증 기능 라우팅해보기

파이어베이스 인증 기능을 넉스트 저장소(vuex)를 이용해 표현해봅니다.

[바로가기](/nuxt/nuxt-014-firebase-auth-middleware/){: .btn .btn--success}

## 15 파이어베이스(firebase) 리캡챠(recaptcha v3) 사용하기

넉스트(nuxt) 와 파이어베이스 펑션스 (firebase functions)로 리캡챠(recaptcha v3)를 사용해봅니다.

[바로가기](/nuxt/nuxt-015-firebase-auth-recaptcha/){: .btn .btn--success}