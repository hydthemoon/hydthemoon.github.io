---
title: "[JAVA] 접근 제한자"
toc_sticky: true
toc: true
categories:
  - JAVA
tags:
  - 접근 제한자


---

저는 처음 자바 코드를 봤을때(사실 바로 지난주), 길다란 클래스 수식어들 때문인지 머리가 어지러워지고 말았습니다. 이번에는 그 주범인 접근제한자 - class modifier에 대해 잊지 않게 간략히 정리해두었다.

## 포함 관계

선언된 대상을 접근할 수 있는 클래스를 기준으로 포함관계를 나타내면 아래와 같습니다.

<img width="400" alt="classmodi" src="https://user-images.githubusercontent.com/40457043/184095515-901d6f08-b94b-4cfe-8f4d-082f14834203.png">

(default란 아무것도 안적은 상황을 의미한다.)

|<center>modifier</center> | <center>접근 가능성</center> |
|:--------|:--------:|
|**public** | <center>모든 패키지에서 가능</center> |
|**protected** | <center>같은 패키지 + 선언된 클래스의 child 클래스</center> |
|**default** | <center>같은 패키지의 클래스들</center> |
|**private** | <center>내부만 가능</center>|

위의 규칙들은 클래스 뿐만 아니라 변수나 메서드 등에도 같이 적용된다.

접근제어자의 적절한 활용을 통해 실수를 방지하고 위험회피가 가능해진다.

## 예시

...

## 참고

[javatpoint](https://www.javatpoint.com/access-modifiers)


## 여담

아직 package 관리나 maven이나 graddle이냐 이런것들은 잘 모르겠는데 배울게 많다 ,,
