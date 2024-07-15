---
layout: post
title: Web Performance 지표
sitemap: true
hide_last_modified: true
---


* toc
{:toc}




## 서론

기초부터 다시 공부하기 시작하면서 **웹 최적화**에 관한 내용은 제쳐두려고 했었다. 부실한 탑을 무너뜨리고 튼튼한 토대를 쌓아가는 과정에서 웹 최적화는 이르다고 판단했기 때문이다.

하지만 프로젝트에 사용할 프레임워크를 고민하면서 각 프레임워크들이 갖는 장점들에 웹사이트의 성능 지표를 나타내는 용어들이 나오는 것을 발견하였다. 겉핥기 식으로만 알고 있던 각종 성능 지표에 관한 정보들을 이번 기회에 정리해보려고 한다.

단, 성능 지표의 정의에 대한 정리만 하고 각종 지표를 최적화하는 방법에 대한 공부는 차후에 하도록 한다.


---


## Lighthouse

> Lighthouse is an open-source, automated tool for improving the performance, quality, and correctness of your web apps.

![Full-width image](/assets/img/study/web_performance_metrics/lighthouse_scoring_calculator.png "Lighthouse Scoring Calculator")
{:.lead width="600" height="414" loading="lazy"}
Lighthouse Scoring Calculator
{:.figcaption}

간단히 말하자면 특정 웹 사이트의 성능을 측정하는 사이트이다.

이번 글에서는 **Lighthouse** 사이트에서 측정해주는 지표들을 기준으로 알아보려고 한다.



---


### FCP  (First Contentful Paint)

* 사이트를 방문한 직후, **페이지 컨텐츠 (텍스트, 이미지)의 일부가 화면에 렌더링 되는 시점까지**의 시간
* 1.8s 이하를 추구

이때 화면에 뜨는 첫번째 픽셀을 측정하는 FP(First Paint)와 달리 DOM에 포함된 엘리멘트가 표시되는 시간을 측정한다.


### SI  (Speed Index)

* 페이지의 **컨텐츠가 시각적으로 얼마나 빨리 채워지는지 측정**하는 지표
* 페이지가 완성되는 시점이 같더라도 과정에 따라 점수가 바뀜
* 수정된 부분만 업데이트 되는 SPA가 대두되면서 중요성이 떨어졌다


### LCP  (Largest Contentful Paint)

* 화면에 표시되는 **가장 큰 이미지 또는 텍스트 블록의 렌더링 시간**
* Paint 시간까지를 측정하는 다른 지표들과 달리, 가장 큰 컨텐츠를 측정한다
* 2.5s 이하를 추구


### TTI  (Time To Interactive)

* **사용자 입력에 안정적으로 응답할 수 있게 되는데 걸리는 시간**
* SSR을 사용하면 컨텐츠들이 화면에 빠르게 표시될 수 있지만, 스레드가 차단되거나 JavaScript가 로드되지 않아 상호작용이 되지 않는다 -> TTI의 증가
* 쾌적한 UX를 위해 **FCP와 TTI의 차이를 최소화**하기 위해 최선을 다해야한다
* 5s 이하를 추구


### FID  (First Input Delay)

* 사용자가 **처음 상호작용할 때부터 브라우저가 이벤트 핸들러를 시작할 때**까지의 시간
* 첫번째 입력(클릭, 탭, 키 누름)에만 초점을 맞춘다
* TTI는 메인스레드에 여유가 생겨 상호작용을 원활하게 할 수 있는 시점까지를 측정하는 반면, FID는 **실제 사용자의 입력과 그 입력에 대한 반응까지 걸리는 시간**을 측정한다
* 100ms 이하를 추구


**Chat**: Google 에서 선정한 Core 지표 3가지는 LCP, FID, CLS이다. 즉, TTI보다 사용자에게 더 밀접한 관련이 있는 FID를 중요시한다고 볼 수 있다.
{:.message}


![Full-width image](/assets/img/study/web_performance_metrics/tti_versus_fid.png "TTI versus FID")
{:.lead width="300" height="143" loading="lazy"}
TTI vs FID 시점 차이
{:.figcaption}


### TBT  (Total Blocking Time)

* FCP 이후, **입력이 방지되기에 충분할만큼 메인 스레드가 차단되는 시간의 총합**
* 아래 그림에 나와있는 것처럼 짧은 스레드 차단 시간은 무시된다
* 200ms 이하를 추구

![Full-width image](/assets/img/study/web_performance_metrics/total_blocking_time.png "Total Blocking Time")
{:.lead width="300" height="58" loading="lazy"}
Total Blocking Time
{:.figcaption}


### CLS  (Cumulative Layout Shift)

* 페이지의 전체 수명 주기동안 발생하는 모든 **예상치 못한 레이아웃 변경 점수**
* 점수 0.1 이하 추구
* 레이아웃 변경에 따른 심각한 사용자 문제 발생 가능 (예시: [CLS ISSUE])

**Chat**: 특정 시간을 측정하는 타 지표들과 달리 유일하게 시간과 무관한 지표이다. 하지만 시간 관련 최적화를 하지 않으면 사용자로 하여금 불편함만 유도하지만 CLS 지표를 무시하면 위 예시처럼 사용자로 하여금 의도치 않은 경험을 유도할 수 있다. 때문에 UX 측면에서 중요도가 높은 지표라고 느꼈다.
{:.message}


---

## 결론

사용자 경험을 위해서 UI만 열심히 디자인하면 된다고 생각하던 시절이 있었다. 하지만 이번 기회에 개발자는 시각적인 부분뿐만 아니라 다양한 측면에서 UX의 향상을 도출해낼 수 있다는 생각이 들었다.

아직은 각 지표들의 개념 정리만 했지만 후에 최적화를 착실히 공부해야겠다.

[CLS ISSUE]: https://web.dev/articles/cls/video/web-dev-assets/layout-instability-api/layout-instability2.webm