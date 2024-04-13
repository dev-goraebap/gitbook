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

# template, slot

`template` 태그와 `slot` 요소는 자바스크립트 DOM API의 일부이며, HTML 표준에 정의되어 있습니다. 이들은 웹 컴포넌트와 함께 사용되어 UI의 재사용 가능한 부분들을 정의하는데 도움을 줍니다.

#### `template` 태그

`template` 태그는 HTML에서 재사용 가능한 마크업 템플릿을 정의할 때 사용됩니다. `template` 내의 콘텐츠는 페이지가 로드될 때 렌더링되지 않습니다. 이는 콘텐츠가 화면에 표시되지 않고, 스크립트를 통해서만 활성화되어 사용될 수 있음을 의미합니다.

```html
<template id="my-template">
  <p>Hello, this is a template!</p>
</template>
```

이렇게 정의된 템플릿은 자바스크립트를 사용하여 DOM에 추가할 수 있습니다. 예를 들어, 다음과 같이 템플릿 내용을 복제하고 문서에 삽입할 수 있습니다.

```javascript
const template = document.getElementById('my-template').content;
document.body.appendChild(template.cloneNode(true));
```

#### `slot` 요소

`slot` 요소는 쉐도우 DOM 내에서 사용자 정의 웹 컴포넌트의 렌더링 위치를 정의하는 데 사용됩니다. 이를 통해 컴포넌트 사용자가 컴포넌트의 쉐도우 트리 내 특정 위치에 콘텐츠를 삽입할 수 있게 합니다.

```html
<my-element>
  <span slot="my-slot">Here is some slotted content!</span>
</my-element>
```

컴포넌트 정의 내에서 `slot` 태그는 해당 이름을 참조하여 사용자가 지정한 콘텐츠를 해당 위치에 배치합니다.

```html
<!-- Custom element's shadow DOM structure -->
<slot name="my-slot"></slot>
```

이 구조를 사용하면, `my-element` 내부의 `slot`에 `<span>` 요소의 내용이 들어가게 됩니다. 이렇게 `slot`을 사용하면 컴포넌트의 콘텐츠를 유연하게 재배치할 수 있어 컴포넌트의 재사용성이 크게 향상됩니다.

`template`과 `slot`은 웹 컴포넌트를 더욱 강력하고 유연하게 만들어 주는 중요한 도구입니다. 이들을 활용하여 동적이고 인터랙티브한 웹 애플리케이션을 구축할 수 있습니다.
