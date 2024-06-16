---
layout: post
title: Bulletproof React Architecture 분석
sitemap: true
hide_last_modified: true
---


* toc
{:toc}


---


## 서론

Clean Architecture에 대해 늘 궁금했었다. 특히 Front-End 개발 특성 상, 디자인에 따라 컴포넌트를 나누다보니 개발에 쫓겨 구조가 엉망진창이 되는 경우가 많았다.

어느샌가 재사용을 위해 작성했던 컴포넌트들이 특정 용도에 국한되는 일이 생겼다. 결과적으로 비슷한 모양을 가진 새로운 컴포넌트를 작성하는 일이 빈번히 발생하게 되었다.

이 같은 사태를 방지하기 위해 BulletProof React의 구조를 분석하고 내 프로젝트에 적용시켜보기로 했다.


---


## Bulletproof React 란?

깃레포: [BulletProof React Github]

> A simple, scalable, and powerful architecture for building production ready React applications.

위 같은 설명으로 시작하는 **Bulletproof React는 일종의 React Architecture을 다룬 교보재**이다. 실제 Boilerplate로 사용하는 목적이 아닌 코드를 살펴보고 분석하는 예제이다. 예제에는 간단한 로그인, 가입, 토론 등의 기능이 포함되어 있다. 실제 기능은 [BulletProof React]에서 확인할 수 있다.


코드는 다음과 같은 원칙들을 지키고 있다.

* 쉽게 시작할 수 있음
* 이해하기 쉽고 유지하기 쉬움
* 작업에 알맞은 도구를 사용함
* **다른 부분 간의 경계가 확실함**
* 팀원 모두 같은 방식을 채택함
* 보안
* 성능
* **확장성**
* **빠른 Issue 감지**

**Chat**: 처음엔 BulletProof React를 접한 .것은 단순히 Architecture가 궁금해서였기 때문에 위에 표시된 기능에 중점을 두고 보게 되었다. 공부하다가 보니 Clean Architecture로 인해 나머지 원칙들도 지킬 수 있게 된다는 것을 알게되었다.
{:.message}


---


## 파일 구조 분석

![Full-width image](/assets/img/study/bulletproof_react_architecture/bulletproof_react_structure.png "Bulletproof React Architecture")
{:.lead width="852" height="519" loading="lazy"}
Bulletproof React Architecture
{:.figcaption}

![Full-width image](/assets/img/study/bulletproof_react_architecture/bulletproof_react_feature_structure.png "Feature Architecture")
{:.lead width="899" height="312" loading="lazy"}
Bulletproof React Features Architecture
{:.figcaption}



### 컴포넌트 구조

눈에 띄는 특징은 **features**라는 폴더의 존재였다. **features** 폴더에는 기능별 폴더 두어 components와 api를 분류하였다. BulletProof React의 설명을 읽다보면 비슷한 기능을 가진 것들은 최대한 가까이 위치시키라는 말이 자주 나오는데 이런 식으로 구조화하는 것 같았다.


그동안 나는 다음과 같이 **components**라는 폴더에 모든 컴포넌트를 넣었었다. 적당히 비슷해보이는 기능들을 가진 컴포넌트들을 폴더로 구분하는 의미없는 분류와 무작정 파일을 작게 쪼개기만하는 습관을 가지고 있었다.

![Full-width image](/assets/img/study/bulletproof_react_architecture/my_project_structure.png "과거 프로젝트의 components 파일")
{:.lead width="906" height="414" loading="lazy"}
약 2년전 진행한 프로젝트의 부끄러운 컴포넌트 폴더 구조
{:.figcaption}


BulletProof React는 **components** 폴더에는 다음과 같이 재활용이 확실한 UI 관련 컴포넌트와 Layout 컴포넌트 그리고 3rd party 라이브러리에 덧붙인 컴포넌트밖에 없었다. **즉, 재사용이 뚜렷한 컴포넌트들만 components 파일로 빼놓았다.** 단순히, 컴포넌트의 재활용이 많다고 좋은 것이 아니라는 것을 말하는 것만 같았다.

![Full-width image](/assets/img/study/bulletproof_react_components_architecture/my_project_structure.png "Bulletproof React Components Structure")
{:.lead width="910" height="244" loading="lazy"}
재활용성이 강한 Components들을 최소한으로 모은 구조
{:.figcaption}



### types 구조

과거 나는 컴포넌트 파일에는 한가지의 컴포넌트만 작성하라는 말을 맹목적으로 따랐었다. 그러다보니 type 정의도 types라는 폴더에 전부 빼서 사용했었는데 이는 불필요한 파일간 이동을 강제하게 되었다.

BulletProof React에서는 **types** 폴더에는 최소한의 api 관련 type만 정의해두었다. **나머지 컴포넌트에서 필요한 type들은 컴포넌트 파일 상단에 정의해두었다.**


---


## 단방향 구조

Bulletproof React를 분석하면서 가장 흥미로웠던 점은 **단방향성**을 유지한다는 점이었다. 단순히 개념적으로 지향한다는 것이 아니라 **eslint의 rules를 정의하여 타 feature간 import가 불가능하도록 제한**하였다.

~~~js
//file: eslintrc.cjs
rules: {
        'import/no-restricted-paths': [
          'error',
          {
            zones: [
              // disables cross-feature imports:
              // eg. src/features/discussions should not import from src/features/comments, etc.
              {
                target: './src/features/auth',
                from: './src/features',
                except: ['./auth'],
              },
              //...이하 생략
            ],
          },
        ],
~~~


### 코드의 흐름

전체적인 코드를 **feature에 따른 파일들**과 **전체적으로 사용되는 파일들을 분류**하였다.

이때 **단방향성**을 단적으로 유지하여 아래와 같은 흐름을 만들어냈다. 

![Full-width image](/assets/img/study/bulletproof_react_architecture/unidirectional_codebase.png "Unidirectional Codebase")
{:.lead width="2268" height="1588" loading="lazy"}
출처: https://github.com/alan2207/bulletproof-react/blob/master/docs/assets/unidirectional-codebase.png
{:.figcaption}



[BulletProof React]: https://bulletproof-react-app.netlify.app/
[BulletProof React Github]: https://github.com/alan2207/bulletproof-react
