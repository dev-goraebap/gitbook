# DOM 기반의 투두리스트 만들기

[아래  깃허브에 접속하셔서 코드를 보면서 글을 읽어주시면 좋을 것 같아요. ](#user-content-fn-1)[^1]

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.1-make-based-element" %}

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

```javascript
const todoFormElement = document.querySelector('#todoForm');
const todoViewElement = document.querySelector('#todoView');
```

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

[^1]: 
