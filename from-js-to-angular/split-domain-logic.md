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
