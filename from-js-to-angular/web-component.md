---
description: >-
  자바스크립트 표준에서 지원하는 custom element 에 대해 알아보고 custom element 를 만들어 웹 컴포넌트를 만드는 방법에
  대해 다룹니다.
---

# 4. 웹 컴포넌트 만들기

### 동적으로 생성된 요소들의 정체

이번 세션에서는 이전 강의에서 다룬 할일 도메인 로직을 클래스로 캡슐화하는 방법에 대한 연장선에서, DOM 조작 로직을 클래스로 구성하는 가능성에 대해서도 살펴보겠습니다. 여러분은 아마도 동적으로 요소를 생성하기 위해 다음과 같은 코드를 작성해본 경험이 있을 것입니다. 이 코드는 이번 커리큘럼에서도 자주 활용되었습니다.

```javascript
document.createElement('요소이름');
```

[위 메서드](../gpt-docs/js-dom-api/document.createelement.md)를 통해 반환받는 객체들은 모두 [`HTMLElement`](../gpt-docs/js-dom-api/htmlelement.md) 클래스를 상속받는 서브클래스입니다. 이는 우리가 일반적으로 HTML에서 사용하는 태그나 요소들이 클래스로 정의되어 있다는 의미입니다. 이를 바탕으로 DOM 조작 로직을 클래스로 구현한다면, 점진적으로 기능적으로 완성도 높은 클래스 구조를 만들어낼 수 있습니다. 이러한 접근은 자바스크립트의 웹 컴포넌트 표준에 점점 더 가까워지게 만듭니다.

제가 클래스 기반으로 코드를 수정한 예제는 아래 GitHub 링크에서 확인하실 수 있습니다.

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.3.5-component-encapsulation" %}

이번 세션에서는 위의 소스코드를 자세히 다루지 않을 예정입니다. 원래 계획은 제가 작성한 코드를 통해 웹 컴포넌트 방식으로의 개선 과정을 보여드리려 했으나, 이 코드는 제 개인적인 아이디어를 담고 있어 반드시 올바른 접근법이라고는 할 수 없습니다. 따라서 오늘 세션의 주요 목적은 '웹 컴포넌트'라는 기술을 소개하고, 이 기술을 활용해 DOM 조작 로직을 어떻게 효과적으로 리팩토링할 수 있는지 보여드리는 데에 초점을 맞추고자 합니다.

### 웹 컴포넌트란?

{% embed url="https://developer.mozilla.org/en-US/docs/Web/API/Web_components" %}

MDN에서 웹 컴포넌트에 대해 자세히 다루고 있지만 GPT를 통해 핵심만 간략하게 요약하면 다음과 같습니다.

1. **쉐도우 DOM**: 쉐도우 DOM을 사용하면 스타일과 스크립트가 메인 페이지의 나머지 부분과 격리되어, 웹 컴포넌트의 내부 구조가 외부로부터 독립적으로 유지됩니다. 이는 컴포넌트가 다른 페이지 요소와 스타일 충돌 없이 사용될 수 있게 합니다.
2. **HTML 템플릿**: `<template>`과 `<slot>` 태그는 HTML 코드를 템플릿화하여 필요할 때마다 인스턴스화할 수 있게 합니다. 이 템플릿을 사용하여 복잡한 사용자 인터페이스를 한 번 정의하고, 여러 위치에서 재사용할 수 있습니다.
3. [**커스텀 엘리먼트**](../gpt-docs/js-dom-api/customelements.md): 개발자는 `customElements` API를 사용하여 새로운 HTML 태그를 정의할 수 있습니다. 이를 통해 기존 HTML 요소를 확장하거나 완전히 새로운 요소를 만들어, 웹 애플리케이션의 유지보수성과 코드의 가독성을 향상시킬 수 있습니다.

이 중에서 우리는 웹 컴포넌트의 핵심 기술인 커스텀 엘리먼트를 활용하여 웹 표준에 부합하는 컴포넌트를 직접 만들어 볼 예정입니다.

### 기존 코드를 개선하려면

기존의 코드 작성 방식에서 DOM 제어 로직을 클래스로 캡슐화하는 것에는 일정한 어려움이 있습니다. 예를 들어, 다음과 같은 HTML 문서에서 할일을 생성하는 form 태그와 그 하위 태그들이 명시되어 있습니다.

```html
<form id="todoForm">
    <input name="content" placeholder="할일을 입력해 주세요."/>
    <button>생성</button>
</form>
```

자바스크립트에서는 이러한 HTML 요소에 접근하여 이벤트를 추가합니다.

```javascript
const todoFormElement = document.querySelector('#todoForm');
todoFormElement.addEventListener('submit', event => {
  ...
});
```

위 코드를 보면 알 수 있듯이 TodoService와는 다르게 화면에 보여주어야할 태그와 그 태그의 이벤트를 제어하는 로직이 분리되어있다는 것입니다. html 파일로 분리된 것이 나쁜 건 아니지만 저 html 파일에는 form 을 제외한 다른 태그들도 존재하고, 모든 태그들이 작성되는 곳이기 때문에 같은 폴더 내에 필요한 html 파일만 따로 가지고 있지 않는 한 연관된 기능들만 캡슐화가 되어있다고 판단하긴 어려운 것 같습니다.

이를 해결하기 위해선, [단일 책임 원칙](../gpt-docs/oop/solid.md)을 고려해야 합니다. 예를 들어, `TodoService`는 할일 데이터와 그 데이터를 조작하는 행위들을 모아 쉽게 클래스로 구현할 수 있었습니다. 이는 할일 도메인을 다루는 한 가지 역할만 수행하기 때문입니다.

개발에 정답이 없듯이, 단일 책임의 해석도 개발자마다 다를 수 있습니다. 제가 `TodoService`를 만든 방식은 개인적인 아이디어와 경험, 다양한 사례를 바탕으로 한 것입니다. DOM 로직을 리팩토링하여 의미 있는 클래스로 만드는 것도 중요한 포인트입니다. 할일 도메인과는 다르게, DOM은 주로 어떻게 보여질지와 어떤 이벤트를 처리할지에 초점을 맞춥니다. 따라서 기존의 DOM을 다루는 코드를 클래스로 구현할 때는 HTML 태그들도 클래스에서 담당해야 할 역할의 일부가 되어야 합니다. 이는 웹 컴포넌트를 만드는 방식을 근거로 볼 수 있습니다.

### 코드 살펴보기

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TODO ⚗️</title>
    <link rel="stylesheet" href="./style.css">
    <script defer src="./index.js"></script>
</head>
<body>

</body>
</html>
```

HTML의 body 태그 내부의 내용을 모두 비워줍니다. 관련된 내용들 모두 자바스크립트에서 [커스텀 엘리먼트](../gpt-docs/js-dom-api/customelements.md)를 통해 생성할 예정입니다.

form 태그를 작성했던 부분과 form 의 submit 이벤트를 핸들링 하던 부분은 다음과 같이 하나의 클래스로 묶을 수 있습니다.

```javascript
class AddTodoComponent extends HTMLElement {

    constructor() {
        super();

        const form = document.createElement('form');
        form.onsubmit = (event) => this.#onSubmit(event);

        const input = document.createElement('input');
        input.name = 'content';
        input.placeholder = '할일을 입력해 주세요';

        const button = document.createElement('button');
        button.textContent = '생성';

        form.appendChild(input);
        form.appendChild(button);

        this.attachShadow({ mode: 'open' });
        this.shadowRoot.appendChild(form);
    }

    #onSubmit(event) {
        event.preventDefault();

        const formData = new FormData(event.target);
        const content = formData.get('content').trim();

        const addEvent = new CustomEvent('ADD_TODO', {
            detail: content
        });
        document.dispatchEvent(addEvent);

        event.target.reset();
    }
}

customElements.define('add-todo', AddTodoComponent);
```

작성된 클래스를 살표보면 다음과 같은 조금 낯선 부분이 있습니다.

```javascript
this.attachShadow({ mode: 'open' });
this.shadowRoot.appendChild(form);
```

위 부분은 웹 컴포넌트를 만들기 위한 기술 중 하나인[ Shadow Dom](web-component.md) 을 사용하는 부분입니다.&#x20;

위 클래스는 [HTMLElement ](../gpt-docs/js-dom-api/htmlelement.md)라는 클래스를 상속하는 클래스이자 할일을 생성하는 요소를 제공하는데 초점을 맞추었습니다. 자바스크립트 표준 클래스를 상속하기 때문에 최종적으로 [customElements.define](../gpt-docs/js-dom-api/customelements.md)을 통해 등록하게 되면 다음과 같이 새로운 웹 컴포넌트를 만들어 낼 수 있습니다.

```javascript
// html file
<add-todo></add-todo>

// or

// js file
const addTodoComponent = document.createElement('add-todo');
document.body.appendChild(addTodoComponent);

// 생성자 주입이 필요할 경우
const 생성자 = customElements.get('add-todo');
document.body.appendChild(new 생성자());
```

### 마무리

이전에 작성한 DOM 관련 로직중에 할일을 생성하는 부분의 로직을 웹 컴포넌트 방식으로 만들어보았습니다. 아직 보여드리지 않은 코드들도 소스코드에서 확인해보실 수 있는데, 한 파일에 많은 코드들이 작성되었기 때문에 다음 시간에 이 코드들을 es6의 모듈 시스템을 활용하여 클래스별로 파일을 분리하는 방법을 추가하여 설명하도록 하겠습니다.

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.4-web-component" %}
