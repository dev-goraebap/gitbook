---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# HTMLElement

`HTMLElement`는 JavaScript의 HTML 웹 페이지를 위한 인터페이스이자 클래스입니다. 이 클래스는 웹 브라우저가 해석하는 HTML 요소의 동작과 속성을 정의하며, 모든 HTML 요소가 공통적으로 상속받아 사용하는 기본 클래스입니다. `HTMLElement`는 DOM API의 일부로, JavaScript에서 HTML 요소를 조작할 때 사용하는 다양한 메서드와 속성을 제공합니다.

#### `HTMLElement`의 주요 특징:

* **클래스로서의 기능**: `HTMLElement`는 다양한 HTML 태그별 클래스들의 부모 클래스 역할을 합니다. 예를 들어, `<a>` 태그는 `HTMLAnchorElement` 클래스의 인스턴스이며, 이 클래스는 `HTMLElement`에서 상속받습니다. 이러한 구조 덕분에 모든 HTML 요소가 공통적인 메서드와 속성을 사용할 수 있습니다.
* **상속 구조**: `HTMLElement`는 `Element` 클래스에서 상속받으며, 이는 더 상위에 `Node`와 `EventTarget`로부터 상속받습니다. 이 계층적인 상속 구조는 HTML 요소가 DOM에서 노드로서 기능할 수 있게 하며, 이벤트를 처리할 수 있는 능력을 제공합니다.

#### `HTMLElement`의 주요 메서드와 속성:

* **속성 (Properties)**:
  * `innerHTML`: 요소 내의 HTML 콘텐츠를 가져오거나 설정합니다.
  * `outerHTML`: 요소를 포함한 외부 HTML을 가져오거나 설정합니다.
  * `style`: 요소의 스타일을 직접 조작할 수 있게 해주는 CSSStyleDeclaration 객체에 접근합니다.
  * `id`: 요소의 ID를 가져오거나 설정합니다.
  * `className`: 요소의 클래스를 가져오거나 설정합니다.
* **메서드 (Methods)**:
  * `click()`: 요소를 프로그래밍적으로 클릭합니다.
  * `focus()`: 요소에 포커스를 설정합니다.
  * `blur()`: 요소에서 포커스를 제거합니다.
  * `getAttribute(attributeName)`: 특정 속성의 값을 가져옵니다.
  * `setAttribute(attributeName, value)`: 요소에 새로운 속성을 설정하거나 기존 속성을 변경합니다.

이처럼 `HTMLElement` 클래스는 웹 페이지 내에서 HTML 요소를 생성하고 조작하는 데 필수적인 기능을 제공하며, JavaScript를 통해 웹 페이지의 동적인 상호작용을 가능하게 합니다.
