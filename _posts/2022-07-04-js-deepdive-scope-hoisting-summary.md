---
title: 스코프 호이스팅 정리
categories:
- Modern JavaScript DeepDive
feature_image: "https://cdn.geekboots.com/geek/javascript-hero-1652702096795.webp"
---

#### 스코프란

- 스코프는 식별자가 유효한 범위

#### 스코프 체인

- 모든 스코프는 하나의 계층적 구조로 연결되며, 모든 지역 스코프의 최상위 스코프는 전역 스코프
- 스코프 체인이란, 스코프가 계층적으로 연결된 것을 의미
- 스코프 체인은 물리적인 실체로 존재함
- 자바스크립트 엔진은 코드（전역 코드와 함수 코드）를 실행하기에 앞서 자료구조인 렉시컬 환경(Lexical Environment)을 실제로 생성

#### 렉시컬 스코프

- 함수 정의가 평가되는 시점에 상위 스코프가 정적으로 결정하는 스코프(정적 스코프)

#### 변수 호이스팅 이란

- 변수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징
    - 자바스크립트 엔진은 소스코드를 한줄씩 순차적으로 실행하기에 앞서 먼저 소스코드의 평가과정을 거치면서 소스코드를 실행하기 위한 준비
    - 소스코드의 평가 과정에서 자바스크립트 엔진은 변수 선언을 포함한 모든 선언문（변수 선언문, 함수 선언문 등）을 소스코드에서 찾아내 먼저 실행
    - 소스코드의 평가 과정이 끝나면 비로소 변수 선언을 포함한 모든 선언문을 제외하고 소스코드를 한 줄씩 순차적으로 실행

<h3>끝!</h3>