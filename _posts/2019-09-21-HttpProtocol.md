---
layout: post
title:  "[Protocol] Http의 변천사"
date:   2019-09-21 21:00:00
categories: protocol http  
---

### Http
http는 1989년 팀 버너스 리에 의해 창안되었습니다. 그는 http의 시작을 두가지 원칙과 함께 하였습니다. 첫번째는 하이퍼텍스트라고 불리우는 "제약없는 방식으로 연결되어 사람이 읽을수 있는 정보" 입니다. 두번째는 "꼭 정보가 텍스트일 필요는 없다" 라는 전제로 하이퍼미디어를 뜻합니다.

### Http/0.9와 1.0
초기 Http/0.9프로토콜은 매우 단순했습니다. 메소드는 GET 뿐이었고 헤더도 없었습니다. 이미지 처리가 안되고 단순 텍스트만 가져올 수 있는 구조로 설계되었습니다. 그렇게 오랜 기간 많은 사용자들이 증가함에 따라 1996년 HTTP/1.0이 발표되었습니다. 1.0버전은 우리가 알고있는 아주 친숙한 개념들을 포함합니다.

1. 헤더
2. Response code
3. Redirects
4. Error
5. Conditional request
6. Encoding
7. Method(POST, PUT 등)

HTTP/1.0에서는 GET메소드에 텍스트만 주고받았던 0.9와는 달리 비약적으로 확장된 기능을 확인할 수 있었습니다. 단순 장난감에서 전문적으로 사용되어질수 있는 도구로서의 기반을 잡은 단계라고도 볼수 있겠습니다. 하지만 비약적으로 확장된 1.0 조차도 필요되어지는 기능이 존재했습니다. 대표적으로 상호 연결을 유지할 수 있는 기능이 없었고, HOST헤더의 필수성과 캐싱옵션의 빈약했던 점을 꼽을수 있습니다. 이 세가지 사항은 웹의 발전에 있어서 가장 해결이 시급한 우선과제였습니다.

### HTTP/1.1
1.0 버전이 나온지 얼마 되지 않아 1.1버전은 발표되었습니다. 현재까지 20년 넘도록 명맥을 유지해오고 있을 만큼 완성도를 채운 HTTP/1.1 버전에서는 1.0에서 부족하였던 기능들이 추가되거나 확장되었습니다. [HOST헤더를 필수](https://gmlwjd9405.github.io/2019/01/28/http-header-types.html)로 지정하여 하나의 IP주소를 통해 다수의 웹 프로퍼티를 제공할 수 있게 되었고, DIRECTIVE를 사용하면 웹서버는 연결을 끊지 않는것이 허용되었습니다. 이 외에도 Cacheability, Option method, Transfer-Encoding 압축 등 1.0으로부터 대폭 개선된 많은 기능, 정의들이 Http/1.1에 추가되었습니다. 

### HTTP/1.1 이후..
20년을 넘도록 Http/1.1이 사용되어져 왔습니다. 그만큼 Http/1.1의 완성도가 뛰어났다는 것과 1.0으로부터의 사용자의 필요성에 대한 연구가 성공적이었다는 것의 반증이라고 생각되어집니다. 하지만 그만큼 오랜기간 사용되어져 왔다는 것이 완벽하다는 것을 의미하지는 않습니다. 20년이 넘는 시간동안 우리의 시스템은 상상도 못할정도로 격변하였습니다. 일반적으로 우리가 정보획득만을 위해 사용되어져 온 웹이라는 것은 생산과 창출, 부가가치의 창출, 산업의 형성, 생활양식의 변화와 같이 우리 삶의 대부분을 바꿔놓았습니다. 이제는 전세계가 더더욱 많은 가치를 웹에서 찾고 있으며 현재까지도 무궁무진한 잠재적 가치를 찾기 위해 많은 비용을 아끼지 않고 있습니다. 그에 수반되는 웹의 고도화는 당연한 수순이었습니다. 사용자들은 더 많은 것을 원하고 더 새로운 것을 원하게 됩니다. 그에 따라 Http의 발전도 불가피적인 상황을 맞이합니다.

### HTTP/2.0
2012년 차세대 HTTP 프로토콜을 재정할 워킹그룹이 재구성되었습니다. 2.0버전의 출시를 목적으로 하는 그들의 목표는 다음과 같았습니다.

1. 최종사용자의 체감지연시간 개선
2. [HTTP의 HOL블로킹](https://en.wikipdia.org/wiki/Head-of-line_blocking) 해결
3. Congestion control 관련 동작 개선
4. Http/1.1버전의 체계 유지
5. Http/1.1 - 2.0 간 호환성 유지
6. 새로운 확장기능과 정책을 정확하고 명확하게 유지

이러한 내용을 토대로 2015년 5월 14일 HTTP/2는 공식화 되었습니다.

### HTTP/2 의 필요성
오늘날 오래된 프로토콜을 사용해 웹페이지를 빠르게 전송하는 것은 오래된 자동차에 짐을 가득 싣고 드라이빙하는 것과 다름없다고 보여집니다. 예전과는 비교도 할 수 없을만큼 비대해진 컨텐츠의 양을 오래된 프로토콜이 언제까지 견딜수 있을지는 아마 예상하기 힘들 것입니다. 크고작은 문제는 곳곳에 존재합니다. 우리는 이러한 문제들을 확인하고 발전을 기획해야 합니다. 현재 우리가 어떠한 문제에 직면해 있는지 확인할 수 있다면 앞으로의 HTTP/2.0 시대에 발맞추어 부작용 없이 착륙할수 있을거라 생각됩니다. 










