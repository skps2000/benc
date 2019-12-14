---
layout: post
title:  "[동시성] 쓰레드와 잠금장치(2/3)"
date:   2019-12-13 12:38:00
categories: Thread Locker
---

동시성 프로그래밍 2탄입니다. 1편을 보고 오지 않으신 경우 1편 먼저 보는것을 추천드립니다. 오늘은 쓰레드와 장금장치에 대한 부연설명과 메모리, 그리고 다중 잠금장치에 대해서 한번 포스팅 해보도록 하겠습니다. 제 나름대로 추상적인 개념들을 직관적으로 바꾸어 정리할 수 있도록 노력해보겠습니다. 

### 자바를 통한 스레드와 잠금장치의 이해
동시성 프로그래밍에서 코드를 작성하다 보면 경쟁조건이나 데드락에 의해서 문제가 발생되는 경우가 흔합니다. 우리는 메모리에서 어떠한 문제를 일으키는지 코드와 함께 이해해 보도록 하겠습니다. 

#### 스레드 만들기
자바에서 사용하는 동시성의 기본단위는 스레드입니다. 스레드는 공유메모리를 이용해서 다른 스레드와 의사소통하는데요 다음 코드를 함께 봅시다.

```java

package com.paulbutcher;

public class HelloWorld {

  public static void main(String[] args) throws InterruptedException {
    Thread myThread = new Thread() {
        public void run() {
          System.out.println("A");
        }
      };
	  
    myThread.start();
    Thread.yield();
    System.out.println("B");
    myThread.join();
  }
}

```

이 코드가 반환하는 값은 순서가 보장될수 없습니다. A가 먼저 출력될지 B가 먼저 출력될지는 타이밍에 따라 달라질수 있게 구성되어 있기 때문입니다. 이같은 스레드의 속성은 멀티스레딩 코드를 어렵게 만드는 부분입니다.

#### 첫번째 잠금장치
우리는 각자의 스레드가 엉키지 않도록 잠금장치를 사용할수 있습니다.

```java
package com.paulbutcher;

// START:main
public class Counting {
  public static void main(String[] args) throws InterruptedException {
	  
    class Counter {
      private int count = 0;
      public void increment() { ++count; }
      public int getCount() { return count; }
    }
    
    final Counter counter = new Counter();
    
    class CountingThread extends Thread {
      public void run() {
        for(int x = 0; x < 10000; ++x)
          counter.increment();
      }
    }

    CountingThread t1 = new CountingThread();
    CountingThread t2 = new CountingThread();
    t1.start(); t2.start();
    t1.join(); t2.join();
    System.out.println(counter.getCount());
  }
}
// END:main

```

t1과 t2 스레드가 하는 일은 같습니다. 이 두 스레드를 위 코드와 같이 호출할 경우 매번 다른 값을 가져올 것입니다. t1스레드와 t2스레드가 동일한 타이밍에 increment 메소드를 호출한다면 서로 같은 count값을 읽고 서로 같은 결과값을 반환할 것이기 떄문입니다. 그렇게 되면 count가 두번이 아니라 한번만 증가된것과 동일한 값이겠지요. 이런 상황에 대한 해법은 잠금장치를 이용하는 것입니다. count에 대한 접근을 동기화 시켜 한번에 하나의 스레드만 접근할 수 있도록 설정해두면 우리가 기대하는 반환값 20000이 반환될수 있을것입니다. 자바에서는 잠금장치를 syncronized 라는 잠금장치를 자체적으로 제공하고 있습니다. 코드를 수정하면 다음과 같습니다.

```java
package com.paulbutcher;

// START:main
public class Counting {

  public static void main(String[] args) throws InterruptedException {
    
    // START:code.counter
    class Counter {
      private int count = 0;
      // START_HIGHLIGHT
      public synchronized void increment() { ++count; }
      // END_HIGHLIGHT
      public int getCount() { return count; }
    }
    // END:code.counter
    final Counter counter = new Counter();
    
    class CountingThread extends Thread {
      public void run() {
        for(int x = 0; x < 10000; ++x)
          counter.increment();
      }
    }

    CountingThread t1 = new CountingThread();
    CountingThread t2 = new CountingThread();
    
    t1.start(); t2.start();
    t1.join(); t2.join();
    
    System.out.println(counter.getCount());
  }
}
// END:main

```

이제 각 스레드가 increment 메소드를 호출하게 되면 잠금장치가 작동합니다. increment를 반환하면 자동으로 잠금장치도 해제됩니다. 그럼 각 스레드가 동일한 값의 count로 계산할 일도 없어집니다. 이렇게 하면 완변한 프로그램인듯 보입니다. 하지만 이 프로그램은 아직 메모리에 의해 발생되는 미묘한 버그를 가지고 있습니다.

### 메모리가 하는일

다음 코드를 실행하게 되면 어떤 문제를 일으킬까요?

```java
package com.paulbutcher;

// START:main
public class Puzzle {
  static boolean answerReady = false;
  static int answer = 0;
  
  static Thread t1 = new Thread() {
      public void run() {
        answer = 42; //<label id="code.setanswer"/>
        answerReady = true; //<label id="code.answerready"/>
      }
    };
    
  static Thread t2 = new Thread() {
      public void run() {
        if (answerReady)
          System.out.println("The meaning of life is: " + answer);
        else
          System.out.println("I don't know the answer");
      }
    };

  public static void main(String[] args) throws InterruptedException {
    t1.start(); t2.start();
    t1.join(); t2.join();
  }
}
// END:main
```
결론부터 말하자면 위 코드를 실행할때마다 우리는 42값을 볼수도 있고 답을 모른다는 응답을 볼수도 있다. 심지어 다음과 같은 출력값을 얻을수도 있습니다.

``
The meaning of life is: 0
``
이해가 안되겠지만 이것은 분명히 일어날수 있는 일입니다. 다음과 같은 사실 때문이지요.
1. 컴파일러는 코드 실행순서를 바꿀 수 있다.
2. JVM 또한 코드 실행순서를 바꿀 수 있다.
3. 코드를 실행하는 하드웨어도 코드의 순서를 바꿀 수 있다.

이러한 이유들 때문에 우리는 Puzzle 클래스를 믿을 수 없게 됩니다. 하지만, 이렇게 이상한 출력값을 주는 코드를 짜기 전에 우리는 이를 방지할수 있는 어떤 규칙을 숙지하면 됩니다. 그것이 바로 다음과 같은 규칙들입니다.

1. 공유되는 변수에 대한 접근을 반드시 동기화한다.
2. 쓰는 스레드와 읽는 스레드가 모두 동기화 되어야 한다.
3. 여러개의 잠금장치를 미리 정해진 공통의 순서에 따라 요청한다.
4. 잠금장치를 가진 상태에서 외부 메서드를 호출하지 않는다.
5. 잠금장치는 최대한 짧게 보유한다.

### 결론
쓰레드와 잠금장치에 대해 몇가지 예제를 가지고 분석해보았습니다. 조금 기본적일 수 있기 때문에 심화학습을 원하시는 분은 [자바 메모리모델](https://parkcheolu.tistory.com/14) 을 학습해보시면 좋을것 같습니다.


