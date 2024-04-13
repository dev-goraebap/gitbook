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

# 자바스크립트의 접근 제한자

자바스크립트는 전통적으로 Java나 C# 같은 다른 객체지향 언어에서 볼 수 있는 명시적인 접근 제한자(`private`, `protected`, `public` 등)를 지원하지 않았습니다. 그러나 개발자들은 여러 가지 방법을 사용하여 비슷한 기능을 구현해왔습니다.

**ES6 이전: 클로저를 사용한 접근 제어**

ES6 이전에는 클로저(closures)를 사용하여 변수를 함수 스코프 내에 숨겨 프라이빗하게 만드는 방식이 일반적이었습니다. 이 방법은 외부에서 접근할 수 없는 변수를 생성함으로써 데이터를 숨기는 데 사용됩니다.

```javascript
function Person(name) {
  var privateName = name;  // 프라이빗 변수

  this.getName = function() {
    return privateName;
  };
}

var person = new Person('John');
console.log(person.getName()); // 'John'
console.log(person.privateName); // undefined, 외부에서 접근 불가
```

이 예제에서 `privateName`은 `Person` 함수의 지역 변수로, 외부에서 직접 접근할 수 없습니다. `getName` 메서드는 클로저를 형성하여 `privateName`에 접근할 수 있으며, 이 메서드를 통해서만 `privateName`의 값을 읽을 수 있습니다.

**ES6 이후: 심볼과 WeakMap**

ES6에서는 `Symbol`과 `WeakMap`을 이용한 방법이 추가적인 방법으로 제시되었습니다. `Symbol`은 유일하며, 외부에서 접근할 수 없는 프로퍼티 키를 생성하는 데 사용할 수 있습니다. `WeakMap`은 객체에 대한 키를 가지고 프라이빗 데이터를 저장할 수 있는 구조입니다.

**Symbol 예제**

```javascript
const privateName = Symbol('name');

class Person {
  constructor(name) {
    this[privateName] = name;
  }

  getName() {
    return this[privateName];
  }
}

const person = new Person('John');
console.log(person.getName()); // 'John'
console.log(person[privateName]); // 직접 접근할 수 없음
```

**WeakMap 예제**

```javascript
const privateProperties = new WeakMap();

class Person {
  constructor(name) {
    privateProperties.set(this, {name: name});
  }

  getName() {
    return privateProperties.get(this).name;
  }
}

const person = new Person('John');
console.log(person.getName()); // 'John'
console.log(person.privateProperties); // undefined, 외부에서 접근 불가
```

**ES2022: 클래스 필드 선언**

ES2022에서는 클래스 필드에 `#`을 사용하여 프라이빗 필드를 정의할 수 있게 되었습니다. 이것은 JavaScript에 명시적인 접근 제한자를 제공하는 첫 번째 기능이며, 이전의 방법들보다 간단하고 명확합니다.

```javascript
class Person {
  #name;

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name;
  }
}

const person = new Person('John');
console.log(person.getName()); // 'John'
console.log(person.#name); // Syntax Error, 외부에서 접근 불가
```

이 예제에서 `#name`은 클래스 내부에서만 접근 가능한 프라이빗 필드입니다. 이렇게 자바스크립트는 시간이 지나면서 점점 더 강력한 캡슐화 기능을 지원하게 되었습니다.

### 암묵적인 룰 언더스코어(\_)

자바스크립트에서 `_` (언더스코어)를 변수명 앞에 붙이는 것은 오랜 기간 동안 비공식적으로 사용된 관례입니다. 이는 변수나 메서드가 "프라이빗" 또는 "보호된" 것으로 간주되어야 함을 나타내기 위해 사용됩니다. 그러나 이것은 JavaScript 언어의 공식적인 기능은 아니며, 단지 개발자들 사이의 약속에 불과합니다.

\
