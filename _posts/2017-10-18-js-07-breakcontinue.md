---
layout: post
title: 7. break와 continue
categories: [javascript]
tags: [javascript]
description: 
---

이번 포스트에서는 자바스크립트의 루프문과 조건문에서 사용할 수 있는 `break`, `continue`에 대해 정리하였다. 

<br>


## Break & Continue

`break`는 코드의 실행을 종료시킨다. 반면 `continue`는 코드를 건너뛰고 계속 진행시킨다. 

if문을 사용하여 다음과 같이 10 이하일 때 7에 도달하면 for문을 종료시키는 코드를 작성할 수 있다.

```js
for (i = 0; i < 10; i++) {
    
    console.log(i);
    
    if (i == 7) {
        
        break;
        
    }
}

console.log("I have broken out of the loop.")
```

실행시 다음과 같이 출력된다.

![break](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-18/break.png?raw=true)

<br>

다음으로 `continue`를 사용한 조건문이 있는 for문을 작성하였다. 

```js
for (i = 0; i < 10; i++) {
    
    if (i === 5 || i === 3) {
        
        continue;
    
    } 
    
    console.log(i);
    
    if (i == 7) {
        
        break;
        
    }
}

console.log("I have broken out of the loop.")
```

<br>

실행시 다음과 같이 출력된다. (3, 5일 때는 로그 출력을 건너뛰었다.)

![break](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-18/breakncontinue.png?raw=true)

<br>

