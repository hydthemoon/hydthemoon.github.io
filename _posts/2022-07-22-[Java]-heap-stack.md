---
title: "[JAVA] 메모리 관리 - Garbage Collection"
toc: true
toc_sticky: true
categories:
  - JAVA
tags:
  - Garbage Colletion
  - cs
---

> Unreachable Object들은 Garbage Collector에 의해 회수된다.

위의 내용을 이해하기 위해 JAVA에서의 메모리 관리 방법에 대해 살펴보자. 아래 reference들의 흐름을 따라 살을 붙이는 방식으로 작성했다.

일단 간략하게 stack과 heap의 특성을 요약해보자.

## Stack

1. Heap에 할당된 object들의 reference가 할당된다.
2. value type(primitive type)들이 직접 저장된다.
3. stack의 변수들은 **특정한 visibility**(scope)가 있다.
4. Thread마다 독립 stack 공간을 가진다.
5. LIFO

scope는 쉽게 말해 변수가 선언된 블록 단위라고 보면 된다. 소속된 괄호가 닫히면 모두 pop되어 회수된다.

## Heap

1. 실제 object들이 저장되고, stack에 의해 reference 된다.
2. process 당 하나의 heap 공간. 즉 Thread들이 공유함.
3. FIFO

```java
StringBuilder builder = new StringBuilder();
```

보통 위와 같은 방법으로 object를 생성하는데, 이때 new 키워드가 heap에 충분한 공간이 있는지 확인하고 생성한다.

## 생각해볼것

### 사례 1

call by value 개념을 되짚어보자.

```java
public class test {
    public static void main(String[] args) {
        String s = "jaehyun";
        oneFunction(s);
        System.out.println(s);
    };
    public static void oneFunction(String param) {
        param += " kim";
    }
}
```

지금까지 배운내용을 토대로 이걸 실행하면 다음과 같은 과정이 일어난다고 해석할 수 있다.

1. 힙에 `"jaehyun"` 오브젝트가 생성되고 s가 스택영역에서 이를 reference한다.
2. `oneFunction` 함수를 실행하며 param 변수가 `"jaehyun"`오브젝트를 같이 reference하도록 한다. (call by value)
3. 함수안에서 `+`연산을 수행하므로, heap공간에 새로운 공간을 할당하고 새로운 string 오브젝트 `"jaehyun kim"`이 생성된다. param은 이걸 refer하고 있다. heap에 있던 기존의 `"jaehyun"`은 주인없이 떠다닌다,,
4. 함수가 종료되면 `"jaehyun kim"`을 reference하던 param변수가 스택에서 pop되고, 결국 heap에 저장된 `"jaehyun kim"`을 refer하는 변수도 없어졌다!
5. 하지만, s변수는 처음부터 계속 `"jaehyun"`을 refer하고 있었으므로, 그상태 그대로 남아있다.

3, 4번에서 주인없이 떠다니는 애들은 어떻게 될까?

### 사례 2

아래처럼 계속 생성하면 어떻게 될까?

```java
while (1) {
    String x = new String("ABC");
}
```

일단 지금까지 배운 내용으로 위를 해석해보자.

`"ABC"`는 object이므로 이 string은 heap 어딘가에 저장되어 각각 `x`라는 stack에 있는 변수에 의해 reference 되어 있다. 

그렇다면 언젠가 memory error가 뜰까?

그렇지는 않다. 우리의 **Garbage Collector**가 열일해서 다 회수했기 때문이다. (자세히 보면 이전에 생성된 object에 일반적인 방법으로는 접근할 수 있는 방법이 없을을 볼 수 있다.)


## Garbage Collector

이제 진짜 GC에 대해 알아보자. 먼저 뭘하는 애인지를 알아보고, 어떻게 작동하는지 살펴보자.

C언어를 배웠다면 메모리를 직접 malloc, free를 해주면서 관리해줬을텐데, java나 python을 쓰면서는 메모리 관리를 직접 신경 쓴 경험(mle같은거 말고)이 거의 없을 것이다. 왜냐면 JVM에 탑재된 Garbage Colletor가 잘 작동되고 있기 때문인데, 덕분에 덜 머리아프게 개발을 할 수 있다. GC가 동작하게된 배경은 다음과 같은 상황이 경험적으로 빈번하기 때문이다.

1. 대부분의 object는 금방 필요없어진다.
2. 오래된 obj는 최근에 생성된 obj를 거의 참조하지 않는다.

일단 뭔지 알아보자. 앞에서 이렇게 말했었다.

> Unreachable Object들은 Garbage Collector에 의해 회수된다.

여기서 unreachable object란 접근할 수 없는 object, 즉, 위의 사례1에서는 param이 `+`가 되기 전에 heap에 있던 `"jaehyun"` object가 `+`이후로는 더 이상 param에 의해 refer되지 않으므로 어떤 stack 변수로도 해당 object에 접근할 수 없는, unreachable object가 되는 것이다. 그리고 곧, 함수가 종료되면 param도 unreachable object가 되어 얘도 garbage가 된다. 이런애들이 GC에 의해 회수된다고 볼 수 있다. 

이 과정은 **Mark and Sweep**으로 진행된다. 꽤나 단순한 방법인데 아래와 같은 순서로 진행된다.

1. Root하나를 잡는다. stack에 있는 변수들, 실행중인 스레드, JNI 레퍼 등등 root가 될만한 것들.
2. Root로부터 트리 서치를 하면서 참조되고 있는 object들을 **mark**한다.
3. mark되지 않은 object들을 **sweep**(해제)한다.

생각보다 단순하게 작동해서 싱거운 맛이 없지 않다. 심지어 직접 mark를 해주는 과정때문에 오버헤드가 상당하지 않을까하는 걱정도 든다. 만약 서치를 하는데 loop가 요상하게 있으면 어떡하지(ps를 한창 풀다보니 드는 생각)?

실제로, 똑똑하신 Java GC 설계자님들께서 이를 해결하기 위한 여러 장치들을 설계해 놓으셨다.

예를 들자면, Young/Old Generation 영역으로 메모리를 분할한다던지, 다양한 Type(serial GC, Parallel GC, ...)의 GC를 설계해놓는다던지 하는 것들이다.

더 적고 싶은 내용이 많지만 이번에는 여기까지!

## references

[DZone](https://dzone.com/articles/java-memory-management)

[Grinnell Edu](https://rebelsky.cs.grinnell.edu/Courses/CS302/99S/Presentations/GC/)