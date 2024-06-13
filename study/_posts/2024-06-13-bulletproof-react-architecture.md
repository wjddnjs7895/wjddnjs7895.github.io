---
layout: post
title: Bulletproof React Architecture 분석
sitemap: false
hide_last_modified: true
---

- toc
  {:toc}

##서론
Clean Architecture에 대해 늘 궁금했었다. 특히 Front-End 개발 특성 상, 디자인에 따라 컴포넌트를 나누다보니 개발에 쫓겨 구조가 엉망진창이 되는 경우가 많았다.

어느샌가 재사용을 위해 작성했던 컴포넌트들이 특정 용도에 국한되는 일이 생겼다. 결과적으로 비슷한 모양을 가진 새로운 컴포넌트를 작성하는 일이 빈번히 발생하게 되었다.

이 같은 사태를 방지하기 위해 BulletProof React의 구조를 분석하고 내 프로젝트에 적용시켜보기로 했다.

##BulletProof React 란?

> A simple, scalable, and powerful architecture for building production ready React applications.

위 같은 설명으로 시작하는 BulletProof React는 일종의 React Architecture을 다룬 교보재이다. 실제 Boilerplate로 사용하는 목적이 아닌 코드를 살펴보고 분석하는 예제이다. 예제에는 간단한 로그인, 가입, 토론 등의 기능이 포함되어 있다. 실제 기능은 [BulletProof React]에서 확인할 수 있다.

코드는 다음과 같은 원칙들을 지키고 있다.
깃레포: [BulletProof React Github]

- 쉽게 시작할 수 있음
- 이해하기 쉽고 유지하기 쉬움
- 작업에 알맞은 도구를 사용함
- 다른 부분 간의 경계가 확실함
  {:.lead}
- 팀원 모두 같은 방식을 채택함
- 보안
- 성능
- 확장성
  {:.lead}
- 빠른 Issue 감지
  {:.lead}

**Note**: 처음엔 BulletProof React를 접한 것은 단순히 Architecture가 궁금해서였기 때문에 위에 표시된 기능에 중점을 두고 보게 되었다. 공부하다가 보니 Clean Architecture로 인해 나머지 원칙들도 지킬 수 있게 된다는 것을 알게되었다.
{:.message}

##파일 구조 분석

![800x400](https://via.placeholder.com/800x400 "Bulletproof React Architecture")

#컴포넌트 구조

눈에 띄는 특징은 **features**라는 폴더의 존재였다. 그동안 나는 다음과 같이 **components**라는 폴더에 모든 컴포넌트를 넣었었다. 적당히 비슷해보이는 기능들을 가진 컴포넌트들을 폴더로 구분하는 의미없는 분류와 무작정 파일을 작게 쪼개기만하는 습관을 가지고 있었다.

![800x400](https://via.placeholder.com/800x400 "과거 프로젝트의 components 파일")

BulletProof React는 **components** 폴더에는 다음과 같이 재활용이 확실한 UI 관련 컴포넌트와 Layout 컴포넌트 그리고 3rd party 라이브러리에 덧붙여 컴포넌트밖에 없었다.

**features** 폴더에는 기능별 폴더 두어 components와 api를 분류하였다. BulletProof React의 설명을 읽다보면 비슷한 기능을 가진 것들은 최대한 가까이 위치시키라는 말이 자주 나오는데 이런 식으로 구조화하는 것 같았다.

#types 구조

과거 나는 컴포넌트 파일에는 한가지의 컴포넌트만 작성하라는 말을 맹목적으로 따랐었다. 그러다보니 type 정의도 types라는 폴더에 전부 빼서 사용했었는데 이는 불필요한 파일간 이동을 강제하게 되었다.

BulletProof React에서는 **types** 폴더에는 최소한의 api 관련 type만 정의해두었다. 나머지 컴포넌트에서 필요한 type들은 컴포넌트 파일 상단에 정의해두었다.

[BulletProof React]: https://bulletproof-react-app.netlify.app/
[BulletProof React Github]: https://github.com/alan2207/bulletproof-react
