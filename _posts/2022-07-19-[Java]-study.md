---
title: "[JAVA] 클래스/인스턴스 변수/메서드의 구분"
toc: true
toc_sticky: true
categories:
  - JAVA
tags:
  - JAVA
  - cs

---

코테 언어 3대장 중 초심자가 봤을 때 가장 어려워보이는 언어는 자바가 아닐까 싶다. C 조금 하다가 파이썬 원툴로 잘 살아왔지만 회사에서 자바를 쓰기 때문에.. 그리고 앞으로 자바민국에서 살아가기 위해 나도 자바 공부를 시작했는데, 이것도 정리하면서 공부하면 용어를 좀더 오래 기억하는데도움이 되지 않을까 싶어 정리해보기로 했다.

## 필드에서의 구분

- class variable (클래스 변수, static 변수)
- instance variable (인스턴스 변수)
- local variable (지역 변수)
static/instance 변수들의 초깃값은 적절히 설정되어 있음.

앞에 static 냅다 붙어있으면 class variable 되는거다.

|  <center>타입</center> |  <center>생성시기</center> |  <center>소멸시기</center> |
|:--------|:--------:|--------:|
|**class** | <center>클래스가 메모리에 로드될 때</center> | <center>프로그램이 종료될 때 </center>|
|**instance** | <center>인스턴스가 생성될 때</center> |<center> 인스턴스가 소멸할때/GC가 수거할 때 </center>|
|**local** | <center>블록 내의 변수 선언문이 실행될 때</center> |<center> 블록을 벗어날 때 </center>|

class 변수는 instance를 생성하기 전부터 이미 메모리(method영역)에 로드되어 있다. 때문에, 해당 클래스의 모든 instance는 같은 값을 가지게 된다. 따라서, shared variable(공유 변수)라고도 불림. 사실 이 문단만 기억하면 될듯.

instance 변수는 heap영역에 저장되어 instance마다 개별 지정 가능.

local 변수는 stack에 저장됨.

### 예시

```java
class Car {
  int limit = 500; // instance variable
  static String name = 'ionic'; // static variable
  public static void main(String args[]){
    int a = 1; // local variable
  }
}
```


## 메소드에서의 구분

- class method (클래스 메소드, static method)
- instance method (인스턴스 메소드)

비슷하게 static 키워드가 달려있으면 클래스되는거고 없으면 인스턴스되는 것이다. 또 비슷하게 class method는 instance 생성 전부터 사용가능하다.

따라서, class method는 instance variable을 사용할 수 없는데, 생각해보면 class들은 바로 생기지만 instance들은 instance가 할당되어야 시작되므로 순서가 꼬이지 않기 위해서 그렇다고 보면 된다.

따라서, method가 instance method/variable을 사용하지 않으면 보통 class method로 해준다고 한다.

### 예시

```java
class Car {
  boolean door;
  void openDoor(){ // instance method
    door = true;
  }
  static void toggleDoor(boolean d) { // class method
    return !d;
  }
}
```

---

각 변수가 어떻게 저장되는지 더 찾아보다가 JAVA에서 GC가 메모리를 어떻게 관리하는지에 대해 공부하게 되었는데 다음 포스트에서 다룰 예정이다.

