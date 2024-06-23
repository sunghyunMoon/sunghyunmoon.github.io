---
title: Puppetter와 Cucumber
categories:
- UI Test
feature_image: "https://raw.githubusercontent.com/sunghyunMoon/sunghyunmoon.github.io/main/assets/img/background/react.png"
---

이번에 UI 테스트를 진행하게 되면서 공부하게된 내용을 정리해본다.

### UI 테스트

- 개발을 할때 테스트에는 여러 유형들이 있다. 테스트 가능한 가장 작은 단위인 함수 하나를 테스트하는 유닛 테스트가 있고, 그 보다 큰 여러 모듈들을 모아 이들이 의도대로 동작하는지 확인하는 통합 테스트가 있다. 
- UI 테스트란 통합 테스트보다 큰 단위이고, 사용자와 어플리케이션의 상호작용이 원활하게 이루어지는지 테스트를 하는 것이다.(전체 애플리케이션의 흐름 테스트)
- 사용자 흐름 테스트
    - 실제 애플리케이션 사용자가 사용하는 것과 같은 방식으로 일련의 동작을 수행해 보는 테스트다.
- 사용자 시나이로 검증
    - 그래서 UI 테스트는 사용자가 애플리케이션을 사용할 때 겪을 수 있는 다양한 시나리오를 검증한다. 버튼 클릭, 폼 제출, 화면 전환, 텍스트 입력 등의 인터페이스 요소들이 기대한 대로 작동하는지 확인할수 있다.

### Puppeteer

- Puppeteer는 Google에서 개발한 Node.js 라이브러리로 Headless Chrome 또는 Chromium을 제어할 수 있다.
    - Headless Chrome??
        - Headless Chrome은 사용자 인터페이스 없이 **백그라운드에서 실행되는 구글 크롬 브라우저**를 말한다. 즉, 화면에 아무것도 표시되지 않지만, 일반 Chrome 브라우저와 동일한 기능을 수행할 수 있다.
        - 주로 웹 스크래핑, 자동화된 테스트, 성능 모니터링, CI/CD 파이프라인 등에서 사용됩니다.
- 따라서 Puppeteer는 브라우저를 프로그래밍 방식으로 제어할 수 있게 하여, 다음과 같은 다양한 작업을 자동화할 수 있다.
    - 웹 페이지 탐색 및 상호작용
    - 스크린샷 및 PDF 생성
    - 웹 페이지 콘텐츠 스크랩
    - 퍼포먼스 테스트 및 트러블슈팅
- Puppeteer의 주요 기능
    - Headless 브라우저 제어: UI 없이 백그라운드에서 브라우저를 실행할 수 있으며, 필요에 따라 UI를 활성화할 수도 있다.
    - DOM 조작: JavaScript를 사용하여 웹 페이지의 DOM 요소를 탐색하고 조작할 수 있다.
    - 네트워크 요청 조작: 요청을 가로채고, 수정하고, 응답을 시뮬레이트할 수 있다.

### Cucumber(오이)

- 참고 : https://cucumber.io/docs/guides/overview/
- 오이처럼 시원하게 BDD를 지원한다하여 Cucumber라고 한다.(?ㅋㅋ)
- Cucumber는 BDD(Behavior Driven Development) 프레임워크로, 비즈니스 이해 관계자와 개발자 간의 소통을 원활하게 하기 위해 자연어 형식의 시나리오를 테스트 코드로 변환한다.
    - BDD(Behavior Driven Development)??
        - TDD(Test Drviven Development)는 테스트를 먼저 작성하고 그 테스트를 통과시키는 코드를 작성하는 흐름을 기본으로 한다. 게다가 테스트 단위도 함수 단위로 매우 작아서 작성하는 거의 모든 함수가 테스트 대상에 포함된다. 이상적으로 보일 수 있지만 그만큼 현업에서 사용하기에는 괴리감이 있다.
        - 프로젝트 초기에 야심 차게 TDD를 도입하여 거의 모든 함수에 대한 테스트 케이스를 준비했더라도 개발 중후반에 수정되는 내용에 대해서 깨지는 테스트 케이스를 계속 유지하면서 가져가기란 쉽지 않다.
    - BDD는 시나리오를 기반으로 테스트 케이스를 작성하며 함수 단위 테스트를 권장하지 않는다. 이 시나리오는 개발자가 아닌 사람이 봐도 이해할 수 있을 정도의 레벨을 권장한다. 하나의 시나리오는 Given, When, Then 구조를 가지는 것을 기본 패턴으로 권장하며 각 절의 의미는 다음과 같다.

```
Feature : 테스트에 대상의 기능/책임을 명시한다.

Scenario : 테스트 목적에 대한 상황을 설명한다.

Given : 시나리오 진행에 필요한 값을 설정한다.

When : 시나리오를 진행하는데 필요한 조건을 명시한다.

Then : 시나리오를 완료했을 때 보장해야 하는 결과를 명시한다
```

- 그럼 실제 Scenario를 보자.

```
Scenario: Breaker guesses a word
  Given the Maker has chosen a word
  When the Breaker makes a guess
  Then the Maker is asked to score
```

- 각 시나리오는 Cucumber가 수행해야 할 단계 목록이다. Cucumber가 시나리오를 이해하려면 Gherkin 이라는 몇 가지 기본 구문 규칙을 따라야 합니다.

<h4>Gherkin</h4>

    - Gherkin은 Cucumber가 이해할 수 있을 만큼 구조화된 일반 텍스트를 만드는 문법 규칙 집합이다.
- **Gherkin 문서는 .feature 형태의 텍스트 파일로 저장**되며 일반적으로 소프트웨어와 함께 소스 제어에서 버전이 관리된다. 

<h4>Step Definition</h4>

- feature(Gherkin step)에서 정의한 시나리오를 StepDefinition이라는 클래스 파일을 통해 프로그래밍 코드로 구현된다.
- 각 단계는 실제로 테스트 코드들을 포함하고 있으며, Gherkin 언어로 작성된 시나리오와 매핑된다.

<h3>끝!</h3>
