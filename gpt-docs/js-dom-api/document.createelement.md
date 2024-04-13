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

# document.createElement

JavaScript의 `document.createElement()` 메서드는 팩토리 함수(factory function)의 역할을 합니다. 팩토리 함수란 특정 객체를 생성하고 반환하는 함수로, 객체를 직접 생성하기 위해 `new` 키워드를 사용하는 대신, 메서드 호출을 통해 간접적으로 객체를 생성합니다. 이런 점에서 `createElement()`는 HTML 요소를 생성하는 팩토리 함수로 볼 수 있습니다.

`document.createElement()` 함수는 다음과 같은 특징을 가집니다:

1. **동적 생성**: 이 메서드를 사용하면 실행 시간에 HTML 요소를 동적으로 생성할 수 있습니다. 이는 프로그램 로직에 따라 요소를 조건적으로 추가하거나, 사용자의 입력을 반영하는 인터페이스를 구성할 때 유용합니다.
2. **종류 지정**: 함수의 인자로 전달된 태그 이름에 따라 해당하는 종류의 HTML 요소를 생성합니다. 예를 들어, 'div', 'span', 'a' 등의 태그 이름을 인자로 전달하면, 각각 `HTMLDivElement`, `HTMLSpanElement`, `HTMLAnchorElement` 등의 인스턴스를 생성합니다.
3. **자동화된 상속**: 생성된 요소는 자동으로 적절한 프로토타입 체인을 상속받습니다. 즉, `HTMLElement` 또는 그 하위 클래스로부터 메서드와 속성을 상속받아, DOM에서 요소를 조작할 수 있는 다양한 API를 사용할 수 있습니다.
4. **확장성**: 이 메서드는 새로운 종류의 요소를 추가하는 웹 표준의 발전에 따라 확장됩니다. 예를 들어, HTML5에서 새롭게 추가된 요소들을 생성하기 위해 사용될 수 있습니다.

이러한 특징들은 `document.createElement()`를 객체 생성을 위한 매우 강력하고 유연한 팩토리 함수로 만들어줍니다.
