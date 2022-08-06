---
title: z-index 분석
categories:
- CSS
feature_image: "https://picsum.photos/2560/600?image=872"
---

* Renderer Process
- 렌더링 프로세스는 브라우저 탭 안에서 일어나는 모든 일을 담당한다.
- 렌더러 프로세스 안에는 메인 스레드가 우리가 구현한 대부분의 코드를 처리하고, 워커, 컴포지터, 래스터 스레드도 존재한다.
- 렌더러 프로세스의 핵심 역할은 HTML, CSS, 그리고 자바스크립트를 사용자가 interation 할 수 있는 웹피이지로 만드는 것이다.
- 브라우저 렌더링 파이프 라인
!rendering_pipeline.PNG!
*** 파싱(메인 스레드) : DOM과 CSSOM을 통해 렌더 트리를 생성한다.
*** 레이아웃(메인 스레드) : 레이아웃 과정에서는 RenderTree를 이용해 각 element의 크기와 위치를 계산한다.
*** 페인트(메인 스레드 & 래스터 스레드) : 페인트 과정에서 메인 스레드는 하나의 페이지를 여러 층의 레이어로 분리하고 그 레이어들을 어떤 순서로 그릴지 결정한다.
**** 이 과정에서 레이아웃 트리를 기반으로 레이어 트리(Layer Tree)가 생성된다. 어떤 element가 어떤 layer에 있어야 하는지 확인하기 위해 메인 스레드는 레이아웃 트리를 순회하며 레이어 트리를 만든다.
**** 레이어 트리가 생성되면 래스터 스레드가 텍스트, 색, 이미지, 경계 및 그림 등 element의 모든 시각적 부분을 그리기 위해 각 레이어의 픽셀을 채우게 된다.(아직 화면에 그려지는게 아님!)
*** 컴포지트(컴포지터 스레드) : 컴포지터 스레드가 픽셀이 채워진 레이어를 순서대로 합성해 화면에 보여주게 된다.
* Rendering Performance 분석
** JS가 Style을 변화시키면 Re-render 된다. 항상 모든 프레임에서 파이프 라인의 모든 부분을 건드릴 필요는 없다.
** Reflow(layout - paint - composite)
*** element의 geometry 형태에 영향을 주는 layout 속성을 변경하면 브라우저는 reflow을 수행한다.
** Repaint(paint - composite)
*** 레이아웃에 영향을 주지 않는 *paint only* 속성을 변경하면 브라우저는 레이아웃을 건너뛴다.
*** 대표적인 속성으로 background-color, z-index가 있다.(참고 : https://csstriggers.com/)
* z-index & stacking contexts
** z-index(z 축에 렌더링되는 레이어)
*** Default Rendering Layer는 0이고, z-index가 음수로 설정되면 렌더링 레이어가 Default Rendering Layer 뒤에 배치된다.
** Default Stacking Order
```1. Root element의 배경과 테두리
2. 자식 element들은 HTML에서 등장하는 순서대로
3. position이 지정된 자식 element들은 HTML에서 등장하는 순서대로
```
** Stacking Contexts
*** Default stacking order와 z-index 값의 효과는 stacking contexts를 만드는 element의 자식 element에만 적용된다.
*** 그럼 Stacking contexts는 어떻게 생성되나?
**** z-index가 없다면 Default Stacking Contexts는 root element인 <html>이다.
**** position이 absolute 또는 relative 이면서, z-index가 auto가 아닌 값을 지닌 element
**** opacity가 1보다 작은 요소
**** transform의 값이 none이 아닌 요소
*** 중요한 것은, 자식의 z-index 값은 부모에게만 의미가 있다.
!z_index_example.PNG!
*** stacking contexts 계층 구조
```
Root
	DIV #1
	DIV #2
	DIV #3
		DIV #4
		DIV #5
		DIV #6
```