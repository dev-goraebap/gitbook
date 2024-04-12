---
description: >-
  이전 시간에 간단하게 자바스크립트를 통해 투두앱을 만들었고 할일이라는 도메인이 DOM 을 다루는 로직에 강하게 결합되었다고 이야기하였습니다.
  이번 시간에는 기존의 코드에서 할일 데이터를 다루는 로직을 분리하여 코드가 어떻게 개선되는지 알아보도록 할게요.
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 2. 코드에서 할일 도메인 로직 추출하기

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.2-split-dom-and-domain" %}

### 코드 살펴보기

index.js 파일의 84 번째 줄부터 todoList 라는 할일 목록을 다루는 코드들이 별도로 추가되었습니다.&#x20;

```javascript
// =================== TODO DOMAIN CODE  =================== //

let generateId = 1;
let todoList = [];

const addTodo = (content) => {
    todoList.push({
        id: generateId++,
        content,
        completed: false
    });
}

const checkTodo = (id) => {
    todoList = todoList.map(todo => {
        if (todo.id !== id) {
            return todo;
        }
        return {
            ...todo,
            completed: !todo.completed
        };
    });
}

const removeTodo = (id) => {
    todoList = todoList.filter(todo => todo.id !== id);
}
```

generateId는 todoList의 값이 담길 때마다 고유한 식별자 키로 쓰일 값입니다. 이 값은 할일 데이터가 추가될 때마다 값을 증가시켜 점차 증가하게 됩니다.&#x20;

todoList는 할일 데이터를 담아둘 배열입니다. addTodo, checkTodo, removeTodo 함수들을 통해 todoList의 값을 증가, 수정, 삭제되게 됩니다.&#x20;

할일을 다루는 코드가 추출됨에 따라 기존의 코드들이 일부 수정되게 됩니다.&#x20;

```javascript
const createTodoItem = (todo) => {

    const checkBoxElement = document.createElement('input');
    checkBoxElement.type = 'checkbox';
    checkBoxElement.checked = todo.completed;
    checkBoxElement.addEventListener('click', () => {
        const checkEvent = new CustomEvent('CHECK_TODO', {
            detail: { id: todo.id }
        });
        document.dispatchEvent(checkEvent);
    });

    const contentElement = document.createElement(todo.completed ? 'del' : 'span');
    contentElement.innerText = todo.content;

    const removeButtonElement = document.createElement('button');
    removeButtonElement.innerText = '삭제';
    removeButtonElement.addEventListener('click', () => {
        const removeEvent = new CustomEvent('REMOVE_TODO', {
            detail: { id: todo.id }
        });
        document.dispatchEvent(removeEvent);
    });

    const todoItemElement = document.createElement('li');
    todoItemElement.appendChild(checkBoxElement);
    todoItemElement.appendChild(contentElement);
    todoItemElement.appendChild(removeButtonElement);

    return todoItemElement;
}
```

createTodoItem 함수의 파라미터로 todo라는 값이 전달됩니다. 자바스크립트라서 어떤 타입인지 명시가 안되어 있지만 다음같은 타입의 값이 넘어 온다고 생각하시면 됩니다.

```javascript
{
    id: 1,
    content: '할일 택스트',
    completed: false
}
```

채크박스는 todo 객체의 completed 값의 상태에 따라 채크 표시 여부가 달라지게 됩니다. 컨텐츠 요소 또한 completed 값의 상태에 따라 span 또는 del 태그로 변경되어 취소선을 나타낼지 정하게 됩니다.&#x20;

크게 변경된 부분은 채크박스와 삭제 버튼의 이벤트리스너가 DOM을 조작하는 것이 아니라 새로운 이벤트를 발행하고 있다는 것인데요.&#x20;

```javascript
// checkbox click ...
const checkEvent = new CustomEvent('CHECK_TODO', {
    detail: { id: todo.id }
});
document.dispatchEvent(checkEvent);

// remove button click ...
const removeEvent = new CustomEvent('REMOVE_TODO', {
    detail: { id: todo.id }
});
document.dispatchEvent(removeEvent);
```

생소해 보일 수도 있는 CustomEvent는 자바스크립트의 표준 기술 입니다. GPT선생님께서 CustomEvent 클래스에 대해 다음과 같이 정의 하였습니다.

**자바스크립트의 `CustomEvent`는 개발자가 직접 정의할 수 있는 이벤트 유형으로, 표준 이벤트와 마찬가지로 이벤트 리스너를 통해 처리할 수 있는 사용자 지정 이벤트 객체를 생성하는 데 사용됩니다. 이것은 특히 웹 애플리케이션에서 컴포넌트 간에 특정 상황이나 데이터를 전달할 때 유용합니다.**

CustomEvent 생성자의 두번째 인자에 전달하는 객체의 detail은 이벤트를 감지하는 리스너에 값을 전달하는 방법이라고 생각해주시면 될 것 같아요.

두 이벤트는 서로 다른 이름의 이벤트를 생성하고 있지만 공통적으로 document 요소의 dispatchEvent 를 통해 발행되고 있습니다. 그렇기 때문에 document에 이벤트리스너를 추가하고 해당 이벤트 이름을 명시하면 이벤트가 발행되는 것을 감지하고 로직을 작성할 수 있습니다.&#x20;

submit 이벤트를 감지하는 리스너 역시 마찬가지입니다.

```javascript
todoFormElement.addEventListener('submit', event => {
    // other code...
    
    const addEvent = new CustomEvent('ADD_TODO', {
        detail: { content }
    });
    document.dispatchEvent(addEvent);

    // other code...
});
```

createTodoItem 함수를 호출하는 대신에 ADD\_TODO 이벤트를 발행하고 작성한 텍스트만 전달하고 있습니다.&#x20;

그리고위에서 언급했던 커스텀 이벤트들은 다음과 같이 대기중인 이벤트 리스너를 통해 이벤트가 실행되게 됩니다.&#x20;

```javascript
document.addEventListener('ADD_TODO', event => {
    const content = event.detail.content;
    addTodo(content);
    renderTodoView();
});

document.addEventListener('CHECK_TODO', event => {
    const todoId = event.detail.id;
    checkTodo(todoId);
    renderTodoView();
});

document.addEventListener('REMOVE_TODO', event => {
    const todoId = event.detail.id;
    removeTodo(todoId);
    renderTodoView();
});
```

각각의 이벤트로부터 전달 받은 값을 활용하여 할일 도메인을 생성, 수정, 삭제하는 행동 함수들을 호출하고 있는 부분입니다. 행동함수를 실행한 이후에는 renderTodoView라는 함수를 호출하고 있는데요.

```javascript
const renderTodoView = () => {
    todoViewElement.innerHTML = '';

    todoList.forEach(todo => {
        const todoItemElement = createTodoItem(todo);
        todoViewElement.appendChild(todoItemElement);
    });
}
```

renderTodoView 함수는 todoViewElement의 내용을 초기화하고 할일 목록 데이터를 담고 있는 todoList를 순회하며 할일 아이템 요소를 todoViewElement 요소에 차곡차곡 생성하는 일을 하고있습니다.

### 개선된 내용

언뜻 보기엔 코드량이 더 많아져서 개선된게 맞는지 의문이 들 수 있습니다. 확실히 코드는 더 많아졌지만 할일 도메인 데이터를 다루는 부분을 분리했기 때문에 이 코드는 훨씬 유연하게 변경사항을 대처할 수 있게됩니다.&#x20;

예를 들어 서버로부터 값을 가져온다던가, 생성한 데이터를 서버로 보내는 작업들로 코드를 변경해야된다고 한다면 할일 도메인을 다루는 로직을 일부 수정하고 수정된 방식대로 코드를 조금 수정만 해주면 될 것 같아요.&#x20;

### DOM 과 Domain 로직 분리를 하기 위한 기술

기존의 DOM 영역에서 Domain 로직을 추출하는 것에 CustomEvent 기능은 필수 사항이 아닙니다. 최대한 두 기능사이의 결합도를 낮추기 위해 자바스크립트 표준 기술을 사용했지만, 이 코드를 작성하기 전에는 직접 순수 프로그래밍 코드를 통해 옵저버 패턴등을 구현하여 해당 문제를 해결했습니다. (네이티브에서 만들어진 코드만큼 깔끔하지 못했습니다)&#x20;

도메인에 대한 로직을 분리하고 DOM을 제어하는 로직과 상호작용할 수 있다면 어떤 방식이던 상관없이 작성해볼 수 있을 것 같아요. 더 좋은 방법을 알고 계시면 공유도 부탁드립니다..ㅎ

### 아직 더 개선될 점

해당 코드들은 각각의 연관된 관심사에 따라 코드가 분리되었습니다. 하지만 일부 문제가 될만한 내용들이 있는데, 다음 시간에 이 부분에 대해서 알아보고 개선하는 시간을 가져보도록 하겠습니다.
