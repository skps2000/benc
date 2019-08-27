---
layout: post
title:  "[Javascript] 함수 실행시 어떤일이 일어날까? "
date:   2019-08-27 21:38:00
categories: javascript callstack heap queue 
---

### 개관
자바스크립트 함수가 실행될때 브라우저는 어떤 일이 일어날까요? 

```js
function jsFn(){
  console.log('hello');
}
```

