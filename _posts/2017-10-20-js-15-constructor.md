---
layout: post
title: 15. 생성자 (Constructor)
categories: [javascript]
tags: [javascript]
description: 
---

일반적으로 객체지향 언어에서 생성자는 객체를 생성할 때 사용한다. 생성자를 실행하는 방식은 일반적으로 `new`를 사용하는 방법이 있다.

<br>

```js
var MyArray = new Array();
```

생성자 함수를 만들 때는 보통 **첫 글자를 대문자로** 쓴다.

```js
// 생성자 함수 정의
var Car = function(maxSpeed, driver){
    
    this.maxSpeed = maxSpeed;
    this.driver = driver;
    this.drive = function(speed, time){
        console.log(speed * time);
    };
    this.logDriver = function(){
        console.log(this.driver);
    };
};
```

<br>

## 생성자로 객체 생성하기

다음과 같이 생성자에서 여러 개의 객체를 만들 수 있다. 

```js
var myCar1 = new Car(70, "paul");
var myCar2 = new Car(90, "katie");
var myCar3 = new Car(20, "mina");
var myCar4 = new Car(80, "jason");
var myCar5 = new Car(40, "gordon");

// test
myCar3.drive(70, 3);  // 210
myCar4.logDriver();  // jason
console.log(myCar1.maxSpeed);  // 70
```

<br>

## 생성자 상속 시 주의할 점

자바스크립트에서는 상속을 받으면 상속받은 함수클래스의 `prototype`은 모두 상속해준 함수클래스의 메서드로 오버라이딩된다. 

따라서 `constructor`, 즉, 생성자는 필요시 상속받은 함수클래스의 것으로 변수명을 사용하여 다시 정의해준다. 

```js
Employee.prototype.constructor = Employee;
```

<br>
