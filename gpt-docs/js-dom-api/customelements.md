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

# customElements

JavaScript에서 `customElements`는 웹 컴포넌트의 핵심 부분으로, 개발자가 사용자 정의 HTML 요소를 생성하고 등록할 수 있게 해줍니다. 이를 통해 기존 HTML 요소를 확장하거나 완전히 새로운 요소를 만들 수 있습니다. `customElements` API는 브라우저의 Custom Elements Registry를 이용하여 이러한 요소들을 관리합니다.

#### Custom Elements 등록하기

`customElements.define()` 메서드를 사용하여 새 요소를 등록합니다. 이 메서드는 세 개의 매개변수를 받습니다:

1. **이름**: 새로운 요소의 이름. 이 이름에는 반드시 하이픈(`-`)이 포함되어야 합니다 (예: `my-element`).
2. **클래스**: 이 요소의 기능을 정의하는 클래스. 이 클래스는 `HTMLElement`에서 파생된 것이어야 합니다.
3. **옵션**: 선택적 매개변수로, 상속받고자 하는 기존 HTML 요소를 지정할 수 있습니다.

```javascript
class MyElement extends HTMLElement {
  constructor() {
    super(); // 항상 super()를 호출해야 합니다.
    // 요소의 초기화 코드 여기에 작성
  }
}

customElements.define('my-element', MyElement);
```

#### 사용자 정의 요소의 생명주기 콜백

사용자 정의 요소 클래스에서는 몇 가지 생명주기 콜백 메서드를 정의할 수 있습니다:

* **connectedCallback**: 요소가 문서 DOM에 연결될 때 호출됩니다.
* **disconnectedCallback**: 요소가 DOM에서 제거될 때 호출됩니다.
* **adoptedCallback**: 요소가 새 문서로 이동될 때 호출됩니다.
* **attributeChangedCallback**: 요소의 속성 중 하나가 추가/제거/변경될 때 호출됩니다.

```javascript
class MyElement extends HTMLElement {
  connectedCallback() {
    console.log('MyElement가 DOM에 추가되었습니다.');
  }
  disconnectedCallback() {
    console.log('MyElement가 DOM에서 제거되었습니다.');
  }
  adoptedCallback() {
    console.log('MyElement가 새 문서로 이동되었습니다.');
  }
  attributeChangedCallback(name, oldValue, newValue) {
    console.log(`속성 ${name} 가 변경되었습니다.`);
  }
}
```

#### 속성 감시하기

`observedAttributes`라는 정적 getter를 사용하여 특정 속성의 변경을 감시할 수 있습니다. 이 메서드는 감시할 속성 이름의 배열을 반환해야 합니다.

```javascript
class MyElement extends HTMLElement {
  static get observedAttributes() {
    return ['enabled', 'format'];
  }
}
```

사용자 정의 요소는 웹 개발에서 매우 유용하며, 복잡한 동작을 캡슐화하고 재사용 가능한 컴포넌트를 만드는 데 도움을 줍니다. 이를 통해 더욱 풍부하고 상호작용적인 웹 애플리케이션을 구축할 수 있습니다.
