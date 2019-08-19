---
layout: post
title:  "[Javascript] Closure"
date:   2019-08-19 21:52:00
categories: javascript closure functional
---

Javascript Closure는 독립적인 (자유) 변수를 가리키는 함수이다. 

또, 클로저 안에 정의된 함수는 만들어진 환경을 ‘기억한다’.

이게 무슨말일까? 항상 느끼지만 공식 레퍼런스를 참고하다 보면 상당히 추상적인 표현이 많다는걸 알게된다. 

소스를 통해 이해해 보자.

```javascript
function getClosure() {
  var text = 'variable 1';
  return function() {
    return text;
  };
}

var closure = getClosure();
console.log(closure()); // 'variable 1'
```

getClosure 라는 함수가 먼저 선언이 되었고 그 함수는 text를 리턴하는 함수를 리턴한다.

리턴되어진 함수를 실행해 보면 getClosure 안의 지역변수의 값을 반환하고 있다.

- 여기서 Javascript Closure에 대해 생각해 보자면 GC 대상이 되었어야 할 사용이 끝난 변수(text) 또는 컨텍스트에 대해 참조 경로(function)를 생성하고 반환함으로써 함수 내의 환경에 접근가능한 방법을 제공한다.

아래에서 조금 더 살펴보자.


### 클로저를 통한 은닉화

클로저를 통한 은닉화라 함은 Private variables에 대한 접근 권한 문제이다. 예제 코드를 보자.

```javascript
function Hello(name) {
  this._name = name;
}

Hello.prototype.say = function() {
  console.log('Hello, ' + this._name);
}

var hello1 = new Hello('승민');
var hello2 = new Hello('현섭');
var hello3 = new Hello('유근');

hello1.say(); // 'Hello, 승민'
hello2.say(); // 'Hello, 현섭'
hello3.say(); // 'Hello, 유근'
hello1._name = 'anonymous';
hello1.say(); // 'Hello, anonymous'
```
위에서 Hello()로 생성된 객체들은 모두 _name이라는 변수를 가지게 된다. 

변수명 앞에 underscore(_)를 포함했기 때문에 일반적인 JavaScript 네이밍 컨벤션을 생각해 봤을때 이 변수는 Private variable으로 쓰고싶다는 의도를 알 수 있다. 

하지만 실제로는 여전히 외부에서도 쉽게 접근가능한 변수일 뿐이다.

이 경우에 클로저를 사용하여 외부에서 변수에 직접 접근하는 것을 제한할 수 있다.

```javascript
function hello(name) {
  var _name = name;
  return function() {
    console.log('Hello, ' + _name);
  };
}

var hello1 = hello('승민');
var hello2 = hello('현섭');
var hello3 = hello('유근');

hello1(); // 'Hello, 승민'
hello2(); // 'Hello, 현섭'
hello3(); // 'Hello, 유근'
```

특별히 인터페이스를 제공하는 것이 아니라면, 여기서는 외부에서 _name 에 접근할 방법이 전혀 없다. 이렇게 은닉화도 생각보다 쉽게 해결할 수 있다.


### 반복문 클로저

```javascript
var i;
for (i = 0; i < 10; i++) {
  setTimeout(function() {
    console.log(i);
  }, 100);
}
```
간단하게 0-9까지의 정수를 출력하는 코드이지만 실제로 돌려보면 엉뚱하게도 10만 열 번 출력되는 걸 볼 수 있다.

먼저 setTimeout()에 인자로 넘긴 익명함수는 모두 0.1초 뒤에 호출될 것이다. 

그 0.1초 동안에 이미 반복문이 모두 순회되면서 i값은 이미 10이 된 상태. 그 때 익명함수가 호출되면서 이미 10이 되어버린 i를 참조하는 것이다.

이 경우에도 클로저를 사용하면 원하는 대로 동작하도록 만들 수 있다.

```javascript
var i;
for (i = 0; i < 10; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j);
    }, 100);
  })(i);
}
```
중간에 IIFE를 덧붙여 setTimeout()에 걸린 익명함수를 클로저로 만들었다. 앞서 말한대로 클로저는 만들어진 환경을 기억한다. 이 코드에서 i는 IIFE내에 j라는 형태로 주입되고, 클로저에 의해 각기 다른 환경속에 포함된다. 반복문은 10회 반복되므로 10개의 환경이 생길 것이고, 10개의 서로 다른 환경에 10개의 서로 다른 j가 생긴다.

이쯤에서 IIFE 매개변수로 i를 넘기지 않고 그냥 직접 참조해도 되지 않느냐는 의문이 들 수도 있다. 하지만 직접 그렇게 해보면 원하는 대로 동작하지 않는다. 클로저는 바로 바깥의 환경(스코프)만 복사하기 때문이다. 그 윗단계의 함수 스코프는 그냥 참조한다. 따라서 위에서는 반드시 IIFE 내에 복사할 용도의 변수가 하나는 정의되어야 한다. 물론 위처럼 매개변수로 넘겨받은 인자더라도 상관은 없다.

수정: 위의 이유 설명문단에서 오류가 발견되어 덧붙인다. 위의 예제에서는 IIFE를 통해서 클로저마다 환경이 생긴다. 하지만 인자로 i를 넘기지 않는다면 당연히 클로저가 참조하는 IIFE의 함수 스코프에서도 i값이 없으므로 생성 당시의 외부 스코프인 글로벌을 탐색하게 되고 결국 모두 같은 i를 참조하게 된다. 반면에, 인자로 i를 넘기게 되면 IIFE로 만든 10개의 스코프에 모두 i라는 변수가 다른 값으로 생기므로 정상적으로 동작할 수 있는 것이다.

참고로 여기서 콜백으로 넘기는 함수 자체를 IIFE로 만들면 되지 않느냐는 사람도 있는데, 그렇게 하면 원하는대로 0-9까지 출력은 되지만 함수 내부가 즉시 실행되어 버리므로 setTimeout()의 0.1초 딜레이가 작동하지 않게 된다.

### 클로저의 성능

클로저는 각자의 환경을 가진다. 이 환경을 기억하기 위해서는 당연히 메모리가 소모될 것이다. 

클로저를 생성해놓고 참조를 제거하지 않는 것은 C++에서 동적할당으로 객체를 생성해놓고 delete를 사용하지 않는 것과 비슷하다. 

클로저를 통해 내부 변수를 참조하는 동안에는 내부 변수가 차지하는 메모리를 GC가 회수하지 않는다. 

따라서 클로저 사용이 끝나면 아래와 같이 참조를 제거하는 것이 좋다.

```javascript
function hello(name) {
  var _name = name;
  return function() {
    console.log('Hello, ' + _name);
  };
}

var hello1 = hello('승민');
var hello2 = hello('현섭');
var hello3 = hello('유근');

hello1(); // 'Hello, 승민'
hello2(); // 'Hello, 현섭'
hello3(); // 'Hello, 유근'

// 여기서 메모리를 release 시키기 클로저의 참조를 제거해야 한다.
hello1 = null;
hello2 = null;
hello3 = null;
```

### 마치며..

클로져라는 개념은 자바스크립트 중급 개발자가 되기위한 기본중의 기본이라 할 수 있다.
자바스크립트 프로그램의 아키텍쳐를 구성하는데 있어 클로져는 없어서는 안될 중요한 개념이고 이에 대한 숙련도에 따라 프로그램의 완성도에 지대한 영향을 미친다. 

#### **단순히 개념을 파악하는데 그치지 않고 어떤 상황에서 어떻게 사용해야 클로져의 효용을 끌어올릴 수 있는지 고찰이 필요한 부분일 것이다.**
