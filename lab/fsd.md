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

## 시작하기 전에

개발자로서 고품질 코드를 추구하는 것은 중요한 일이라고 생각됩니다. 고품질 코드는 유지보수가 쉽고, 읽기 쉬우며, 효율적이고, 확장 가능해야 합니다.  (_고품질 코드의 정의는_ [_**디자인패턴의 아름다움**_](https://www.yes24.com/Product/Goods/118859035) _이라는 책에서 찾아 볼 수 있습니다.)_

그런데 이러한 고품질 코드를 작성하는데  방해가 되는 요소가 있습니다. 바로 코드를 관리하는 폴더 (패키지) 간의 의존성 입니다.&#x20;

폴더 구조는 항상 의미를 부여하며 합리적으로 만들었다고 생각되는데 뭔가 찜찜한.. 하지만 뭐가 찜찜한건지 잘 모르는 그런 현상들이 있었습니다. 그러다 언젠가 이 영상을 보게 되었는데요. 6분 50초 쯤으로 넘어가시면 패키지 의존성에 대해 짧게나마 이야기를 하고있습니다. 11분 28초까지 꼭 보시면 좋을 것 같아요.

{% embed url="https://www.youtube.com/watch?v=dJ5C4qRqAgA&t=410s" %}
6분 50초쯤에 패키지 의존성에 대해 다룹니다.
{% endembed %}

클래스 파일을 포함하고 있는 폴더, 즉 패키지 구조 역시 코드의 의존성에 영향을 미칩니다. 패키지 간의 의존성이 복잡하게 얽혀 있으면, 코드를 추적하고 관리하기 어려워집니다.&#x20;

**기능 분할 디자인(FSD)** 를 프로젝트에 적용하게 되면 **레이어 참조 규칙**, **슬라이스  공개  API 규칙**을 통해 위 영상에서 말하는 패키지간의 의존성을 관리하기 쉽게 하는 모범 사례를 만들어 낼 수 있다고 생각합니다.

## 기능 분할 디자인(FSD) 소개

공식문서를 보고 FSD에 대해 정리하였습니다. 문서는 Deepl 번역을 사용하여 최대한 사이트가 전달하고자 하는 내용을 정확하게 인지하도록 노력했습니다.

{% embed url="https://feature-sliced.design/" %}

{% hint style="info" %}
**Feature Slided Design** 이하 **기능 분할 설계** 는 프런트엔드 애플리케이션을 스캐폴딩하기 위한 아키텍처 방법론입니다. 간단히 말해, 코드 구성에 관한 규칙과 규칙의 모음입니다. 이 방법론의 주된 목적은 끊임없이 변화하는 비즈니스 요구사항에 맞서 프로젝트를 더 이해하기 쉽고 체계적으로 만드는 것입니다.

원문 [https://feature-sliced.design/docs](https://feature-sliced.design/docs)
{% endhint %}

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

### 레이어 참조규칙 ✨ <a href="#import-rule-on-layers" id="import-rule-on-layers"></a>

{% hint style="info" %}
**슬라이스**의 **모듈**은 다른 슬라이스가 바로 아래 레이어에 있는 경우에만 다른 슬라이스를 가져올 수 있습니다.&#x20;

여기서 말하는 **모듈**은 해당 폴더의 애플리케이션의 내부 모듈(인덱스 파일이 있는 파일 또는 디렉토리)을 의미합니다.

_원문_ [_https://feature-sliced.design/docs/reference/layers#import-rule-on-layers_](https://feature-sliced.design/docs/reference/layers#import-rule-on-layers)
{% endhint %}

레이어의 참조 규칙을 설명하려면 레이어들은 각각 슬라이스로 구성된다는 것을 알아야합니다. 슬라이스에 대해서는 아래에서 다루지만, 일단 레이어 폴더 하위의 폴더들이라고 생각해주세요.&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>features layer의 하위 슬라이스들</p></figcaption></figure>

레이어 참조규칙을 적용하면 **auth, user, post**는 모두 **features**라는 같은 레이어의 슬라이스들이기 때문에 서로의 모듈 내 파일을 참조할 수 없습니다.&#x20;

**app, pages, widgets**는 **features**보다 상위 레이어들이기 때문에 당연히 해당 레이어들의 슬라이스들도 접근할 수 없습니다.

그렇다면 **entities, shared** 는 어떨까요? **features** 보다 하위 레이어들이기 때문에 이 경우에는 이 레이어의 슬라이스들에 접근이 가능합니다.

### Slice (슬라이스)

**슬라이스**는 기능 분할 디자인에서 두 번째 수준의 조직 계층 구조입니다. 슬라이스는 애플리케이션의 **비즈니스 도메인**에 따라 만들어 집니다. 즉 프로젝트의 요구사항에 따라 생기기 때문에 이름의 규칙이 정해지지 않고, 개발자가 직접 작성하게 됩니다.

_\*\*모든 레이어들은 하위 폴더로 슬라이스 들을 가지지만 예외로 **app, shared 레이어**는 슬라이스를 가지지 않습니다. **shared**에는 비즈니스 로직이 전혀 포함되어 있지 않으므로 의미가 없고, **app**에는 전체 애플리케이션과 관련된 코드만 포함되어야 하므로 슬라이스가 필요하지 않기 때문입니다._

### _슬라이스_ 공개 API 규칙 ✨

슬라이스 내부에서는 코드를 매우 자유롭게 구성할 수 있으며, 슬라이스에서 좋은 공개 API를 제공하는 한 아무런 문제가 발생하지 않습니다.&#x20;

공개 API를 만들기 위해서는 슬라이스의 경로에 **index** 파일을 만들어 두는 것 입니다. 외부에서 사용 될 수 있는 파일들만 이 index 파일에 명시하며, 외부에서는 index에 명시되지않은 파일엔 접근할 수 없습니다.&#x20;

이로써 패키지간에 캡슐화 원칙을 적용하게 됩니다.

FSD에서는 슬라이스 공개 API 규칙을 통해 다음과 같은 이점을  달성할 수 있다고 이야기합니다.

{% hint style="info" %}
1. 애플리케이션은 개별 모듈의 내부 구조가 **변경되지 않도록 보호되어야** 합니다.
2. 모듈의 내부 구조 처리는 다른 모듈에 **영향을 미치지 않아야** 합니다.
3.  모듈 동작의 중요한 변경 사항은 **쉽게 감지할 수**있어야 합니다.

    > 모듈**동작의 중대한 변경** - 모듈의 사용자 주체의 기대를 깨는 변경 사항입니다.



본문: [https://feature-sliced.design/docs/reference/public-api#goals](https://feature-sliced.design/docs/reference/public-api#goals)
{% endhint %}

### 밀접한 슬라이스들 그룹화 <mark style="color:red;">(주의!  내용이 정확하지 않을 수 있음)</mark>

<figure><img src="../.gitbook/assets/graphic-nested-slices-b9c44e6cc55ecdbf3e50bf40a61e5a27.svg" alt=""><figcaption></figcaption></figure>

위 이미지는 [공식문서 ](https://feature-sliced.design/docs/reference/slices-segments#slices) 제공되고 있습니다. 조금 이해하기 힘든 부분인데, **features**는 레이어이기 때문에 하위에 **post**가 슬라이스가 되어야 하는게 맞다고 생각됩니다.&#x20;

**post**가 슬라이스가 맞다면 하위에는 세그먼트들 (**ui, model, lib, api**) 등이 위치해야하는데, 그렇지 않고 게시물이라는 비즈니스 도메인의 기능들이 위치하게 됩니다.&#x20;

제가 이해하기로는 **compose, like, delete** 들은 FSD가 말하는 슬라이스 들이며, 이들은 게시물이라는 비즈니스 도메인에 밀접하게 관련된 슬라이스 들이기 때문에 **post**라는 슬라이스로 그룹화 할 수 있다는 걸로 보입니다.

대신 붉은 이름의 파일에 취소선이 그어진 부분처럼 위 post 슬라이스 내부의 슬라이스들은 **레이어 참조 규칙에** 따라 **서로 코드를 공유해서는 안됩니다.**

### 슬라이스에 대한 고찰

**위 예처럼 FSD에서는 다음과 같은 모듈들이 슬라이스라고 이야기 하고 있습니다.**

<mark style="background-color:blue;">photo</mark>, <mark style="background-color:purple;">create-album</mark>, <mark style="background-color:orange;">gallery-page</mark>, <mark style="background-color:red;">post</mark>, <mark style="background-color:green;">add-user-to-friends</mark>, <mark style="background-color:blue;">news-feed</mark>

**photo :** 자체적으로도 비즈니스 도메인이 될 수 있음

**create-album :** album이라는 비즈니스 도메인에 종속된 기능, **밀접한 슬라이스 그룹화**에 따라 album 폴더 하위로 분류될 수 있음

**gellery-page** **:**  gellery 라는 비즈니스 도메인에 관련된 페이지

**add-user-to-friends :** **팔로우/팔로잉** 이라는 비즈니스 도메인에 종속된 기능으로 볼 수 있음. 밀접한 슬라이스 그룹화에 따라 **fallow** 폴더 하위로 분류될 수 있음

**news-feed :** news-feed 자체가 비즈니스 도메인이거나 news라는 비즈니스 도메인을 조회하는 **위젯** 또는 **엔티티**가 될 수 있음

**저는 위 내용에 애매한 내용이 있다고 생각합니다.**&#x20;

