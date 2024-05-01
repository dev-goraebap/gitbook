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

# 🍰 nest.goraebap

nestjs를 사용하면서 자주 사용되는 모듈과 기능들을 npm package 로 관리하는 목적으로 만들었습니다.

생각하고 있는 주 기능들은 Oauth2 인증 및 jwt 인증에 대한 재사용성 모듈을 제공하고, 그 외에도 도메인간의 이벤트 통신 모듈, 이메일 지원, 파일 업로드, 로깅 등의 모듈등을 제공할 예정입니다.🍩

### 기능

1. auth 모듈 ->
   1. [local-auth](local-auth-module.md) -> 시험 단계 완료
   2. [jwt-auth](https://github.com/dev-goraebap/nest-goraebap/tree/develop/src/libs/auth-manager/jwt) -> 토큰 발급 완료, 인증 미구현
   3. oauth -> 제공 예정
2. 이벤트 통신 모듈 -> 제공 예정
3. 이메일 모듈 -> 제공 예정
4. 파일 업로드 모듈 -> 제공 예정
5. 로깅 모듈 -> 제공 예정

### 설치

```bash
npm i nest.goraebap
```
