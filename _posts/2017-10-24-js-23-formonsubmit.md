---
layout: post
title: 23. 폼(form)에 onsubmit 이벤트 적용하기
categories: [javascript]
tags: [javascript]
description: 
---

지난 포스트에 이어 이번 포스트에서는 `submit` 버튼 클릭 시 추가적인 유효성 검사를 적용할 수 있는 `onsubmit` 이벤트에 대해 소개한다.

<br>

## onsubmit 이벤트 적용하기

제출 버튼을 누르고 나면 서버와 통신을 하여 사용자가 입력한 정보가 유효한지 검사하고 정보를 (기능에 맞게) 처리한다. 

이 때 `onsubmit` 이벤트를 사용하면 통신을 하기 전에 자바스크립트로 클라이언트 측에서 필요한 추가적인 유효성 검사를 실시할 수 있다. 만약 이 검사를 통과하지 못했을 경우에는 서버와의 통신으로 넘어가지 않아 좀더 효율적이다.

<br>

```js
// onsubmit event
var myForm = document.forms.myForm;
var message = document.getElementById("message");


myForm.onsubmit = function(){
  if (myForm.name.value == ""){
      
      message.innerHTML = "Please enter a name.";
      // return값을 정의해 통신을 하지 않게끔 처리.
      return false;
      
  } else{
      
      message.innerHTML = "";
      return true;
  
  }
};
```

<br>

결과 화면은 다음과 같이 동작한다. 

![onsubmit](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-24/onsubmit.png?raw=true)

<br>

