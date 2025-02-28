---
layout: post
title:  "Range Retention"
subtitle: "리텐션(3)"
date:   2023-11-09 18:00:30
author: nahyun
categories: ["배움", "데이터분석"]
tags:
  - "Range Retention"
  - "Bracket Retention"
  - "분석 프레임워크"
  - "published"
---

**범위 리텐션(Range Retention)**은 클래식 리텐션을 유연하게 확장한 개념이다. 클래식 리텐션에서는 분석을 특정 일/주/월 단위로 한정짓는 느낌이라면 범위 리텐션은 구간을 유연하게 나눈다는 점만 다르다. **범위를 지정하는 게 특징**이기 때문에 Bracket Retention이라고도 한다.

### 범위 리텐션 계산하기
12월 1일에 서비스에 가입한 사용자 A, B, C, D, E의 방문로그를 아래와 같이 가정해보자. 이 데이터를 3일씩 묶어서 범위 리텐션을 계산해보자.

| 사용자 | 12/1 | 12/2 | 12/3 | 12/4 | 12/5 | 12/6 | 12/7 |
|--------|------|------|------|------|------|------|------|
| A      | 방문 | 방문 | 방문 | 방문 | 방문 | 방문 | 방문 |
| B      | 방문 | 방문 | ㅤ   | 방문 | ㅤ   | ㅤ   | ㅤ   |
| C      | 방문 | ㅤ   | 방문 | 방문 | ㅤ   | ㅤ   | ㅤ   |
| D      | 방문 | ㅤ   | ㅤ   | ㅤ   | ㅤ   | 방문 | ㅤ   |
| E      | 방문 | ㅤ   | ㅤ   | ㅤ   | ㅤ   | ㅤ   | 방문 |


아래의 기준으로 데이터를 묶어보자.
- Day 0: 12월 1일 (최초 접속일 또는 서비스 가입일)
- Day 1~3: 12월 2일부터 4일까지
- Day 4~6: 12월 5일부터 7일까지


|                    | 12/1 | 12/2~12/4 | 12/5~12/7 |
|--------------------|------|-----------|-----------|
| 기간 내 방문유저수 | 5    | 3         | 3         |
| 범위 리텐션 %      | 100% | 60%       | 60%       |


Day 1~3 기간 동안에는 사용자 A, B, C가 방문했고 Day 4~6 기간에는 사용자 A, D, E가 방문했다. Day 4~6 기간보다 Day 1~3 동안에 훨씬 많은 방문이 발생했지만 이러한 특성은 데이터를 요약하면서 사라지고 두 구간 모두 범위 리텐션은 60%로 계산된다.

이렇게 기간을 묶어서 계산하기 때문에 범위 리텐션 계산 방식은 **노이즈에 강하다**. 우연히 하루정도 접속을 안 했다고 하더라도 리텐션에 영향을 주지 않기 떄문이다. **주로 서비스 사용주기가 길거나 주기적인 서비스에서 많이 사용**한다.


### 범위 리텐션 계산하기
클래식 리텐션, 롤링 리텐션, 범위 리텐션 계산 결과를 하나의 차트로 그려보면, 같은 데이터를 이용하더라도 계산 방식에 따라서 이렇게 리텐션이 달라질 수 있다.

![Image Alt rolling_retention](/assets/img/231109/3retention.png)


사용자의 이용 패턴이 바뀐 것도 아니고, 로그의 수집 형식이 달라진 것도 아니고, 계산하는 방법만 달라졌는데 리텐션 값이 이렇게 다른 것을 보면서 몇 가지 주의해야 할 점이 있다.

**1. 남과 비교를 하려면 제대로 해야 한다.**
우리 서비스는 클래식 리텐션을 계산하고 있는데 옆 회사 롤링 리텐션을 가져다가 비교하는 일은 하지 말아야 한다.

**2. 우리 서비스에 맞는 계산 방법을 사용해야 한다.**
사용 주기가 긴 서비스인데 일 단위 클래식 리텐션을 사용한다면 쓸데없는 일희일비를 하다가 잦은 푸시, 할인 등으로 사용자들을 지치게 할 수 있다. 

또한, 사용주기가 짧은 서비스인데 너무 넓은 범위의 리텐션을 사용하면 계산하기까지 시간이 많이 소요되고(범위만큼 데이터가 모여야 계산할 수 있기 때문), 지나치게 요약된 정보를 보느라 사용자들의 이탈시기를 놓칠 수 있다.

**3. 형식에 얽매이지 말자.** 
지금까지 세 가지 리텐션 계산 방법을 살펴봤지만, 정해진 공식은 없다. 이 서비스와, 서비스를 사용하는 유저를 가장 잘 이해하는 건 담당자이다. 세 가지 리텐션 계산 방법을 기초로 서비스의 상황과 목표에 맞게 응용할 필요가 있다.

**4. 하나만 볼 필요는 없다.**
어떤 것이 최선일까 고민하는데 너무 많은 시간을 쏟지 말자. 일단 가장 쉬운 방법으로 계산하고 현상을 파악하는 게 중요하다. 그렇다고 너무 많이 보지도 말자. 숫자가 너무 많으면 그것을 다 이해하기보다는 압도된다...




<br>

**Ref**

datarian 블로그 - https://datarian.io/blog