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

# 자바스크립트의 getter, setter

자바스크립트에서는 ES6 (ECMAScript 2015)부터 클래스가 공식적으로 도입되었고, 클래스 내에서 getter와 setter를 사용할 수 있게 되었습니다.&#x20;

#### Getter와 Setter

Getter와 Setter는 객체의 속성을 간접적으로 조작할 수 있게 해주는 메서드입니다. 이들은 객체의 데이터를 읽고(`get`) 수정(`set`)하는 방식을 정의하여 데이터의 유효성 검사, 계산 또는 변형을 수행할 수 있게 합니다.

* **Getter**는 클래스에서 특정 값을 읽을 때 자동으로 호출되는 메서드입니다. 외부에서는 속성 접근처럼 보이지만 내부적으로 메서드를 통해 처리됩니다.
* **Setter**는 값을 설정할 때 호출되는 메서드로, 외부에서는 속성에 값을 할당하는 것처럼 작동하지만, 실제로는 메서드가 처리합니다.

#### ES2022 예시

아래 코드는 ES2022의 새로운 `#` 프라이빗 필드 표기법을 사용한 클래스에서 getter와 setter를 정의하는 방법을 보여줍니다:

```javascript
class Person {
  #firstName;
  #lastName;

  constructor(firstName, lastName) {
    this.#firstName = firstName;
    this.#lastName = lastName;
  }

  // Getter for fullName
  get fullName() {
    return `${this.#firstName} ${this.#lastName}`;
  }

  // Setter for fullName
  set fullName(name) {
    [this.#firstName, this.#lastName] = name.split(' ');
  }
}

const person = new Person('John', 'Doe');
console.log(person.fullName); // 'John Doe'
person.fullName = 'Jane Smith';
console.log(person.fullName); // 'Jane Smith'
```

이 예에서 `#firstName`와 `#lastName`은 클래스 내부에서만 접근 가능한 프라이빗 필드입니다. `fullName`을 통해 이 필드들을 간접적으로 읽거나 수정할 수 있으며, 외부에서는 직접 접근할 수 없습니다. 이로써 데이터를 보다 안전하게 관리할 수 있습니다.
