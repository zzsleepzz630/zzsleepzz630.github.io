---
title: (자알쓰) Scope Part. 1
date: 2017-04-27 00:14:47
category: [Programming, ECMAScript, 자알쓰]
tag: [JS, ES, 자알쓰, Scope]
---
![](thumb.png)

## 자알쓰란?
`자`바스크립트 `알`고 `쓰`자. (잘 쓰자는 의미도 담겨있다.)  
자바스크립트라는 언어 자체는 내 기준에서는 설계 상 미스가 참 많다.  
함수 단위의 스코프, 호이스팅, 동적 타입 등등  
자바와 같은 깐깐(?)한 언어를 배우고 바라본 자스는 허점 투성이처럼 보였다.  
애초에 자바스크립트는 어떠한 프로그램을 만들기 위해서 탄생했다기 보다는  
웹 페이지에 입력값에 대한 유효성 검사(데이터가 공란인지 아닌지 등등)와 같은  
페이지의 동적 제어가 주된 목적 + 짧은 개발 기간(넷 스케이프 사의 새로운 브라우저에 탑재 예정) 때문에  
설계 상에 미스가 있을 수 밖에 없다고 나는 생각된다.  
일종의 안전 장치가 없어서 개발자가 일일이 구현해주고, 신경써야 하는 느낌이었다.  
그렇다고 해서 자바스크립트를 극혐하거나 그런 것은 아니고 매우 사랑한다.  
또한 그 허점을 아는 사람은 허점을 보완해서 요리조리 피해서 잘 쓰겠지만...  
잘 모르는 부분들은 잘못 써도 동작이 잘 되기 마련이다.  
이는 지금 당장에는 큰 문제가 안 될지 모르겠지만, 추후에 대규모 웹 어플리케이션을 만들거나  
직면할 문제로부터 미리 해방시키기 위해 처음부터 좋은 습관을 들여가는 것이 좋다고 생각한다.  
그 세 번째 시리즈는 Scope(스코프)를 주제로 진행하겠다.  

## Scope? 스코프?  
흔히 변수의 Scope라고 부른다.  
> Scope
(무엇을 하거나 이룰 수 있는) 기회; (주제・조직・활동 등이 다루는) 범위

후자의 `범위`라고 보면 될 것 같다.  
즉 변수의 Scope는 `변수를 사용할 수 있는 범위`가 된다.  
ES5까지는 `함수 단위의 스코프`를 가진다.  
백문이 불여일견, 일단 코드로 보고 이해하자.  
```javascript
// 전역 공간은 스코프를 공유한다.
var a = 1;
var b = 2;
if(a < b) {
  var i = 3;
} else {
  // 사실 전역에 있는 변수는 공유되기 때문에 여기서는 var를 생략해도 된다.
  var i = 4;
}
var c = function() { // 함수 단위의 스코프가 시작됐다.
  var a = 5;
  var d = 6;
  console.log(a); // 5, 현재 스코프에 a가 있기 때문에 현재 스코프에 있는 a를 출력한다.
}
console.log(i); // 3, 함수 단위의 스코프이기 때문에 같은 전역 공간에 있다고 간주해서 변수를 공유하기 때문에 사용 가능하다.
c();
console.log(d); // Uncaught ReferenceError: d is not defined, 함수 단위의 스코프이기 때문에 함수에서 쓰인 변수는 함수에서만 사용할 수 있다.
```

이것 또한 전통 방식의 자바, C와 큰 차이점 중 하나이다.  
기존 프로그래밍 언어들은 블록 단위의 스코프라고 해서 if 문도 별도의 스코프를 가졌는데  
자바스크립트에는 쌩뚱맞은 함수 단위의 스코프라는 게 존재한다.  
왜 이렇게 만들었는지 나도 모르겠다.  

## Scope Chaining(스코프 체이닝)  
> Chaining  
체이닝, 연쇄(적 처리)

뭔가를 연쇄적으로 처리하는 내용이다.  
이번에도 코드를 봄으로써 이해해보자.  
```javascript
var a = 1;
var b = function() { // 외부 함수 스코프(b)의 시작
  var c = 2;
  var d = function() { // 내부 함수 스코프(d)의 시작
    // 1. 현재 스코프(d)에는 a란 변수가 없다.  
    // 2. 스코프 체인을 타고 스코프 b로 올라간다.
    // 3. 스코프 b에도 a란 변수가 없다.  
    // 4. 스코프 체인을 타고 전역 스코프로 올라간다.  
    // 5. 전역 스코프에는 a란 변수가 있으므로 그 a를 출력한다.
    console.log(a); // 1
    
    // 1. 현재 스코프(d)에는 c란 변수가 없다.  
    // 2. 스코프 체인을 타고 스코프 b로 올라간다.
    // 3. 스코프 b에도 c란 변수가 있으므로 그 c를 출력한다.  
    console.log(c); // 2
  }
  d(); // 내부 함수 d를 호출
}
b(); // 함수 b를 호출
```

하지만 이 스코프 체이닝은 변수 사용에서만 존재하는 게 아니다.  
변수 선언에 있어서도 스코프 체이닝이 발생할 수 있다.  
```javascript
var b = 1;
var c = function() { // 외부 함수 스코프(c)의 시작
  var d = 2;
  var e = function() { // 내부 함수 스코프(e)의 시작
    // 변수의 선언을 var 없이 하면 스코프 체이닝이 발생한다.  
    // 1. 현재 스코프(e)에는 d라는 변수가 없다.
    // 2. 스코프 체인을 타고 상위 스코프인 c로 이동한다.  
    // 3. 스코프 c에는 변수 d가 있으므로 그 변수에 3을 할당한다.
    d = 3;
    
    // 1. 현재 스코프(e)에는 d라는 변수가 없다.
    // 2. 스코프 체인을 타고 상위 스코프인 c로 이동한다.  
    // 3. 스코프 c에는 변수 b가 없다.
    // 4. 스코프 체인을 타고 상위 스코프인 전역 스코프로 이동한다.
    // 5. 전역 스코프에는 변수 b가 있으므로 그 변수에 4를 할당한다.
    b = 4;
    
    // 1. 현재 스코프(e)에는 d라는 변수가 없다.
    // 2. 스코프 체인을 타고 상위 스코프인 c로 이동한다.  
    // 3. 스코프 c에는 변수 b가 없다.
    // 4. 스코프 체인을 타고 상위 스코프인 전역 스코프로 이동한다.
    // 5. 전역 스코프에는 변수 f가 없으니 새로 만들고 5를 할당한다.
    f = 5;
  }
  // 똥 싸기 전에는 2를 가지고 있다가
  console.log(d); // 2
  
  e(); // 내부 함수 e를 호출
  
  // 똥싸고 나니 3으로 변하였다.
  console.log(d); // 3
}
// 똥 싸기 전에는 1을 가지고 있고 f는 벌써 호이스팅 처리가 전부 끝난 건지 5가 할당되어있다.
console.log(b); // 1
console.log(f); // 5

c(); // 함수 c를 호출

// 똥싸고 나니 3과 5라는 변수로 바뀌었다.
console.log(b); // 4
console.log(f); // 5
```

이렇게 var를 쓰지 않고 변수를 선언하면 상위 스코프로 체인을 타고 해당 변수를 찾아 여정을 떠난다.  
전역 스코프에도 해당 변수가 없다면 새로 만들게 된다.  
이렇게 var를 쓰지 않고 변수를 선언하다보면 변수의 스코프가 뒤죽박죽이 되어버린다.  
따라서 이렇게 코딩하는 걸 사전에 방지하고 오류를 뿜어주는 게 존재한다.  

## strict mode
ES5에서 새로 생긴 모드다.  
이 포스트에서는 변수 선언에 대해서만 다룰테니 자세한 내용은 아래 링크를 확인하자.  
[Strict mode - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode)  
코드의 문법을 좀 더 깐깐하게 검사해서 오류를 내뿜어 개발자로 하여금 의도한 바대로 실행 결과를 도출하게끔 하는 모드이다.  
나는 사용을 권장한다.  
혹시 IE8 같은 경우에는 지원하지 않아서 오류를 뿜지 않을까 걱정이라면 붙들어 메도 된다.  
일단 코드로 보자.  
```javascript
'use strict';
// 이렇게 전역 스코프 상단에 저 구문을 넣어주거나 
// 사용하려는 함수 바디 상단에만 넣어주면 된다.  
// 미지원 브라우저는 그냥 문자열로 인식하고 오류를 뿜지 않는다.
var a = 1;
var b = function() {
  // 얘는 전역 스코프에 존재하는 녀석이어서 그 변수를 덮어씌우면 되니 오류를 뿜지 않는다.
  a = 1;
  // 스코프 체인 상에 존재하지 않는 변수를 var 없이 선언하면 오류를 뿜게 된다. 
  d = 2; // Uncaught ReferenceError: d is not defined
}
// 스코프 체인 상에 존재하지 않는 변수를 var 없이 선언하면 오류를 뿜게 된다. 
c =  3; // Uncaught ReferenceError: c is not defined
b();
```

## 아주그냥 난장판인 스코프
하나의 html 파일에는 여러가지 자바스크립트 파일이 들어가게 된다.  
그럼 2개의 자바스크립트 파일을 썼다고 가정해보고 코드를 보자.  
```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="a.js"></script>
  <script src="b.js"></script>
</head>
<body>
<button id="a">a</button>
<button id="b">b</button>
</body>
</html>
```

a.js를 작성해보자.  
```javascript
'use strict';
var num = 1;
```

b.js를 작성해보자.  
```javascript
window.onload = function() {
  'use strict';
  var btnA = document.getElementById('a');
  var btnB = document.getElementById('b');
  btnA.onclick = function() {
    console.log(num);
  };
  btnB.onclick = function() {
    console.log(num);
  }
};
```

버튼을 누르면 1이 출력된다.  
그러다가 내 부사수 마니똘이 추가 작업을 하다가 아래와 같이 c.js를 만들고 html에 추가하였다.  
```javascript
'use strict';
// 코드 블라블라
var num = 2;
// 코드 블라블라
```

그리고 b.js 보다 먼저 처리해야할 로직이 있어서 스크립트를 아래와 같은 순서로 로딩하게 하였다.  
```html
<script src="a.js"></script>
<!-- 요 사이에 내 부사수가 작성한 c.js를 로딩시켰다. -->
<script src="c.js"></script>
<script src="b.js"></script>
```
그리고 나서 다시 버튼들을 클릭해보자.  
그럼 내가 기대했던 1이 아닌 부사수가 작성한 2가 찍히게 된다.  
사실 위 3가지 js 파일을 아래와 같이 바꿀 수도 있다.  
```javascript
'use strict';
// 내가 작성한 a.js
var num = 1;

// 내 부사수가 작성한 c.js
// 블라블라
var num = 2;
// 블라블라

// 내가 작성한 b.js
window.onload = function() {
  'use strict';
  var btnA = document.getElementById('a');
  var btnB = document.getElementById('b');
  btnA.onclick = function() {
    console.log(num);
  };
  btnB.onclick = function() {
    console.log(num);
  }
};
```

num이 더 뒤에 할당된 2가 찍히게 되는 것이다.  
자바의 경우에는 파일(클래스) 별로 별도의 스코프가 존재했는데, js는 그렇지 못하다.  
이 또한 기존에 다른 언어 개발자들을 미치게 하는 요인 중에 하나다.  

## 마치며...
글이 길어져서 일단 한 템포 끊어서 가야할 것 같다.  
다음 포스트에서는 [ES2015+의 스코프](/2017/05/19/js-004-scope/)를 다루고,  
그 다음에는 스코프의 한계를 극복한 [모듈화](/2017/05/20/js-005-module/)에 대해 다뤄야할 것 같다.  