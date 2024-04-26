---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: false
  outline:
    visible: false
  pagination:
    visible: false
---

# 회원가입

1. 구글 로그인 버튼 클릭
2. 사용자는 사용할  구글 계정을 선택 ->&#x20;
   1. 구글 Authorization 을 통해 토큰 발급
   2. 토큰값을 백엔드에 전달
3. 토큰값을 통해 사용자의 snsID, 닉네임, 썸네일 이미지를 받아오기
4. 받았던 데이터들을 통해 회원 계정을 생성.
5. 엑세스토큰, 리프레시토큰 발급 (회원가입 후 로그인까지 자동으로 하기 위함)
