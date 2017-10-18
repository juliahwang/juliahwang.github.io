---
layout: post
title: 5. 비교연산자와 논리연산자
categories: [javascript]
tags: [javascript]
description: 
---

이번 포스트에서는 자바스크립트의 비교연산자(Comparison Operators)와 논리연산자(Logical Operators)에 대해 소개한다.

<br>

## 1. 비교연산자 (Comparison Operators)


```js
> var x = 5;
undefined

> x > 4
true

> x > 5
false
> x >= 5
true

> x < 5
false
> x <= 5
true

// 값 비교
x == 5
true

// 자료형 비교 
x === 5
true

x = "5"
"5"

// x의 값은 문자형이고 5는 숫자형이므로 false를 출력한다.
x === 5
false

x == 5
true
x === "5"
true

// 값이 다른지를 비교할 때는 '!'를 사용한다.
x = 5
5
x != 4
true
x != 5
false
x !== 5
false

// 이 때는 숫자형 값을 가진 x가 문자형과 자료형이 다르므로 true를 출력한다.
x !== "5"
true
```

<br>

## 2. 논리연산자 (Logical Operators)

```js
// 14_logicaloperator.js

var myAge = 40;

// '&&' 는 and 연산자와 같다.
if (myAge >= 18 && myAge <= 30) {
    
    document.write("You can enter the club!");
    
} else {
    
    document.write("You ain't coming.");
    
}

// '||' 는 or 연산자와 같다.
if (myAge < 15 || myAge > 30) {
    
    document.write("You ain't coming.");
    
} else {
    
    document.write("You can enter the club!");  
}

// '||' 는 or 연산자와 같다.
if (myAge < 18 || myAge > 30 || myAge === 25) {
    
    document.write("You ain't coming.");
    
} else {
    
    document.write("You can enter the club!");  
}
```

<br>
