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

# 인증된 회원 정보 조회

1. 웹앱에 방문한 사용자는 클라이언트 측에 저장된 토큰 정보를 통해 회원 정보를 요청한다.
   1. 인증 토큰이 없을 경우 요청을 보내지 않는다 -> 비회원 상태
   2. 인증 토큰이 있을 경우 요청을 보낸다.
2. 백엔드는 토큰에 대한 검증을 수행한다.
   1. 토큰이 올바르지 않다면 접근하지 못하게 한다.
   2. 토큰이 올바르다면 유효한 식별자를 추출한다.
3. 추출한 식별자를 통해 회원을 조회한다.&#x20;
   1. 회원이 존재하지 않다면 예외 처리한다.&#x20;
   2. 회원이 존재한다면 회원의 일부 데이터를 제공해준다.

<img src="../../../.gitbook/assets/file.excalidraw (2) (1).svg" alt="" class="gitbook-drawing">
