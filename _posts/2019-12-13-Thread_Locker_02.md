---
layout: post
title:  "[동시성] 쓰레드와 잠금장치(2/3)"
date:   2019-12-13 12:38:00
categories: Thread Locker
---

동시성 프로그래밍 2탄입니다. 1편을 보고 오지 않으신 경우 1편 먼저 보는것을 추천드립니다. 오늘은 쓰레드와 장금장치에 대한 부연설명과 메모리, 그리고 다중 잠금장치에 대해서 한번 포스팅 해보도록 하겠습니다. 제 나름대로 추상적인 개념들을 직관적으로 바꾸어 정리할 수 있도록 노력해보겠습니다. 

### 메모리모델
동시성 프로그래밍에서 코드를 작성하다 보면 경쟁조건이나 데드락에 의해서 문제가 발생되는 경우가 흔합니다. 우리는 메모리에서 어떠한 문제를 일으키는지 코드와 함께 이해해 보도록 하겠습니다. 

#### 스레드 만들기
자바에서 사용하는 동시성의 기본단위는 스레드입니다. 스레드는 공유메모리를 이용해서 다른 스레드와 의사소통하는데요 다음 코드를 함께 봅시다.

```java

package com.paulbutcher;

public class HelloWorld {

  public static void main(String[] args) throws InterruptedException {
    Thread myThread = new Thread() {
        public void run() {
          System.out.println("Hello from new thread");
        }
      };
	  
    myThread.start();
    Thread.yield();
    System.out.println("Hello from main thread");
    myThread.join();
  }
}

```



### 메모리가 하는일

### 다중 잠금장치

### 외부 메서드



