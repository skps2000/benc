---
layout: post
title:  "[Javascript] Closure"
date:   2019-08-19 21:52:00
categories: javascript closure functional
---

>  **클로져의 적당한 의미?**
>  
> A closure gives you access to an outer function’s scope from an inner function. 
In JavaScript, closures are created every time a function is created, at function creation time.

>  (클로져는 내부 함수에서 외부 함수의 스코프에 접근할 수 있도록 허용하고, 자바스크립트 내에서의 클로져는 함수가 만들어질 때마다 매번 생성됩니다.)


항상 느끼지만 해외 레퍼런스를 참고하다 보면 상당히 추상적인 표현이 많다는걸 알게됩니다. 

소스를 통해 이해해 보죠.

```javascript
function fnClosure() {
  var varText1 = 'text1';
  return function() {
    return varText1;
  };
}

var closure = fnClosure();
console.log(closure()); // 'variable 1'
```

fnClosure라는 함수가 먼저 선언이 되었고 그 함수는 varText1 를 리턴하는 함수를 리턴합니다.  리턴되어진 함수를 실행해 보면 fnClosure안의 지역변수의 값을 반환하고 있습니다. 여기서 Javascript Closure는 무엇인가에 대하여 생각해 보자면 ***GC 대상이 되었어야 할 사용이 끝난 변수(text) 또는 컨텍스트에 대해 참조 경로(function)를 생성하고 반환함으로써 함수 내의 환경에 접근가능한 방법을 제공하는 함수***  라고 보여집니다. 아직은 조금 애매모호 하기 때문에 다음 항목에서 조금 더 이해를 해봅시다.

### 클로저를 통한 은닉화

클로저를 통한 은닉화라 함은 Private variables에 대한 접근 권한 문제입니다. 코드부터 보죠.

```javascript
function Student(name) {
  this._privateName= name;
}

Student.prototype.play = function() {
  console.log(this._privateName+ ' 는 놀고있습니다..');
}

var student1 = new Student('철수');
var student2 = new Student('영희');
var student3 = new Student('훈이');

Student1.play(); // 철수는 놀고있습니다.
Student2.play(); // 영희는 놀고있습니다.
Student3.play(); // 훈이는 놀고있습니다.

Student3._privateName = '철이';

Student1.play(); // 철수는 놀고있습니다.
Student2.play(); // 영희는 놀고있습니다.
Student3.play(); // 철이는 놀고있습니다.
```
상기 예제에서는 Student라는 함수는 name 이라는 인자값을 받아 _privateName 이라는 지역변수에 할당합니다.
이 경우 new 연산자를 사용하여 각각의 이름을 가진 Student 객체를 생성할 수 있지만 상기 Student3 객체의 훈이를 철이로 바꾼것 처럼 객체 생성당시 할당하였던 이름을 외부에서 쉽게 바꿀수도 있습니다.

만약 학생의 이름은 최초 생성자가 실행되어질 경우에만 정하도록 하고 외부에서 변수에 직접 접근하는 것을 제한하려고 한다면 클로져를 이용할 수 있습니다.

```javascript
function Student(name) {
  var _privateName = name;
  return function() {
    console.log(_privateName + '는 놀고있습니다..');
  };
}

var student1 = Student('철수');
var student2 = Student('영희');
var student3 = Student('훈이');

student1(); // 철수는 놀고있습니다..
student2(); // 영희는 놀고있습니다..
student3(); // 훈이는 놀고있습니다..
```

이처럼 _privateName 변수를 함수 내의 지역변수로 지정하면 외부에서 Student 함수 내의 지역변수에 접근할 방법이 없어지게 됩니다. 이렇게 클로져를 활용하면 _privateName이라는 변수의 은닉화가 가능합니다.

### 반복문 클로저

```javascript
var i;
for (i = 0; i < 10; i++) {
  setTimeout(function() {
    console.log(i);
  }, 100);
}
```
클로져를 설명할 때 자주나오는 굉장히 유명한 예제입니다.
일반적으로 해당 코드를 작성할 때 콘솔창에 0부터 9까지 찍히는걸 기대했겠지만, 막상 실행시켜 보면 콘솔창에는 10만이 열번 찍히는 것을 확인하실 수 있을 것입니다. 왜냐하면 setTimeout 안의 함수가 0.1초 뒤에 열번이 실행되었을 시점에는 for문의 i는 이미 열번의 순회를 마치고 i에는 10이 할당되어 있을 것이기 때문입니다. 이러한 코드를 처음 생각했던 것처럼 0부터 9까지 출력되는 결과로 바꾸려면 클로저를 이용하여 원하는 대로 동작하도록 만들 수 있습니다.

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
이 또한 유명한 예제입니다. 이전 코드와 바뀐 점은 for문 안의 setTimeout 함수를 함수로 랩핑하였고 또 그 랩핑된 함수를 즉시 실행하도록 하였습니다.([IIFE](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-stop-feeling-iffy-about-using-an-iife-7b0292aba174), 즉시실행함수를 활용한 예제입니다. 해당 문법에 대해서는 따로 설명하지 않겠으며 링크로 대체하겠습니다)  이렇게 작성한 코드를 실행하게 되면 for문이 순회할 때마다 for문 내의 함수가 실행되게 되고 그때마다 새로운 환경이 만들어집니다. 새로운 환경이 만들어질 때마다 함수 외부의 i값은 함수 내의 j값으로 할당되어지고 결국 0부터 9까지 10개의 환경은 setTimeout 함수가 참조하는 j값을 각각 다른결과로 출력할 수 있도록 합니다.

첫번째 예제의 코드에서의 setTimeout 함수는 같은 환경 내의 똑같은 i값을 바라보고 10번의 실행이 이루어지는 반면, 두번째 개선된 예제의 코드에서는 for문이 실행될때마다 각기 다른 환경을 생성하고 그 각기 다른 환경들은 생성되어질 때 0부터 9까지의 j값이 할당되어집니다. 그러므로 생성된 환경 내의 setTimeout 함수가 실행되어지면 각각 자신의 환경 내에 있는 j값을 참조하게 되는 것입니다.

### 성능
위에서 살펴본 바와 같이 클로져는 함수가 실행될 때마다 각기 다른 환경을 조성합니다. 그리고 그 환경을 기억합니다. 그렇기 때문에 변수에 할당된 자원은 회수가 되지 않는 상태로 지속됩니다. 이로 인한 성능저하를 막기 위해서는 해당 변수에 할당되어져 있는 값을 제거하여 메모리가 회수될수 있도록 하는 다음 예제와 같은 작업이 필요합니다.

```javascript
function Student(name) {
  var _privateName = name;
  return function() {
    console.log(_privateName + '는 놀고있습니다..');
  };
}

var student1 = Student('철수');
var student2 = Student('영희');
var student3 = Student('훈이');

student1(); // 철수는 놀고있습니다..
student2(); // 영희는 놀고있습니다..
student3(); // 훈이는 놀고있습니다..

//변수 할당 해제 및 메모리 회수 작업
var student1 = null;
var student2 = null;
var student3 = null;
```

### 마치며..
클로져라는 개념은 자바스크립트 중급 개발자가 되기위한 기본중의 기본이라 할 수 있습니다. 자바스크립트 프로그램의 아키텍쳐를 구성하는데 있어 없어서는 안될 중요한 개념이고 이에 대한 숙련도에 따라 프로그램의 완성도에 지대한 영향을 미칩니다. 그렇기 때문에 ***단순히 개념을 파악하는데 그치지 않고 어떤 상황에서 어떻게 사용해야 클로져의 효용을 끌어올릴 수 있는지 고찰이 필요*** 한 부분일 것입니다.
