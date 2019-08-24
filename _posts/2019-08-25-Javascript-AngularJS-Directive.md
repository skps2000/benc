---
layout: post
title:  "[Javascript-AngularJS] Directive를 활용하기 위한 적당한 지식"
date:   2019-08-25 11:02:30
categories: javascript angularJS directive
---

>  **AngularJS Directive를 활용하기 위한 적당한 지식**
>  
> 이번 포스트에서는 AngularJS의 Directive에 대한 이야기를 해보려 합니다. ^^ directive란 angular측에서 제공하고 있는 핵심 컴포넌트이며 대개 Angular 엔진에서 HTML 내의 directive 엘리먼트를 발견하면 정의된 해당 행위(개발자가 작성한 행위이건 Angular 엔진에서 정의해둔 행위이건)를 Compile, link 2단계에 걸쳐 수행하게 됩니다.

AngularJS의 directive를 작성한 예제부터 보도록 해보지요 : )
```js
var myModule = angular.module(...);  

myModule.directive('directiveName', function (injectables) {  
  return {
    restrict: 'A',
    template: '<div></div>',
    templateUrl: 'directive.html',
    replace: false,
    priority: 0,
    transclude: false,
    scope: false,
    terminal: false,
    require: false,
    controller: function($scope, $element, $attrs, $transclude, otherInjectables) { ... },
    compile: function compile(tElement, tAttrs, transclude) {
      return {
        pre: function preLink(scope, iElement, iAttrs, controller) { ... },
        post: function postLink(scope, iElement, iAttrs, controller) { ... }
      }
    },
    link: function postLink(scope, iElement, iAttrs) { ... }
  };
});
```
예제코드 내에서는 Angular 모듈 내에서 directive를 생성하는 함수를 호출합니다. 생성하려고 하는 directive 명은 "directiveName" 이며, 다음 함수 인자값에 directive가 무엇을 수행할 것이느냐에 대한 정의 함수를 확인할 수 있습니다. 이 함수에 정의된 directive의 기능을 확인해 보도록 하겠습니다.

1. restict : HTML에서 디렉티브를 사용하기 위한 DOM  속성 네이밍 설정입니다.

```html
<!-- restrict 설정을 E로 지정할 경우 엘리먼트는 -->
<my-example></my-example>
<!-- 형태로 작성해야 합니다. -->
```

2. template : directive를 수행할 엘리먼트 내에 사용되어질 html(보통은) 내용입니다.
3. templateUrl : 2번과 같은 목적의 html 내용을 특정 url 주소로 설정합니다.
4. replace : 디렉티브를 사용한 HTML의 태그에 template 또는 templateUrl에 포함된 태그 내용을 추가할지 교체할지 설정
5. priority : 디렉티브 별로 compile()과 link()의 호출 우선 순위를 지정합니다.
6. transclude : ng-transclude를 이용하여 원본 내용을 포함시킬지를 설정합니다.
7. scope : 디렉티브 자체 스코프를 설정할건지, 부모스코프를 받아 올 것인지 등 scope에 관한한 내용들을 어떻게 사용할건지를 결정할 수 있습니다.
8. controller : 디렉티브들과 통신하기 위한 역할을 하는 controller명칭을 정의합니다.
9. compile : DOM 엘리먼트를 해석하여 디렉티브로 변환하며 두 종류의 link function을 리턴합니다.
10. link  : preLink()=>compile phase가 실행되고 child 엘리먼트가 link 되기 전에 호출합니다. postLink() => compile phase가 실행되고 child 엘리먼트가 link 된 후 호출합니다. (DOM 구조의 변경을 위해서는 postLink()를 이용하는 편이 좋다고 보여집니다. )
> 예제) link, compile 속성의 활용
```js
compile: function compile(tElement, tAttrs, transclude) {  
      return {
        pre: function preLink(scope, iElement, iAttrs, controller) { ... },
        post: function postLink(scope, iElement, iAttrs, controller) { ... }
      }
    },
    link: function postLink(scope, iElement, iAttrs) { ... }  
```

### 마무리
디렉티브의 기본적인 내용을 설명하였지만 쉽지 않다고 느껴질 수 있습니다. 
직접만들고 실행시켜보는것이 이해하는데에 많은 도움이 될 것입니다.
현재 저는 angularJS 기반의 엔터프라이즈급 프로젝트를 수행중이라 이에 대한 자료들이 종종 좋아보여 정리해두고 있습니다.

추가로 AngularJS 1버전대가 아닌 Angular 5 버전 이상들도 함께 공부하고 있습니다.
의견이나 조언, 제언 아낌없이 주신다면 감사드리겠습니다.
감사합니다.
