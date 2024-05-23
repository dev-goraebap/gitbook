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

개발자로서 고품질 코드를 추구하는 것은 중요한 일이라고 생각됩니다. 고품질 코드는 유지보수가 쉽고, 읽기 쉬우며, 효율적이고, 확장 가능해야 합니다.&#x20;

{% hint style="info" %}
### TMI

_제가 완독한지 얼마 안되는 책 중의 하나인 **디자인패턴의 아름다움**의 내용 중 일부를 사용하였습니다. 책 자체는 500페이지가 넘지만 (200 페이지 이후로 디자인패턴에 대해 알려주는데, 사실 그부분보다 1\~200 페이지가 제일 가치 있었던.. 이상한 책) 초반부만 읽어도 상당히 도움을 주는 책이기 때문에 적극 추천 드립니다._

[_https://www.yes24.com/Product/Goods/118859035_](https://www.yes24.com/Product/Goods/118859035)
{% endhint %}

그런데 이러한 고품질 코드를 작성하는데  방해가 되는 요소가 있습니다. 바로 코드를 관리하는 폴더 (패키지) 간의 의존성 입니다.&#x20;

폴더 구조는 멋진 이름을 사용하며 나름의 의미를 부여하며 합리적으로 만들었다고 생각되는데 뭔가 찜찜한.. 하지만 뭐가 찜찜한건지 잘 모르는 그런 현상들이 있었습니다. 그러다 이 영상을 언젠가 보게 되었는데요. 6분 50초 쯤으로 넘어가시면 패키지 의존성에 대해 짧게나마 이야기를 하고있습니다. 11분 28초까지 꼭 보시면 좋을 것 같아요.

{% embed url="https://www.youtube.com/watch?v=dJ5C4qRqAgA&t=410s" %}
6분 50초쯤에 패키지 의존성에 대해 다룹니다.
{% endembed %}

클래스 파일을 포함하고 있는 폴더, 즉 패키지 구조 역시 코드의 의존성에 영향을 미칩니다. 패키지 간의 의존성이 복잡하게 얽혀 있으면, 코드를 추적하고 관리하기 어려워집니다.&#x20;

개인적으로 기능 분할 디자인의 레이어 참조 규칙은 참조 방향성을 명확하게 정의한다는 점에서 마음에 들었습니다. 위 영상에서 다루는 패키지 의존성을 단 방향으로 풀어 내는 것이 바로 FSD의 레이어 참조 규칙 이라고 생각됩니다.

### Slice (슬라이스)

슬라이스는 기능 분할 디자인에서 두 번째 수준의 조직 계층 구조입니다. 슬라이스는 애플리케이션의 **비즈니스 도메인**에 따라 만들어집니다. 때문에 슬라이스의 이름은 애플리케이션의 **비즈니스 도메인**에 따라 직접 결정되므로 표준화되어 있지 않습니다. (개발자가 직접 정의)

모든 레이어들은 하위 폴더로 슬라이스 들을 가지지만 예외로 **app, shared 레이어**는 슬라이스를 가지지 않습니다. **shared**에는 비즈니스 로직이 전혀 포함되어 있지 않으므로 의미가 없고, **app**에는 전체 애플리케이션과 관련된 코드만 포함되어야 하므로 슬라이스가필요하지 않기 때문입니다.

본문: [https://feature-sliced.design/docs/reference/slices-segments#slices](https://feature-sliced.design/docs/reference/slices-segments#slices)

위 비즈니스 도메인이라는 부분이 정말 애매한 내용인데, 일단 비즈니스 도메인의 정의 자체도 확실한 단어가 없어서 제가 찾아봤을 때 제일 유사한 단어는 **비슷한 업무의 집합**으로 생각하는것이 합리적이라고 판단했습니다. 애플리케이션에서 구현해야할 요구사항의 기능 또는 개념의 집합인 것이죠.&#x20;

**제가 오해하고 다른 개발팀원들에게 잘못 설명했던 부분에 대해서 정정을 해야할 것 같습니다.**&#x20;

`photo`, `create-album`, `gallery-page`, `post`, `add-user-to-friends`, `news-feed`

위 항목 모두 공식 문서에서는 슬라이스라고 표현하고 있습니다. 위 예제는 제가 생각했던 것과 조금 다르지만, 공식문서가 그렇다고 하니.. 타협점을 찾는다면 다음과 같이 볼 수 있습니다.&#x20;

{% hint style="success" %}
**photo :** 자체적으로도 비즈니스 도메인이 될 수 있음

**create-album :** 행동이 포함되어 있지만 album이라는 비즈니스 도메인을 포함하니까 이 부분도 맞을 수 있다고 생각함..

**gellery-page** **:** gellery 라는 비즈니스 도메인에 관한 것을 알 수 있음

**add-user-to-friends :** 행동이 포함되어 있지만 팔로우/팔로잉  이라는 비즈니스 도메인을 나타내고 있음

**news-feed :** 자체적으로 비즈니스 도메인이 될 수 있음
{% endhint %}

