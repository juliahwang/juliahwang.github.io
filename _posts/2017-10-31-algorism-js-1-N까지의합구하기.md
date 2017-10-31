---
layout: post
title: 1. 자바스크립트 - N 까지의 합 구하기
categories: [algorism-javascript]
tags: [Vue.js]
description: 
---

이번 포스트에서는 자바스크립트로 1부터 N까지의 합을 구하는 알고리즘을 풀이한다. 

<br>

## 코드 및 풀이

`this`의 개념은 매우 중요하다. 함수 안에서 `this`는 도큐먼트 객체를 참조한다. 만약 함수 내부에서 다른 콜백 함수를 실행하면 `this`는 다른 값으로 연결될 수 있으므로 주의하여야 한다. 

<a href="http://juliahwang.kr/javascript/2017/10/19/js-14-this.html" target="_blank">this 더 알아보기</a>


```js
var num = 100;

var sumN = function() {
    # num 값을 숫자형으로 명시적 변환
    var n = Number(this.num);
    
    # 
    if (Number.isNaN(n) || n < 1)
        return 0
    return (n + 1) * n / 2;
};

sumN()
// 5050
```

HTML 요소 내부에서는 num 값이 문자열로 다루어진다. 

따라서 `Number()` 함수나 `parseInt()` 함수를 사용하여 명시적으로 숫자 값으로 변환해준 후 연산을 시행한다. 

```js
> Number("100");
< 100

> parseInt("100");
< 100
```

<br>

## isNaN()

`isNaN(value)`는 Number의 내장함수로, `value`가 NaN 자료형인지 boolean 자료형으로 출력해준다. 

```js
> Number.isNaN("100");
< false

> Number.isNaN(100);
< false

> Number.isNaN("*" * 100);
< true
```

<br>

```js
// 문자형은 난수가 아니다.
> isNaN("100");
< false

// 숫자형도 난수가 아니다.
> isNaN(100);
< false

// 문자형과 숫자형을 연산하면 NaN 자료형이 출력된다.
> isNaN("*"*100);
< true
```

<br>

## 논리 연산자에 대한 간략 소개

조건식에서 활용하는 논리연산자에 대해 간단히 소개한다.

<br>

### `||` (OR)

논리연산자 중 `||`를 사용하면 조건이 하나 혹은 모두 참인 경우 `true`를 반환하고 그에 따른 실행문을 실행한다.

<br>

### `&&` (AND)
 
논리연산자 중 `&&`를 사용하면 조건을 모두 만족해야 `true`를 반환하며 그에 따른 실행문을 실행한다. 

### `!` (NOT)

논리연산자 중 `!`를 사용하면 피연산자가 true일 경우 반대인 false를, false일 경우 true를 반환하고 그에 따른 실행문을 실행한다. 

<br>

### false로 평가되는 값들

```js
> null;
> NaN;
> 0;
> empty string("");
> undefined;
```

<br>

