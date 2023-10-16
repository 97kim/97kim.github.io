---
layout: post
title: 쇼핑몰 매출이 올라가는 자동메시지 팝업
subtitle: 노코드솔루션
author: 97kim
categories: 후기
banner:
  video:
  loop:
  volume:
  start_at:
  image: /assets/images/codenbutter/codenbutter-bg.webp
  opacity: 0.618
  background: "#000"
  height: "70vh"
  min_height: "50vh"
  heading_style: "font-size: 2em; font-weight: bold; text-shadow:2px 2px 5px #000;"
  subheading_style: "color: gold; text-shadow:2px 2px 5px #000;"
tags: 노코드솔루션 코드앤버터 자동메시지
sidebar: []
permalink: /posts/review/codenbutter-auto-message
---

### 쇼핑몰 매출이 올라가는 자동메시지 팝업 ###

쇼핑몰을 운영 중이라면 눈여겨 봐야 할 중요한 요소 중 하나는 자동메시지입니다. 특정 상황에 카카오톡, 문자, 이메일을 고객들에게 보내는 것이 중요한 것처럼 자동메시지 팝업도 중요합니다. 팝업은 어떤 내용을 담고 있는지도 중요하지만 위치와 어떤 타이밍에 나타나는지도 아주 중요합니다. 상단 바 배너에 회원 가입 유도를 할 수도 있고, 뒤로 가기를 누른 고객들에게 리마인드 팝업을 노출시킬 수도 있고, 모바일 웹에서 접속 시 앱 설치 팝업도 노출시킬 수 있습니다. 이렇게 상황에 따라 사용자의 행동과 의도에 적합한 팝업을 이용하면 훨씬 짜임새 있는 온사이트 마케팅 전략을 세울 수 있겠죠? 이렇게 팝업의 위치와 팝업이 뜨는 타이밍에 대해 디테일하게 세팅할 수 있는 코드앤버터를 소개하려고 합니다.

> [코드앤버터 구경하기 (클릭)](https://www.codenbutter.com/?utm_source=site&utm_medium=blog&utm_campaign=1&utm_term=automessage)

#### 1. 로그인 페이지 상단 바 배너에 팝업 노출 ####
나의 쇼핑몰의 도메인 주소가 myshop.com 이라고 가정해볼게요. myshop.com/login 이라는 로그인 페이지에서 상단 바 배너에 회원가입 유도 팝업이 나오도록 세팅하는 방법을 알아보겠습니다.

코드앤버터에서는 모바일, 데스크탑 환경별 팝업 형태를 설정할 수 있습니다.

모바일
![codenbutter34](/assets/images/codenbutter/codenbutter34.png)

데스크탑
![codenbutter35](/assets/images/codenbutter/codenbutter35.png)

우리는 로그인 페이지에서 상단 바에 팝업을 띄우도록 하기로 했기 때문에 상단 띠 스타일 A 또는 B를 선택하면 되겠죠.

![codenbutter36](/assets/images/codenbutter/codenbutter36.png)

이런 식으로 내용을 입력해준 다음 노출 대상이라는 메뉴를 클릭합니다.

![codenbutter37](/assets/images/codenbutter/codenbutter37.png)

노출 대상 메뉴를 들어가면 여러가지 항목을 확인할 수 있습니다.

![codenbutter38](/assets/images/codenbutter/codenbutter38.png)

로그인 페이지에서 상단 바에 팝업을 띄우도록 하기 위해선 도메인에는 myshop.com을 입력하면 되고 페이지에는 /login을 입력하면 됩니다. 로그인 페이지에 접속한 모든 고객들에게 팝업이 보여야 하니 브라우저 식별 정보는 따로 세팅하지 않아도 되겠죠? 나머지 항목들도 마찬가지로 특정 조건이 필요하다면 추가하면 됩니다. 저장하고 팝업을 공개시키면 끝입니다! 자동메시지 팝업 만들기 간단하죠?

#### 2. 뒤로가기 / 닫기 클릭 시 팝업 노출 ####
고객이 쇼핑몰에서 쇼핑하다가 뒤로가기 또는 닫기를 클릭했을 때 팝업이 뜨도록 할 수도 있는데요.

![codenbutter39](/assets/images/codenbutter/codenbutter39.png)

트리거 메뉴로 들어갑니다.

![codenbutter40](/assets/images/codenbutter/codenbutter40.png){: width="700px"}

트리거 메뉴에서 이탈 감지 항목을 켜면 됩니다. 저장하고 공개시키면 끝이에요. 간단하죠?
이탈 감지 이외에도 공개 지연은 사용자가 페이지 진입 후 몇 초 뒤에 팝업이 뜨게 할 것인가 설정할 수도 있고, 스크롤을 몇 퍼센트 했냐에 따라서도 팝업이 노출되게 할 수도 있어요.

#### 3. 특정 기간에 프로모션 팝업 노출 ####
특정 기간에 상품들을 할인해서 파는 프로모션이 있다면 고객들에게 프로모션 관련해서 팝업을 노출시키면 좋을 것 같은데요.

![codenbutter41](/assets/images/codenbutter/codenbutter41.png)

![codenbutter42](/assets/images/codenbutter/codenbutter42.png)

스케줄 설정 메뉴에서 예약 공개를 클릭 해 예약기간을 세팅해주면 됩니다. 스케줄 설정도 간단합니다!


코드앤버터를 통해 자동메시지 팝업을 추가하는 방법을 알아보았는데요. 이용해보시면 어려움 없이 설정하실 수 있을 것이라고 생각합니다. 자동메시지 팝업으로 업무 효율을 높이고 매출까지 올리면 더욱 좋을 것 같습니다.

본 포스팅은 소정의 원고료를 지원받아 작성된 후기입니다.