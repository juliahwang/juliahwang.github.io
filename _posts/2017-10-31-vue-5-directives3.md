---
layout: post
title: 5. 디렉티브 (directives) <3부> 기타 디렉티브들
categories: [Vuejs]
tags: [Vue.js]
description: 
---

지난 포스트에서는 선언적 렌더링을 위해 HTML 요소 내부에서 사용할 수 있는 `디렉티브`(directive)에 대해 알아보았다. 이번 포스트에는 기타 디렉티브들에 대해 정리하였다. 

<br>


## `v-pre` 디렉티브

`v-pre`의 역할은 HTML 요소를 컴파일하지 않고 문자 그대로 출력하는 것이다. 

```html
<body>
<div id="example">
    <span v-pre>{% raw %}{{message}}{% endraw %}</span>
</div>
<script type="text/javascript">
var vm = new Vue({
    el : "#example",
    data : { 
        message : "Hello World"
    }
})
</script>
</body>
```



## `v-once` 디렉티브

`v-once`는 HTML 요소를 한 번만 렌더링한다. `v-once`를 적용한 곳에는 콘솔에서 Vue 객체의 속성값을 변경해도 다시 렌더링되지 않는다. 값을 바꾸지 않을 템플릿 속성이 있을 때 적용한다. 

```html
<body>
<div id="example">
    <span v-once>{% raw %}{{message}}{% endraw %}</span>
</div>
<script type="text/javascript">
var vm = new Vue({
    el : "#example",
    data : { 
        message : "Hello World"
    }
})
</script>
</body>
```

<br>

## 계산형 속성

`v-bind`가 간단한 데이터 바인딩에 사용된다면 좀더 복잡하고 연산이 필요한 부분은 `계산형 속성`(Computed Property)을 사용한다. 

```html
<body>
<div id="example">
    <input type="text" v-model="num" /><br />
    1부터 입력된 수까지의 합 : <span>{{sum}}</span>
</div>
```

아래의 자바스크립트 코드는 <a href="http://juliahwang.kr/algorism-javascript/2017/10/31/algorism-js-1-N%EA%B9%8C%EC%A7%80%EC%9D%98%ED%95%A9%EA%B5%AC%ED%95%98%EA%B8%B0.html" target="_blank">1. 자바스크립트 - N까지의 합 구하기</a>에 자세히 설명해놓았다. 

```js
<script type="text/javascript">
    // 1부터 입력된 수까지의 합 구하기
    var vmSum = new Vue({
        el: "example",
        data : {num : 0},
        computed : {
            sum : function() {
                var n = Number(this.num);
                if (Number.isNaN(n) || n < 1)
                    return 0;
                return (1 + n) * n / 2;
            }
        }
    })
</script>
```

Vue 객체에 `computed` 속성으로 함수를 등록하여 사용할 수 있다. 

<br>
