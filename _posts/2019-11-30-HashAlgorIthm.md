---
layout: post
title:  "[Algorithm] 해시 알고리즘"
date:   2019-11-30 21:38:00
categories: algorithm hash 
---

 안녕하세요 항상 풀스택을 지향하는 개발자 조승규입니다. 현재 몸담고 있는 프로젝트가 워낙 혼란스러워 이제야 포스트를 하게 되네요. 며칠만인지.. 혹은 몇주만인지 모르겠습니다. 지금부터(혹은 새해부터)라도 주 1회 포스트를 하도록 계획을 짜놓는것이 좋을것 같다고 생각이 드네요. 특별히 포스트를 할 계획이 없으니 포스트를 이제 한번 올려야 하지 않나 생각만 하게 되네요...

 해시라는것은 아마 대부분의 개발자들이라면 사용을 안할래야 안할수가 없을 것입니다. 해시함수를 대체할만한 수단이 있다면 모를까(있다 한들 굳이..?) 코딩을 하는 이상 직접적으로든 간접적으로든 알게모르게 많이 사용합니다. 오늘은 기초적이지만 매우 중요한 해시를 한번 정리하고 넘어가려 합니다. 길게 끊지 않고 바로 정리해보도록 하겠습니다.

### 해시라는게 뭐지? (해시, 해시함수)

 해시(Hash)는 어떠한 데이터에 대해 고정된 길이로 변환하는 것을 의미합니다. 보통 해시함수라 하면 해시작업을 해주는 기능이지요. 만약 "teacher"라는 문자열을 해시함수에 인자값으로 작동시킨다면 "4sld83kd..." 와 같은 비인식형 문자열을 확인할수 있을것입니다. "student"라는 문자열을 해시함수에 넣는다면 "teacher"를 넣어서 나온 결과값과는 다른 문자열의 비인식형 문자가 반환되겠죠. 하지만 반환되는 비인식형 문자열의 길이는 "teacher", "student" 둘다 동일할 것입니다. 그리고 동일 문자열의 해시화를 열번 수행하든, 백번을 수행하든 인풋값이 변하지 않는다면 결과값은 항상 동일할 것입니다.(해시함수가 순수함수라는 가정하에) 이러한 변환작업을 해주는 객체가 해시함수입니다.
 
### 여러가지 특징
 해시함수는 입력값의 범위보다 출력값의 범위가 좁은경우가 많습니다. 그렇기 때문에 매우 드물게 입력값이 다른데도 불구하고 출력값이 동일하게 나오는 경우가 있습니다.(비둘기 집의 원리) 우리가 알고있고 종종 사용하기도 하는 MD(Message-Digest Algorithm)와 SHA(Secure Hash Algorithm) 이라는 해시알고리즘도 버전이 업데이트 되면서 이러한 충돌문제를 개선하기도 합니다. 해시 알고리즘은 이렇게 어쩔수 없는 충돌문제를 제외하고 의도적으로 충돌을 계산해낼수는 없어야 합니다.

### 해시를 통해서 보안을 강화한다?
 해시는 보안분야에서도 주로 사용됩니다. 해시함수는 본래의 데이터를 복호화가 불가능하도록 변조한다는 점과 원본 데이터와 결과 해시값의 관계가 선형적이지 않다는 특징 때문이죠. 만약 "teacher"라는 데이터를 통해 "3sss"라는 해시값을 얻는다면 "teacher"라는 결과물은 "3sss"라는 값을 만들지만 "3sss"라는 값은 "teacher"라는 값으로 정확히 찾아갈수 없습니다. 애초에 원본 데이터인 "teacher"라는 값이 손실되면서 해시값을 만들어내기 때문이죠. 해시값은 여러개의 원본값을 가질수도 있습니다. 이러한 특징을 가지기 때문에 우리가 사용자의 비밀번호, 전자서명, 전자상거래와 같은 주요 정보의 무결성을 검증하는데 사용되고 있습니다.
 
### 해시함수를 깬다?
 우리가 종종 사용하는 SHA-1, SHA-256등과 같은 알고리즘은  해시알고리즘의 충돌 가능성이 이론적으로 제시되었습니다. 학계에서는 순수 무차별대입보다 몇천배 적은 정도 계산 횟수로 값을 복호화된다면 대체로 "깼다"라고 표현한다고 합니다. 하지만 SHA-1 해시를 무차별대입으로 깨려면 2의 160승 횟수로 계산을 해야 하는데 1000배 빨라진다고 해봐야 2의 150승 입니다. 현실적인 의미로 해시가 불안하다고 볼수는 없다는 것이죠
 
### 향후..
 해시함수를 사용함에 있어 기본적인 내용정도로 요약을 해보았습니다. 고도의 수학 연산이 필요하지 않는 선에서 현재 배우고 있는 알고리즘을 정리해보려 합니다. 제가 쓰는 글들이 부디 한명이라도 도움이 되었으면 하는 바람이 있긴 하네요..


감사합니다.
