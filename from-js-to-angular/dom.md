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

# 1. DOM 기반의 투두리스트 만들기

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.1-make-based-element" %}
소스코드
{% endembed %}

이번 시간에는 DOM 요소 기반의 투두리스트를 만들어볼 예정입니다. 먼저 HTML 파일을 확인하면 다음과 같이 사용자 상호작용을 위한 최소한의 마크업만 작성되어있습니다.

```html
...
<form id="todoForm">
    <input name="content" placeholder="할일을 입력해 주세요."/>
    <button>생성</button>
</form>
<ul id="todoView"></ul>
...
```

todoView의 영역은 자바스크립트 DOM API를 통해 동적으로 컨텐츠가 생성될 예정입니다.

다음으로 HTML에 연결된 index.js 파일을 확인하면 상단에 #todoForm, #todoView 요소에 접근하는 것을 확인할 수 있습니니다.

```javascript
const todoFormElement = document.querySelector('#todoForm');
const todoViewElement = document.querySelector('#todoView');
```

todoFormElement는 아래와 같이 이벤트 리스너를 추가하여 submit 이벤트를 감지하고 할일 아이템 요소를 생성하는 로직을 실행하게 됩니다.

```javascript
todoFormElement.addEventListener('submit', event => {
    event.preventDefault();

    const formData = new FormData(event.target);
    const content = formData.get('content').trim();

    if (!content) {
        window.alert('내용을 입력해 주세요.');
        return;
    }

    const todoItemElement = createTodoItem(content);
    todoViewElement.appendChild(todoItemElement);

    event.target.reset();
});
```

1. submit 이벤트는 기본적으로 html 페이지를 새로고침 하는데, 새로고침하는 이벤트를 막습니다.
2. event.target은 이벤트를 호출한 form 요소 자신입니다. FormData 클래스에 form 요소를 주입하면 form 요소하위의 값들에 접근할 수 있게됩니다. name이 content 인 요소의 값에 접근하는 모습입니다.
3. content 의 값을 검증하고 작성되지 않는 값일 경우 사용자에게 알림창을 띄우고 early return 합니다.
4. createTodoItem 함수에 작성된 할일 택스트를 인자값으로 넘기고 todoItemElement를 전달 받습니다. 상단에 엑세스했던 todoViewElement에 생성된 todoItem 요소를 추가해줍니다.
5. form 요소의 값을 초기화 합니다.

createTodoItem 함수는 개별적인 할일 요소를 생성하는 팩토리 함수입니다.

```javascript
const createTodoItem = (content) => {

    const checkBoxElement = document.createElement('input');
    checkBoxElement.type = 'checkbox';
    checkBoxElement.addEventListener('click', event => {
        const todoItemElement = event.target.parentNode;
        const contentElement = todoItemElement.querySelector('span');
        contentElement.classList.toggle('checked');
    });

    const contentElement = document.createElement('span');
    contentElement.innerText = content;

    const removeButtonElement = document.createElement('button');
    removeButtonElement.innerText = '삭제';
    removeButtonElement.addEventListener('click', event => {
        const todoItemElement = event.target.parentNode;
        todoItemElement.remove();
    });

    const todoItemElement = document.createElement('li');
    todoItemElement.appendChild(checkBoxElement);
    todoItemElement.appendChild(contentElement);
    todoItemElement.appendChild(removeButtonElement);

    return todoItemElement;
}
```

위 코드를 보면 내용이 많아 보이지만 사실은 아래와 같은 컴포넌트를 만드는거라고 생각하시면 됩니다.

```html
<li>
    <input type="checkbox"/>
    <span>content</span>
    <button>삭제버튼</button>
</li>
```

DOM API를 통해 HTML의 네이티브 요소들을 생성합니다. 채크박스와 삭제버튼의 경우 클릭되었을 때 이벤트를 주기 위해 각각 이벤트 리스너를 등록해준 상태입니다.&#x20;

채크박스 클릭의 경우 채크박스의 상위요소인 li 요소에 접근하여 형제요소 span 에 접근하게됩니다. 이 때 checked 라는 클래스를 추가하거나 제거하게 되는데요. checked 클래스는 style.css 파일에 이미 작성되어있는 취소선을 추가해주는 클래스입니다.

```css
.checked {
    /* 취소선 추가 */
    text-decoration: line-through;
}
```

삭제 버튼을 클릭했을 경우 삭제버튼의 부모요소인 li 요소에 접근하고 li 요소를 삭제시키는 작업을 합니다. li 요소가 실제 할일 아이템의 root 컴포넌트이니까요.

### 고려해야할 문제점

위의 코드만 작성하면 당장은 투두리스트 앱이 기능을 수행하는데는 문제가 없어보입니다. 하지만 일반적인 웹앱은 데이터를 영구저장한다는 요구사항을 만족해야합니다. 그렇기 때문에 작성한 데이터를 서버로 보내게되고 서버의 데이터베이스에 저장된 영속성 데이터를 조회하여 화면에 그려주는 것이 일반적인 흐름입니다.&#x20;

이번시간에 만든 투두리스트는 DOM을 다루는 코드에 할일이라는 도메인이 강하게 결합되어있습니다. 단순한 예제로는 충분하지만 추후에 서버에서 데이터를 가져오는 방식으로 개선되어야 한다면 현재 방식에선 상당한 어려움을 겪을 거에요.&#x20;

다음 시간에는 작성된 소스코드에서 할일 도메인을 다루는 로직을 추출하여 변경해보도록 하겠습니다.
