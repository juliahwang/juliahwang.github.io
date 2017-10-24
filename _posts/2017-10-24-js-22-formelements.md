---
layout: post
title: 22. 폼(form) 엘리먼트 접근하기
categories: [javascript]
tags: [javascript]
description: 
---

HTML 요소 중 `form`을 구성하는 태그들은 자바스크립트 `document.forms`로 접근할 수 있다. 이번 포스트에서는 폼에 이벤트를 적용하는 것까지 실습을 통해 정리하였다.

<br>

 `form` 태그 내 `name` 속성이 `myForm`이라면 다음과 같이 접근하여 변수에 할당할 수 있다. 

```js
> var myFormTag = document.forms.myForm
< undefined 

> myForm
< <form data-brackets-id="322" action="#" id="my-form" name="myForm">…</form>

// name 속성값으로 원하는 폼 태그에 접근할 수 있다.
> myForm.name
< <input type=​"text" name=​"name">​

// 아직 아무것도 입력하지 않았을 때 value는 빈값으로 출력된다.
> myForm.name.value
< ""

// 입력창에 hello!!!라고 작성한 후 다시 입력해보면 입력한 값이 출력된다. 
> myForm.name.value
< "hello!!!"
```

<br>

## 폼 태그에 이벤트 적용하기

```js
// 입력 중일 때 이벤트를 선언하여 테두리 스타일을 다르게 지정
> myForm.name.onfocus = function(){

    myForm.name.style.border = "4px solid pink";
    
};
```

하지만 포커스가 사라진 후에도 보더 스타일은 그대로 적용되어 있다. 

![onfocus](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-24/onfocus.png?raw=true)

위의 문제를 해결하려면 `onfocus`와 반대되는 기능인 `onblur` 이벤트를 적용해주면 된다. 

```js
> myForm.name.onblur = function(){
    
    myForm.name.style.border = "1px solid gray";

};
```

이제는 입력하지 않을 때 기본 테두리스타일이 적용된다. 

<br>
