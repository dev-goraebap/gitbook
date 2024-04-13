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

# CustomEvent

자바스크립트의 `CustomEvent`는 개발자가 직접 정의할 수 있는 이벤트 유형으로, 표준 이벤트와 마찬가지로 이벤트 리스너를 통해 처리할 수 있는 사용자 지정 이벤트 객체를 생성하는 데 사용됩니다. 이것은 특히 웹 애플리케이션에서 컴포넌트 간에 특정 상황이나 데이터를 전달할 때 유용합니다.

#### CustomEvent 생성

`CustomEvent`를 생성하려면 `new CustomEvent()` 생성자를 사용합니다. 이 생성자는 두 개의 매개변수를 받습니다:

1. `typeArg`: 이벤트의 이름입니다.
2. `customEventInit`: 이벤트의 구성 옵션을 지정하는 객체로, `detail` 속성을 포함할 수 있습니다. `detail` 속성은 이벤트와 관련된 추가 데이터를 포함하며, 이벤트를 수신하는 리스너에서 이 데이터를 사용할 수 있습니다.

#### 예시 코드

다음은 `CustomEvent`를 생성하고 사용하는 기본적인 예시 코드입니다:

```javascript
// CustomEvent 객체 생성
const myEvent = new CustomEvent('myEvent', {
  detail: { message: 'Hello, this is a custom event!' }
});

// 이벤트 리스너 추가
document.addEventListener('myEvent', function (e) {
  console.log(e.detail.message); // 'Hello, this is a custom event!'
});

// 이벤트 디스패치
document.dispatchEvent(myEvent);
```

위 코드에서 볼 수 있듯이, `CustomEvent`는 데이터를 포함할 수 있으며, 이벤트를 발생시키고 리스너에서 해당 데이터를 처리할 수 있습니다. 이를 통해 개발자는 애플리케이션의 다양한 부분 간에 복잡한 상호 작용을 더 쉽게 구현할 수 있습니다.
