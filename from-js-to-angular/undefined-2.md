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

# 투두리스트 - 클래스 사용

이전에 함수 방식으로 코드들을 작성하였습니다. 하나의 파일에 작성된 코드들은 어떤 관심사에 연관된 코드 인지로 분류할 수 있을 것 같아요.

* 할일 도메인
* DOM 이벤트, 제어

이번 글에서는 할일 도메인에 대한 내용을 class 를 사용하여 코드를 캡슐화하고 필요한 기능만 노출시켜 기능이 오작동 되는 잠재적인 문제까지 개선해보도록 하겠습니다.

class 를 다루는 방식에 대해서는 따로 작성하지 않습니다. 인터넷에 너무 많고 좋은 강의들이 넘처나기 때문이죠.. 10분에서 30분 정도만 투자하면 되기 때문에 class 문법이 익숙하지 않다면 취향에 맞는 강의를 찾아보고 오시면 좋을 것 같아요. 아래 드림코딩에서 소개하는 이 영상 하나로도 아래 내용을 이해하는데는 문제가 없다고 생각됩니다.

{% embed url="https://www.youtube.com/watch?v=_DLhUBWsRtw" %}

먼저 할일에 관련된 로직은 TodoService 라는 클래스를 만들어 캡슐화 하였습니다.

```javascript
class TodoService {

    #todoList = [];
    
    getTodoList() {
        return [...this.#todoList];
    }

    addTodo(content) {
        if (!content) {
            window.alert('내용을 입력해 주세요.');
            return;
        }
    
        const generateId = new Date().getTime().toString();
    
        this.#todoList.push({
            id: generateId,
            content,
            finished: false,
        });
    }

    checkTodo(id) {
        this.#todoList = this.#todoList.map((todo) => {
            if (todo.id !== id) {
                return todo;
            }
            return { ...todo, finished: !todo.finished };
        });
    }

    removeTodo(id) {
        this.#todoList = this.#todoList.filter((todo) => todo.id !== id);
    }
}
```

클래스 바로 아래 작성된 todoList 배열에 # 키워드가 붙은 것을 볼 수 있는데요. ES10 부터 도입된 자바스크립트의 private 키워드 입니다.

{% embed url="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/Private_properties" %}

Java, C# 과 같은 객체지향 프로그래밍 언어들은 class 영역 내부의 속성 또는 메서드에 접근제한자라고 하는 키워드(public, private, protected 등)를 사용하여 외부에 공개할지 숨길지를 결정할 수 있습니다.

아쉽게도 es10 이전에 자바스크립트의 클래스는 접근제한자 키워드가 따로 존재하지 았았으며  class의 속성, 메서드들은 모두 외부에서 접근 가능한 상태로 쓰이게 됩니다. 객체지향에서 사용되는 캡슐화 개념을 완벽하게 다루기 힘든 것이죠. <mark style="color:orange;">여기서는 연관된 기능들을 묶는것은 성공하였으나 은닉이 필요한 데이터를 숨기지 못했다는 의도로 표현하였습니다.</mark>

{% embed url="https://ko.wikipedia.org/wiki/%EC%BA%A1%EC%8A%90%ED%99%94" %}

물론 자바스크립트의 클로저라는 기술을 통해 숨길 값을 외부로부터 숨기고 개발자가 의도한 기술을 사용하도록 유도할 수는 있습니다.

```javascript
class TodoService {
    constructor() {
        let todoList = []; // 비공개 속성

        this.getTodoList = function() { // 공개 메소드
            // 외부에서 todoList 속성에 직접 접근 못하게하기 위해
            // 복사한새로운 배열을 반환
            return [...todoList]; 
        };
    }
}

const todoService = new TodoService();
console.log(todoService.todoList); // undefined, 외부에서 접근 불가
console.log(todoService.getTodoList()); // []
```

위의 코드는 클로저의 개념을 모르면 파악하기 힘들고 원하지 않는 방향으로 코드를 작성해야 하기에 저는 자바스크립트를 사용해서 클래스를 만들 때는 캡슐화를 포기하는 타협을 보기도 했던 것 같아요.

위 코드는 더 이상 사용할 필요가 없으며 숨겨야하는 속성이나 메서드는 #을 붙여 명시해주기만 하면 됩니다.

```javascript
class TodoService {

    #todoList = [];
    
    getTodoList() {
        return [...this.#todoList];
    }
    
    ...
}
```

위와 같이 코드들을 클래스로 캡슐화하고나면 다음과 같은 이점이 있습니다.

1. 연관된 코드들의 집합
2. 정보은닉

연관된 코드들을 클래스로 캡슐화하는 행동을 반복하다보면 작성했던 코드를 관심사별로 분류하는 능력이 생기고 절차지향식의 코드를 작성하는 방법에서 그룹화한 객체끼리 협력하는 코드를 만들어 낼 수 있습니다.&#x20;

그리고  숨겨야 하는 데이터에 접근제한자 (#)를 사용하여 외부에서 사용하지 못하게 하는 것은 코드에 개발자의 의도를 전달할 수 있다는 점이 크게 작용한다고 생각해요.

사용하려고 했던 속성 todoList에 접근을 제한하지 않았다면 getTodoList 메서드를 만들어준 의미는 사라질거에요. 원본 데이터에 접근하지 못하게 하는 역할로 만들어준건데 접근할 수 있게 되었으니까요.

이후에 DOM에 관련된 코드는 크게 달라진게 없습니다.

```javascript
const todoService = new TodoService();

const formElement = document.querySelector('#todoForm');
const inputElement = document.querySelector('#contentInput');
const containerElement = document.querySelector('#todoContainer');

formElement.addEventListener('submit', (event) => {
    event.preventDefault();

    todoService.addTodo(inputElement.value);

    inputElement.value = '';

    updateUI();
});

containerElement.addEventListener('click', (event) => {
    const targetAction = event.target.getAttribute('data-action');
    const targetTodoId = event.target.getAttribute('data-id');

    switch (targetAction) {
        case 'checkTodo':
            todoService.checkTodo(targetTodoId);
            break;
        case 'removeTodo':
            todoService.removeTodo(targetTodoId);
            break;
        default:
    }

    updateUI();
});

const updateUI = () => {
    const todoList = todoService.getTodoList();
    containerElement.innerHTML = todoList.map((todo) => {
        const contentTag = todo.finished ? 'del' : 'span';
        return `
        <li>
            <input data-action="checkTodo" data-id="${todo.id}" type="checkbox" ${todo.finished ? 'checked' : ''}  />
            <${contentTag}>${todo.content}</${contentTag}>
            <button data-action="removeTodo" data-id="${todo.id}">삭제</button>
        </li>
        `;
    }).join('');
}
```

TodoService를 객체로 생성하고  각각의 이벤트는 todoService 객체에서 공개된 인터페이스(여기서는 메서드) 를 사용하여 작업을 수행하도록 바뀌었습니다.

이제 나머지 Dom 에 관련된 작업들도 class 로  캡슐화 시켜주면 끝날 것 같나요? 다음 글에서 나머지 코드들도 클래스를 적용해보는 것을 다루도록 하겠습니다.&#x20;

이번 글에 대한 소스코드는 아래 링크에서 확인해 볼 수 있어요.

{% embed url="https://github.com/dev-goraebap/learn-angular-loosely/tree/ch1.2-class/apps/vanilla-todo" %}

