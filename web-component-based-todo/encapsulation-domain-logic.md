---
description: 도메인 로직을 캡슐화하고 어떤 이점이 있는지 알아보도록 하겠습니다.
---

# 3. 도메인 로직 캡슐화

{% embed url="https://github.com/dev-goraebap/from-js-to-angular/tree/ch1.3-domain-encapsulation" %}

### 기존 코드의 문제점

할일 도메인을 다루는 코드의 일부 내용을 살펴볼게요. 아래 generateId와 todoList 변수들 입니다.

```javascript
let generateId = 1;
let todoList = [];
```

generateId는 할일 데이터가 생성될 때 사용되는 데이터입니다. 데이터 생성 이후 값이 증가되어 할일 데이터의 유일한 식별자의 역할을 해주고 있죠.&#x20;

그런데 위 변수들은 global 영역에 작성되어있습니다. 즉, 어디서든 접근이 가능한 상태이고 마음만 먹으면 접근하여 값을 변경할 수 도 있죠. 이 값이 의도하지 않게 바뀌게 된다면 todoList 에 존재하는 할일 데이터의 id와 중복이 발생할 수도 있고, 중복이 발생하면 예상하지 못한 에러가 발생하게 되겠죠.

todoList 배열 역시 마찬가지 입니다. 이 값은 만들어 두었던 행동 함수들을 통해서만 값이 조작되어야하지만, 현재로써는 원본 데이터에 접근이 가능하기 때문에 배열 자체를 조작하는 것도 어려운 일은 아닙니다.

누가 멍청하게 그런 짓을 하겠냐고 생각하실 수 있지만, 코드는 시간이 지남에 따라 증가하게되며 코드량이 증가한다는 것은 점점 파악하기 어려워진다는 것을 의미합니다. 미래의 나 자신 또는 팀원이 실수를 하지 않을거라는 보장이 없다는 것이죠.&#x20;

### 어떻게 해결할 수 있을까?

예전부터 지금까지 널리 사용되고 있는 [객체지향 프로그래밍](https://app.gitbook.com/o/WgNOYUrHrab7S1aB9l5N/s/uI0XIhcGxtvC1BLQtXVB/) 방법론은 캡슐화 원칙을 통해 이러한 문제점을 해결합니다. 이 커리큘럼은 객체지향을 배우는 과정이 아니기 때문에 이 부분에 대해 자세히 이야기 하지는 않겠지만 앞으로 작성해야할 클래스 문법에 익숙하지 않다면, 아래 영상을 보고 오시면 충분할거라고 생각됩니다.

{% embed url="https://www.youtube.com/watch?v=_DLhUBWsRtw&t=253s" %}

```javascript
class TodoService {
    #generateId = 1;
    #todoList = [];

    get todoList() {
        // git에 작성된 소스코드
        // return [...this.#todoList]; 
        // get 함수는 읽기 전용 값으로 리턴하기 때문에 
        // 새로운 배열을 반환할 필요가 없음
        return this.#todoList; 
    }

    addTodo(content) {
        this.#todoList.push({
            id: this.#generateId++,
            content,
            completed: false
        });
    }
    
    checkTodo(id) {
        this.#todoList = this.#todoList.map(todo => {
            if (todo.id !== id) {
                return todo;
            }
            return {
                ...todo,
                completed: !todo.completed
            };
        });
    }
    
    removeTodo(id) {
        this.#todoList = this.#todoList.filter(todo => todo.id !== id);
    }
}
```

이전에 할일에 관련된 로직들이 TodoService라는 클래스 내부에 코드가 위치하게 되었습니다.&#x20;

generateId 와 todoList 속성명에 [#키워드를 붙여 외부에서 접근할 수 없게](../gpt-docs/oop/access-modifier.md) 만들었습니다. 덕분에 외부에서는 숨긴 속성들에 접근하지 못하기 때문에 값을 잘못 조작하는 일은 발생하지 않는데요.  그렇다면 todoList 값에 접근하여 화면을 랜더링하는 부분은 어떻게 할 수 있는 걸까요.&#x20;

```javascript
get todoList() {
    return this.#todoList; 
}

// 또는 
getTodoList() {
    return [...this.#todoList];
}
```

소스코드 7번째 줄에 get todoList 라는 함수가 작성되어있는데, 이 값은 todoList 속성을 반환합니다. 조금 특별한 방식인데 [자바스크립트에서 제공하는 getter, setter](../gpt-docs/oop/class-get-set.md) 함수를 작성하는 방법입니다. getter 함수는 setter가 없을 경우 값을 읽기전용으로 제공하기 때문에 이 값을 수정할 수 없습니다.&#x20;

getter를 쓰지 않고 일반 메서드로 값을 제공한다면 리턴값에 접근하여 값을 수정하는 것이 가능한데, 위와 같이 새로운 배열을 만들어 반환하면 원본 값을 수정할 수 없기 때문에 똑같은 효과를 줄 수 있죠.&#x20;

### 개선된 내용

할일 데이터와 그 데이터를 조작하는 관련된 기능들을 하나의 클래스로 묶고, 외부에서 직접 조작되길 원치 않는 속성들을 숨기고 별도의 인터페이스(여기서는 메서드) 를 노출하였습니다. 이를 통해 데이터와 기능을 하나의 단위로 묶어 관리할 수 있으며, 코드의 가독성과 유지보수성을 향상시킬 수 있습니다.&#x20;

또한 외부에 노출 시킬 필요 없는 값을 숨김으로써 외부에서 잘못된 방법으로 데이터를 변경할 위험이 줄어듭니다. 클래스를 사용하는 개발자들은 내부 구현을 몰라도 공개된 메서드의 이름과 사용 방법만을 알면 됩니다. 이로 인해 코드의 사용성이 좋아지고, 다른 개발자들이 코드를 이해하고 사용하기 쉬워집니다.

### 마무리하며

할일 도메인과 관련된 기능들은 TodoService라는 클래스로 잘 캡슐화 하였는데, 다른 코드들도 클래스로 만들 수 있을까요?&#x20;

다음 시간에는 DOM을 조작하는 코드들을 역할에 맞게 나누고 각각 클래스로 만들어보는 시간을 가져보도록 할게요.&#x20;
