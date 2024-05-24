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

# FSD에 관하여

시작하기 전에

개발자로서 고품질 코드를 추구하는 것은 중요한 일이라고 생각됩니다. 고품질 코드는 유지보수가 쉽고, 읽기 쉬우며, 효율적이고, 확장 가능해야 합니다.  (_고품질 코드의 정의는_ [_**디자인패턴의 아름다움**_](https://www.yes24.com/Product/Goods/118859035) _이라는 책에서 찾아 볼 수 있습니다.)_

그런데 이러한 고품질 코드를 작성하는데  방해가 되는 요소가 있습니다. 바로 코드를 관리하는 폴더 (패키지) 간의 의존성 입니다.&#x20;

폴더 구조는 항상 의미를 부여하며 합리적으로 만들었다고 생각되는데 뭔가 찜찜한.. 하지만 뭐가 찜찜한건지 잘 모르는 그런 현상들이 있었습니다. 그러다 언젠가 이 영상을 보게 되었는데요. 6분 50초 쯤으로 넘어가시면 패키지 의존성에 대해 짧게나마 이야기를 하고있습니다. 11분 28초까지 꼭 보시면 좋을 것 같아요.

{% embed url="https://www.youtube.com/watch?v=dJ5C4qRqAgA&t=410s" %}
6분 50초쯤에 패키지 의존성에 대해 다룹니다.
{% endembed %}

클래스 파일을 포함하고 있는 폴더, 즉 패키지 구조 역시 코드의 의존성에 영향을 미칩니다. 패키지 간의 의존성이 복잡하게 얽혀 있으면, 코드를 추적하고 관리하기 어려워집니다.&#x20;

**기능 분할 디자인(FSD)** 를 프로젝트에 적용하게 되면 **레이어 참조 규칙**, **슬라이스  공개  API 규칙**을 통해 위 영상에서 말하는 패키지간의 의존성을 관리하기 쉽게 하는 모범 사례를 만들어 낼 수 있다고 생각합니다.

## 기능 분할 디자인(FSD) 소개

{% hint style="info" %}
공식문서를 보고 FSD에 대해 정리하였습니다. 문서는 Deepl 번역을 사용하여 최대한 사이트가 전달하고자 하는 내용을 정확하게 인지하도록 노력했습니다.

공식 문서: [https://feature-sliced.design/](https://feature-sliced.design/)
{% endhint %}

**Feature Slided Design** 이하 **기능 분할 설계** 는 프런트엔드 애플리케이션을 스캐폴딩하기 위한 아키텍처 방법론입니다. 간단히 말해, 코드 구성에 관한 규칙과 규칙의 모음입니다. 이 방법론의 주된 목적은 끊임없이 변화하는 비즈니스 요구사항에 맞서 프로젝트를 더 이해하기 쉽고 체계적으로 만드는 것입니다.

## FSD의  계층 구조

대부분의 프론트엔드 프로젝트의 폴더 구조를 봤을 때 **src 폴더** 하위부터 작업하는 소스코드의 영역이므로 계층의 시작점은 src 폴더를 기본 경로로 잡고 시작한다고 생각합니다.

참고용 샘플 저장소들 입니다. (레이어의 위치, 구조만 파악해주세요)

<details>

<summary>참고 샘플</summary>

[https://github.com/UmttikhinaDasha/IT-Bookstore](https://github.com/UmttikhinaDasha/IT-Bookstore)

[https://github.com/ruslan4432013/fsd-react-query-example](https://github.com/ruslan4432013/fsd-react-query-example)

[https://github.com/vissh/vkui-audiopad/tree/master/packages/front](https://github.com/vissh/vkui-audiopad/tree/master/packages/front)

</details>

### Layer (레이어)

레이어는 기능 분할 디자인에서 첫 번째 수준의 조직 계층 구조입니다. 현재날짜(2024.05.23) 기준으로 총 6개의 레이어가 있습니다. 프로젝트에서 모든 레이어를 사용할 필요는 없으며, 필요하지 않은 레이어를 억지로 만들 필요는 없습니다.

1. <mark style="background-color:blue;">**app**</mark>
2. <mark style="background-color:purple;">**pages**</mark>
3. <mark style="background-color:orange;">**widgets**</mark>
4. <mark style="background-color:red;">**features**</mark>
5. <mark style="background-color:yellow;">**entities**</mark>
6. <mark style="background-color:green;">**shared**</mark>

_각 레이어의 역할은 가장 마지막에 **슬라이스**와 **세그먼트**를 포함하여 다루도록 하겠습니다._

### 레이어 참조규칙 ✨ <a href="#import-rule-on-layers" id="import-rule-on-layers"></a>

**레이어**의 참조 규칙을 설명하려면 레이어들은 각각 **슬라이스**로 구성된다는 것을 알아야합니다. 슬라이스에 대해서는 아래에서 다루지만, 일단 **레이어 하위의 폴더들**이라고 생각해주세요.&#x20;

{% hint style="info" %}
**슬라이스**의 **모듈**은 다른 슬라이스가 바로 아래 레이어에 있는 경우에만 다른 슬라이스를 가져올 수 있습니다.&#x20;

여기서 말하는 **모듈**은 해당 폴더의 애플리케이션의 내부 모듈(인덱스 파일이 있는 파일 또는 디렉토리)을 의미합니다.

_원문_ [_https://feature-sliced.design/docs/reference/layers#import-rule-on-layers_](https://feature-sliced.design/docs/reference/layers#import-rule-on-layers)
{% endhint %}

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>features layer의 하위 슬라이스들</p></figcaption></figure>

레이어 참조규칙을 적용하면 **auth, user, post**는 모두 **features**라는 같은 레이어의 슬라이스들이기 때문에 서로의 모듈 내 파일을 참조할 수 없습니다.&#x20;

**app, pages, widgets**는 **features**보다 상위 레이어들이기 때문에 당연히 해당 레이어들의 슬라이스들도 접근할 수 없습니다.

그렇다면 **entities, shared** 는 어떨까요? **features** 보다 하위 레이어들이기 때문에 이 경우에는 이 레이어의 슬라이스들에 접근이 가능합니다. _(**shared**는 슬라이스가 존재하지 않습니다. 바로 세그먼트들이 존재하기 때문에, 세그먼트들에 접근한다고 생각해주세요)_

### Slice (슬라이스)

**슬라이스**는 기능 분할 디자인에서 두 번째 수준의 조직 계층 구조입니다. 슬라이스는 애플리케이션의 **비즈니스 도메인**에 따라 만들어 집니다. 즉 프로젝트의 요구사항에 따라 생기기 때문에 레이어처럼 사전에 명명되어있지 않고 **비즈니스 도메인** 에 맞는 이름을 개발자가 직접 작성하게 됩니다.

{% hint style="info" %}
예를 들어 진행하고 있는 프로젝트가 게시판 사이트라면

**인증, 사용자, 게시물, 댓글, 태그, 추천** 등을 비즈니스 도메인들로 분류 할 수 있고 이 명세들이 각각의 슬라이스가 될 수 있습니다.

**auth, user, post, comment, tag, recomend ...**

\
물론 모든 레이어가 위 슬라이스 이름으로 구성되는 것은 아닙니다.&#x20;

* 모든 레이어들은 하위 폴더로 슬라이스들을 가지지만 예외로 **app, shared 레이어는 슬라이스를 가지지 않습니다.** 자세한 내용은 가장 마지막에 다루겠습니다.
* 공식문서를 보면 도메인에 대한 **단일 도메인 명시(post)** 뿐만 아니라 어떤 레이어에 위치하는지에 따라 **도메인 + 동사 (create-post)**, **도메인 + 명사 (post-list-page)**  등 도메인이 포함되어 있기만 하면 슬라이스라고 하는 것 같습니다.
{% endhint %}

### _슬라이스_ 공개 API 규칙 ✨

슬라이스 내부에서는 코드를 매우 자유롭게 구성할 수 있으며, 슬라이스에서 좋은 공개 API를 제공하는 한 아무런 문제가 발생하지 않습니다.&#x20;

공개 API를 만들기 위해서는 슬라이스의 경로에 **index** 파일을 만들어 두는 것 입니다. 외부에서 사용 될 수 있는 파일들만 이 index 파일에 명시하며, 외부에서는 index에 명시되지않은 파일엔 접근할 수 없습니다.&#x20;

이로써 패키지간에 **캡슐화 원칙**을 적용하게 됩니다.

FSD에서는 슬라이스 공개 API 규칙을 통해 다음과 같은 이점을  달성할 수 있다고 이야기합니다.

{% hint style="info" %}
1. 애플리케이션은 개별 모듈의 내부 구조가 **변경되지 않도록 보호되어야** 합니다.
2. 모듈의 내부 구조 처리는 다른 모듈에 **영향을 미치지 않아야** 합니다.
3.  모듈 동작의 중요한 변경 사항은 **쉽게 감지할 수**있어야 합니다.

    > 모듈**동작의 중대한 변경** - 모듈의 사용자 주체의 기대를 깨는 변경 사항입니다.



본문: [https://feature-sliced.design/docs/reference/public-api#goals](https://feature-sliced.design/docs/reference/public-api#goals)
{% endhint %}

### 밀접한 슬라이스들 그룹화

<figure><img src="../.gitbook/assets/graphic-nested-slices-b9c44e6cc55ecdbf3e50bf40a61e5a27.svg" alt=""><figcaption></figcaption></figure>

FSD 공식문서는 **compose, like, delete** 들이 슬라이스라고 말하고 있습니다. **features**가 레이어이기 때문에 하위에 **post**가 분명 슬라이스가 맞는데.. 저는 이 부분이 특히 이해하기 힘들었는데, 여기서는 제 생각이 크게 작용하는 글입니다.

{% hint style="warning" %}
#### 개인적인 해석

&#x20;features 레이어 하위에 다음과 같은 슬라이스 들이 존재한다고 가정합니다.

* compose-post (개시물 작성)
* like-post (게시물에 좋아요 누르기)
* delete-post (게시물 삭제)&#x20;

위 3개의 슬라이스들은 모두 post 라는 비즈니스 도메인에 종속된 기능들 입니다. 따라서 위 슬라이스들은 post 라는 슬라이스로 그룹화 되고 post라는 도메인명을 부모 슬라이스가 명시하고 있기 때문에 post를 이름에 굳이 포함할 필요가 없게 됩니다.&#x20;

따라서 **post/compose, post/like, post/delete** 라는 하위 슬라이로 표현되는게 아닐까 싶습니다.
{% endhint %}

나름 타당성이 있는것은 **슬라이스 공개 API 규칙**에 따라 슬라이스 내에서 공개 API만 잘 노출하면 내부의 코드를 자유롭게 구성할 수 있다는 점 때문에 **밀접한 슬라이스 그룹화** 또한 가능한 부분인지 생각이 됩니다.

대신 FSD 공식문서에서는 **밀접한 슬라이스 그룹화**를 적용할 경우 하위 슬라이스들도 **레이어 참조 규칙에** 따라 격리되기 때문에 **서로 코드를 공유해서는 안된다고 말하고 있습니다.**

위 내용은[ 공식문서 슬라이스 파트 ](https://feature-sliced.design/docs/reference/slices-segments#slices) 다루는 부분입니다. 다들 한번씩 참고하시고 제가 해석한 내용이 맞는지 스스로 판단하는게 좋을 것 같습니다.

### Segment (세그먼트)

**세그먼트**는 기능 분할 디자인에서 세 번째이자 마지막인  조직 계층 구조입니다. **슬라이스**의 하위에 구성되며 비즈니스 도메인(슬라이스) 를 구성하는 단위 입니다.&#x20;

세그먼트들은 기술적인 특성에 따라 분류되는데 개발자가 직접 이름을 작성할 수 있지만 **표준화된 세그먼트명**들도 존재합니다.

* **ui** - ui 구성요소(컴포넌트로 이해하면 될 것 같습니다)
* **model** - 비즈니스 로직 및 데이터 저장소, 데이터를 조작하는 함수 ( 상태를 다루거나 api를 호출하고 연계하는 클래스 또는 함수들이 위치할 것 같습니다)
* **lib** - 보조 및 인프라 코드 ( 외부 third party 라이브러리 설정 등이 위치할 것 같습니다)
* **api** - 외부 api와의 통신 코드 ( api를 호출하는 클래스 또는 함수들이 위치할 것 같습니다)

물론 위 세그먼트들 역시 필요할 때 작성하면 됩니다. 어떤 레이어의 슬라이스인지에 따라 세그먼트의 구성이 달라질 수 있습니다.

### 슬라이스에 대한 고찰

**위 예처럼 FSD에서는 다음과 같은 모듈들이 슬라이스라고 이야기 하고 있습니다.**

<mark style="background-color:blue;">photo</mark>, <mark style="background-color:purple;">create-album</mark>, <mark style="background-color:orange;">gallery-page</mark>, <mark style="background-color:red;">post</mark>, <mark style="background-color:green;">add-user-to-friends</mark>, <mark style="background-color:blue;">news-feed</mark>

**photo :** 자체적으로도 비즈니스 도메인이 될 수 있음

**create-album :** album이라는 비즈니스 도메인에 종속된 기능, **밀접한 슬라이스 그룹화**에 따라 album 폴더 하위로 분류될 수 있음

**gellery-page** **:**  gellery 라는 비즈니스 도메인에 관련된 페이지

**add-user-to-friends :** **팔로우/팔로잉** 이라는 비즈니스 도메인에 종속된 기능으로 볼 수 있음. 밀접한 슬라이스 그룹화에 따라 **fallow** 폴더 하위로 분류될 수 있음

**news-feed :** news-feed 자체가 비즈니스 도메인이거나 news라는 비즈니스 도메인을 조회하는 **위젯** 또는 **엔티티**가 될 수 있음

**저는 위 내용에 애매한 내용이 있다고 생각합니다.**&#x20;

