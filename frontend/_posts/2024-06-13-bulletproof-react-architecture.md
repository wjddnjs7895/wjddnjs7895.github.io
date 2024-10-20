---
layout: post
title: "BulletProof React: A Guide to Clean Architecture for Scalable Front-End Development"
sitemap: true
image: /assets/img/frontend/bulletproof_react_architecture/bulletproof_react_structure.png
hide_last_modified: true
---

- toc
  {:toc}

## Introduction
I have always been curious about Clean Architecture. Particularly in front-end development, the need to divide components based on design often leads to chaotic structures as development progresses.

Before I knew it, components that were initially written for reuse became limited to specific use cases. Consequently, the frequent creation of new components with similar appearances became commonplace.

To prevent such situations, I decided to analyze the structure of BulletProof React and apply it to my projects.

---

##What is BulletProof React?

GitHub Repository: [BulletProof React GitHub]

> A simple, scalable, and powerful architecture for building production ready React applications.

위 같은 설명으로 시작하는 **Bulletproof React는 일종의 React Architecture을 다룬 교보재**이다. 실제 Boilerplate로 사용하는 목적이 아닌 코드를 살펴보고 분석하는 예제이다. 예제에는 간단한 로그인, 가입, 토론 등의 기능이 포함되어 있다. 실제 기능은 [BulletProof React]에서 확인할 수 있다.

Starting with this description, **BulletProof React serves as a resource for understanding React architecture**. It provides examples rather than serving as a boilerplate for direct implementation. The examples include simple functionalities like login, signup, and discussions. You can find the actual features at [BulletProof React](https://bulletproof-react.com/).

The code adheres to the following principles:

- Easy to get started
- Simple to understand and maintain
- Utilizes appropriate tools for the task
- **Clear boundaries between different parts**
- Consistent practices among all team members
- Security
- Performance
- **Scalability**
- **Rapid issue detection**

**Chat**: Initially, my interest in BulletProof React stemmed from a curiosity about architecture, which led me to focus on the features highlighted above. As I studied further, I realized that Clean Architecture enables adherence to these other principles as well.
{:.message}

---

## File Structure Analysis

![Full-width image](/assets/img/frontend/bulletproof_react_architecture/bulletproof_react_structure.png "Bulletproof React Architecture")
{:.lead width="852" height="519" loading="lazy"}
Bulletproof React Architecture
{:.figcaption}

![Full-width image](/assets/img/frontend/bulletproof_react_architecture/bulletproof_react_feature_structure.png "Feature Architecture")
{:.lead width="899" height="312" loading="lazy"}
Bulletproof React Features Architecture
{:.figcaption}

### Component Structure

A notable feature is the existence of a **features** folder. This folder categorizes components and APIs by functionality. The BulletProof React documentation frequently emphasizes that similar functionalities should be placed close to one another, and this structural organization reflects that principle.

In the past, I had placed all components in a **components** folder, often resorting to arbitrary classifications based on perceived similarities and an uncritical habit of fragmenting files unnecessarily.


![Full-width image](/assets/img/frontend/bulletproof_react_architecture/my_project_structure.png "과거 프로젝트의 components 파일")
{:.lead width="906" height="414" loading="lazy"}
An embarrassing component folder structure from a project I undertook about two years ago
{:.figcaption}

In BulletProof React, the **components** folder contains only reusable UI components, layout components, and components associated with third-party libraries. **In other words, it exclusively holds components with clear reuse potential.** This structure communicates the idea that simply having many reusable components does not necessarily indicate quality.

![Full-width image](/assets/img/frontend/bulletproof_react_architecture/bulletproof_react_components_structure.png "Bulletproof React Components Structure")
{:.lead width="910" height="244" loading="lazy"}
A structure that minimizes the collection of highly reusable components
{:.figcaption}

### Types Structure

Previously, I followed the guideline to have only one component per file blindly. As a result, I would often extract type definitions into a separate **types** folder, which led to unnecessary file navigation.

In BulletProof React, the **types** folder contains only the minimal types related to the API. **All other types required by components are defined at the top of their respective files.**

---

## Unidirectional Structure

One of the most intriguing aspects of analyzing BulletProof React is its commitment to maintaining **unidirectionality**. This is not merely a conceptual goal; rather, it is enforced through **eslint rules that restrict imports across features**.

```js
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
              //...other rules omitted
            ],
          },
        ],
```

### Code Flow

The overall code is categorized into files according to features and files that are utilized throughout the application.

In doing so, it creates a flow that distinctly maintains unidirectionality.

![Full-width image](/assets/img/frontend/bulletproof_react_architecture/unidirectional_codebase.png "Unidirectional Codebase")
{:.lead width="2268" height="1588" loading="lazy"}
출처: https://github.com/alan2207/bulletproof-react/blob/master/docs/assets/unidirectional-codebase.png
{:.figcaption}

[BulletProof React]: https://bulletproof-react-app.netlify.app/
[BulletProof React Github]: https://github.com/alan2207/bulletproof-react
