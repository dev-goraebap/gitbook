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
    visible: false
---

# 점진적인 클린아키택처를 위한 설계

<figure><img src="../.gitbook/assets/README (1).png" alt=""><figcaption></figcaption></figure>

![thumbnail](https://github.com/dev-goraebap/progressive-clean-architecture/blob/develop/public/README.png)

아래 샘플 소스코드가 있습니다.

{% embed url="https://github.com/dev-goraebap/progressive-clean-architecture" %}

### Module Public API 참조 규칙

`system module` 하위의 각각의 폴더(`module`)들은 모두 `index 파일 (public api)`을 작성해야합니다. 참조 방향성은 단방향이며, 상위 레이어의 모듈들은 동일한 레이어 내의 모듈과 하위 레이어 모듈의 `index 파일(public api)`를 참조할 수 있습니다.

`system module`은 `src` 폴더 하위에 위치하며, 다음과 같은 폴더들로 구성됩니다:

* **app** : 앱의 설정파일들이 위치합니다. `app`, `feature`, `shared` 시스템 레이어 하위 모듈들의 `public api`를 참조할 수 있습니다.
* **features** : 앱의 요구사항을 만족하는 도메인 모듈들이 위치합니다. `features`, `shared` 레이어 하위 모듈들의 `public api`를 참조할 수 있습니다.
* **shared** : 시스템 내에서 공유될 수 있는 설정, 유틸, 서비스, 타입 등이 위치할 수 있습니다. `shared` 레이어 하위 모듈들의 `public api`를 참조할 수 있습니다.

### Feature System Module

features 폴더는 여러 도메인 모듈(Domain Module)로 구성되어 있습니다. 그리고 도메인 모듈들은 각각 다음과 같은 하위 폴더(Layer)로 구성됩니다:

#### 레이어 구조

* **api (presentation layer)**: 사용자 인터페이스와의 상호작용을 처리하는 폴더입니다.
* **app (usecase layer)**: 애플리케이션의 비즈니스 로직을 처리하는 폴더입니다.
* **domain (domain layer)**: 시스템의 핵심 비즈니스 규칙과 로직을 정의하는 폴더입니다.
* **infra (infrastructure layer)**: 선택적인 레이어로, 데이터베이스와 같은 외부 시스템과의 통신을 처리하는 폴더입니다.

#### 레이어 설명

#### api (presentation layer)

* **목적**: 사용자 인터페이스와의 상호작용을 처리합니다.
* **구성 요소(예)**:
  * **controllers**: HTTP 요청을 처리하는 컨트롤러 등을 포함합니다.
  * **events**: 다른 도메인 모듈에게 필요한 기능 요청을 받을 수 있습니다.

#### 예시

```plaintext
src/
├── modules/
│   ├── post/
│   │   ├── api/
│   │   │   ├── controllers/
│   │   │   │   ├── post.controller.ts
│   │   │   ├── events/
│   │   │   │   ├── post.event.ts
```

#### app(usecase layer)

* **목적**: 애플리케이션의 비즈니스 로직을 처리합니다.
* **구성요소**:
  * **usecase**: 특정 비즈니스 작업을 수행하는 유즈케이스를 포함합니다.
  * **dto**: 데이터 전송 객체를 포함합니다. 이는 계층 간 데이터 전달을 담당합니다.
  * **interface**: (리팩토링 시 필요) 유즈케이스에서 외부 인프라의 기능을 사용하기 위한 인터페이스들을 정의합니다. 인프라 레이어의 어뎁터들은 이 인터페이스를 구현하여 유즈케이스와 간접적으로 상호작용합니다.

**예시**

```plaintext
src/
├── modules/
│   ├── post/
│   │   ├── app/
│   │   │   ├── dto/
│   │   │   │   ├── create-post.dto.ts
│   │   │   │   ├── update-post.dto.ts
│   │   │   │   ├── res-post-detail.dto.ts
│   │   │   │   ├── res-post-list.dto.ts
│   │   │   ├── usecases/
│   │   │   │   ├── get-post.usecase.ts
│   │   │   │   ├── create-post.usecase.ts
│   │   │   │   ├── update-post.usecase.ts
│   │   │   │   ├── remove-post.usecase.ts
│   │   │   ├── interface/
│   │   │   │   ├── post.repository.ts
```

#### domain(domain layer)

* **목적**: 시스템의 핵심 비즈니스 규칙과 로직을 정의합니다.
* **구성요소**:
  * **entity**: 도메인 엔티티를 포함합니다.
  * **value-object**: 값 객체를 포함합니다.

```plaintext
src/
├── modules/
│   ├── post/
│   │   ├── domain/
│   │   │   ├── entities/
│   │   │   │   ├── post.entity.ts
│   │   │   │   ├── post-image.entity.ts
│   │   │   ├── vo/
│   │   │   │   ├── create-post.vo.ts
```

#### infra(infrastructure layer)

* **목적**: 데이터베이스와 같은 외부 시스템과의 통신을 처리합니다.
* **구성요소**:
  * **repository**: 데이터베이스와의 상호작용을 처리하는 리포지토리를 포함합니다.
  * **orm-entity**: (리팩토링시 필요) ORM 엔티티를 포함합니다. 이는 데이터베이스 테이블과 매핑됩니다.
  * **mapper**: (리팩토링시 필요) 도메인 엔티티와 ORM 엔티티 간의 변환을 처리하는 매퍼를 포함합니다.

```plaintext
src/
├── modules/
│   ├── post/
│   │   ├── infra/
│   │   │   ├── repositories/
│   │   │   │   ├── post.repository.ts
│   │   │   ├── entities/
│   │   │   │   ├── post.orm-entity.ts
│   │   │   │   ├── post-image.orm-entity.ts
│   │   │   ├── mappers/
│   │   │   │   ├── post.mapper.ts
```

#### 레이어 내부 구조 자율성

각각의 `Layer`는 해당 레이어의 기능을 수행하는 파일들로 구성됩니다. 각 파일들은 역할에 따라 분류될 수 있으며, 이때 하위 폴더를 구성하는 것은 자율적이고 선택적입니다. 예를 들어, 레이어 내에 비슷한 성격의 파일이 한 개만 존재하는 경우에는 굳이 하위 폴더를 만들지 않아도 됩니다.

#### 예시

```plaintext
src/
├── modules/
│   ├── post/
│   │   ├── infra/
│   │   │   ├── entities/
│   │   │   │   ├── post.orm-entity.ts
│   │   │   │   ├── post-image.orm-entity.ts
│   │   │   ├── post.repository.ts
│   │   │   ├── post.mapper.ts
```

#### 도메인 모듈간 기능 참조

각각의 도메인 모듈들은 필연적으로 다른 도메인 모듈의 기능(유즈케이스, 유틸 서비스 등)을 필요로 하게 됩니다. 이 때 제공되는 `EventBus`를 통해 도메인 모듈간의 참조 결합도를 낮출 수 있습니다.

* 기능을 제공하는 도메인 모듈은 `presentation layer`에 이벤트 요청을 받을 수 있는 프레젠터를 준비합니다.
* 외부 도메인의 기능을 참조하는 도메인 모듈은 `eventBus`를 통해 외부 모듈과 상호작용합니다.
* 추가적으로 다음과 같은 방식도 고려할 수 있습니다:
  * 내부적인 HTTP 통신을 통한 상호작용
  * gRPC를 사용한 상호작용

### 클린아키택처로 점진적인 리팩토링 가이드

현재 설계된 방식에서 도메인 모듈들은 `typeorm`에 상당히 의존적입니다. 하지만 리포지토리는 별도의 인터페이스를 두지 않았습니다. 리포지토리를 의존 역전 시키더라도 도메인 엔티티 자체가 `typeorm`에 의존적이기 때문에, 둘 다 유즈케이스와 분리해주지 않는 한 리포지토리만 인터페이스를 만드는 것은 의미가 없다고 생각하였습니다. (추후에 유즈케이스를 단위 테스트한다고 하더라도, 테스트 라이브러리가 리포지토리 구현체를 가짜로 목킹하는 것이 가능하기 때문에 이 부분도 괜찮다고 판단)

위의 부분을 허용한 이유는 `application` 레이어에서 `infrastructure` 레이어를 완벽히 분리하기 위한 비용이 많이 들어가기 때문입니다. 그리고 Node.js 진영에서 `typeorm`을 대체할 ORM은 많지 않기 때문에 기술 스택이 바뀌는 일은 향후 몇 년간은 없을 것이라 판단하였습니다. 만약 `infrastructure` 레이어의 변화가 예측된다면, 프로젝트는 다음과 같은 방식으로 리팩토링을 할 수 있습니다.

* `application layer`에서 사용하는 외부 라이브러리의 사용을 최소화합니다.
* `application layer`에서 `interfaces` 폴더를 만들어 리포지토리 인터페이스를 정의하고, 유즈케이스들은 인터페이스에 의존합니다.
* `domain layer`에서 사용하는 외부 라이브러리의 사용을 최소화합니다.
* `domain layer` 엔티티에서 `typeorm` 의존성을 제거합니다.
* `infrastructure layer`에서 `adapters` 폴더를 만들어 ORM을 사용하는 리포지토리가 인터페이스를 구현하도록 합니다.
* `infrastructure layer`에서 `orm-entities` 폴더를 만들어 ORM을 사용하는 엔티티를 구현합니다.
* `infrastructure layer`에서 `mappers` 폴더를 만들어 도메인 엔티티와 ORM 엔티티 간의 변환을 처리하는 매퍼를 작성하고, 리포지토리에서 사용합니다.
