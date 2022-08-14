---
layout: post
title: Cron Expression
subtitle: Cron 표현식
author: 97kim
categories: Cron
banner:
  video:
  loop:
  volume:
  start_at:
  image: /assets/images/cron/cron_bg.jpg
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: Cron
sidebar: []
---

Cron

`cron`은 UNIX에서 오랫동안 사용된 잡 스케줄러다. 작업을 고정된 시간, 날짜, 간격에 주기적으로 실행할 수 있도록 기능을 제공한다.

### Format

cron 표현식은 공백(띄어쓰기)으로 구분 된 6개 또는 7개의 필드로 구성된 문자열이다.

| 필드 구분 | 필수  | 허용 값 | 허용 특수문자 |
| --- | --- | --- | --- |
| 초   | o   | 0-59 | , - * / |
| 분   | o   | 0-59 | , - * / |
| 시   | o   | 0-23 | , - * / |
| 일   | o   | 1-31 | , - * ? / L W |
| 월   | o   | 1-12 또는 JAN-DEC | , - * / |
| 요일  | o   | 1-7 또는 SUN-SAT | , - * ? / L # |
| 년도  | x   | empty, 1970-2099 | , - * / |

- **`*`** - 필드 내의 모든 값을 선택하는 데 사용된다. 예를 들어, **`분`**필드의 **`*`**는 **`매분`**을 의미한다.
  
- **`?`** - **`일, 요일`** 두 필드에서만 사용되는데 특정 값이 없을 때 (어떤 값이든 상관 없을 때) 사용한다. 예를 들어, 특정 날짜 (예: **`10일`**)에 실행하고 싶지만 그 요일이 무슨 요일인지는 신경 쓰지 않으려면 **`일`**필드에 **`10`**을 넣고, **`요일`**필드에 **`?`**를 넣는다.
  
- **`-`** - **`범위`**를 지정하는 데 사용된다. 예를 들어, **`시간`** 필드의 **`1-3`** 은 **`1시, 2시, 3시`**를 의미한다.
  
- **`,`** - **`추가 값`**을 지정하는 데 사용된다. 예를 들어, **`요일`** 필드의 **`MON, WED, FRI`**는 **`월요일, 수요일, 금요일`**을 의미한다.
  
- **`/`** - **`시작 값`**과 **`증가 값`**을 지정하는 데 사용된다. 예를 들어 **`초`** 필드의 **`0/20`** 는 **`0초, 20초, 40초`**를 의미한다. 그리고 **`초`** 필드의 **`5/20`**은 **`5초, 25초, 45초`**를 의미한다.
  
- **`L`** - **`일, 요일`** 두 필드에서만 사용되는데 지정할 수 있는 범위의 **`마지막 값`**을 설정할 때 사용한다. 예를 들어, **`일`** 필드에서 **`L`**은 **`월`**의 **`마지막 날`**을 의미한다. 예를 들어, 1월의 경우는 31일, 2월의 경우는 28일이다. **`요일`** 필드에 **`L`**만 사용되는 경우는 **`7(SAT)`**을 의미한다. 그리고 **`6L`**은 **`매월 마지막 금요일`**을 의미한다.
  
  > **`L-3`**과 같이 해당 월의 마지막 날부터 오프셋을 지정할 수도 있다. 하지만 **`L-3`**처럼 사용하면 혼동을 주고 예기치 않은 결과를 얻을 수 있으므로 **`L`**을 사용할 때 목록이나 범위를 지정하지 않는 것이 좋다.
  
- **`W`** - **`일`** 필드에만 쓰이고, 주어진 요일에 가장 가까운 평일(**`월요일-금요일`**)을 지정하는 데 사용된다. 예를 들어, **`일`** 필드에 **`15W`**를 넣으면 **`매월 15일에서 가장 가까운 평일`**에 실행한다. 따라서 15일이 토요일이면 14일 금요일에 실행된다. 15일이 일요일이면 16일 월요일에 실행된다. 15일이 월요일이면 15일 월요일에 실행된다. 그런데 **`일`** 필드에 **`1W`**를 넣었을 때, 1일이 토요일이면 월의 경계를 넘지 않으므로 3일 월요일에 실행된다. **`W`**는 하루인 경우에만 지정할 수 있고 목록이나 범위에 사용할 수 없다.
  
  > **`L`** 및 **`W`**는 **`LW`**처럼 **`일`** 필드에서 합쳐 쓸 수 있고, **`마지막 평일`**을 뜻한다.
  

- **`#`** - **`요일`** 필드에만 쓰이고, 해당 월의 **`n번째 00일`**을 지정하는 데 사용된다. 예를 들어, **`요일`** 필드의 **`6#3`**은 **`매월 세 번째 금요일`**을 의미한다. (6=금요일, #3 = 해당 월의 세 번째 요일). 또한 **`2#1`** 는 **`매월 첫 번째 월요일`**를 의미한다. 그리고 **`7#5`**처럼 **`매월 다섯 번째 토요일`**인데, **`#5`**를 지정하고 해당 월에 다섯 번째 토요일이 없으면 해당 월에는 실행하지 않는다.

  > **`허용되는 문자`**와 **`월`** 및 **`요일`** 값은 대소문자를 구분하지 않는다. **`MON`**은 **`mon`**과 동일하다 .

<br>

> **참고**  
[http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/crontrigger.html](http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/crontrigger.html)