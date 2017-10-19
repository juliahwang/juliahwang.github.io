---
layout: post
title: 10. 숫자형 자료와 NaN
categories: [javascript]
tags: [javascript]
description: 
---

이번 포스트에서는 자바스크립트의 숫자 자료형과 자료형 연산시 등장하는 `NaN`의 개념에 대해 소개한다.

<br>

## 숫자 자료형(Numbers)의 연산과 연쇄

```js
// 더하기 연산, add operation
var a = 7;
var b = 5; 
console.log(a + b);
console.log(typeof (a + b));

// 연쇄, concatenation
var a = "7";
var b = 5; 
console.log(a + b);
console.log(typeof (a + b));
```

숫자형에 `""`를 붙일 경우 문자형으로 변환된다. 문자형과 숫자형 자료를 더하면 띄어쓰기 없이 합쳐진다. 결과는 다음과 같이 나온다.

![number](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-19/number.png?raw=true)

<br>

## NaN (Not a Number)

```js
var a = "8";
var b = 4;


console.log(a * b); // 32
```

위 로그의 결과는 32다. '22'에서 '+' 연산은 하나의 변수가 문자형일 때 두 수를 연쇄(concatenation)적으로 붙여서 출력했다면 '*' 연산은 그대로 연산을 진행한다. 

<br>

```js
var c = "apple";
var d = 3;

// 변수 'c' 가  NaN인지를 조건화하였다.
if (isNaN(c)) {
    
    console.log("That int isn't a number.");
    
} else{
    // 숫자형이라면 곱하기 연산결과를 출력한다.
    console.log("meaning of life is " + (c * d));

}

console.log(c * d);
```

<br>

## 이중부정 

다음은 이중부정을 사용한 조건문이다.

```js
// 이중부정, double negation
// 'c'의 값이 숫자라면 (= 숫자가 아닌게 아니라면)
if (!isNaN(c)) {
    
    console.log("meaning of life is " + (c * d));
    
}
```

<br>
