---
layout: post
title: 4. 디렉티브 (directives) <2부> v-if, v-show, v-for
categories: [Vuejs]
tags: [Vue.js]
description: 
---

지난 포스트에 이어 선언적 렌더링을 위해 HTML 요소 내부에서 사용할 수 있는 `디렉티브`(directive) 중 `v-show`, `v-if`에 대해 알아보고자 한다.

<br>

## `v-if`와 `v-show`의 차이

`v-if` 디렉티브는 Vue 객체의 속성에 따라 렌더링 여부를 결정할 수 있는 기능이다. `v-show`도 `v-if`랑 비슷한 기능을 하지만 **실제 렌더링 여부**에 따라 다르다. 

`v-show` 디렉티브는 일단 HTML 요소를 렌더링한 후`display` 스타일 속성으로 화면에 보여줄지 여부를 판단한다. 따라서 모든 데이터가 렌더링이 일단 되고 `display: none`에 의해 표시되지 않는다.

반면 `v-if` 디렉티브는 조건에 부합되지 않으면 렌더하지 않는다. 아예 데이터 속성을 불러오지 않는 것이다. 

<br>

## `v-show`를 언제 쓸까?

`v-show`는 조건을 만족하지 않을 때 `display` 속성을 `none`으로 표시하므로 자주 화면이 변경되는 부분에 적용한다. 

도서의 예제에서 마이너스 숫자형이 입력되면 에러 마크가 표시되는 코드를 소개했다. 

디렉티브를 사용한 부분만 보면 다음과 같다.

```html
<div id="account">
    예금액 : <input type="text" v-model="amount">
    <img v-show="amount < 0" src="images/error.png"
      title="마이너스는 허용하지 않습니다."
      style="width:15px; height:15px; vertical-align:middle;" />
  </div>
```

`v-show` 디렉티브를 사용하면 실제 조건이 충족되지 않았음에도 이미지가 콘솔에 렌더링되어 있는 것을 확인할 수 있다. 

![v-show](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-19/v-show.png?raw=true)

<br>

반면 `v-if`를 사용한 경우 조건이 충족되지 않으면 콘솔창에 렌더링되지 않는다는 것을 확인할 수 있다.

![v-if](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-19/v-if.png?raw=true)

<br>

## `v-if` 사용하기

`if` 조건문을 사용할 수 있는 디렉티브는 `v-if`, `v-else-if`, `v-else`가 있다. 

각각 파이썬의 `if`, `elif`, `else`와 동일한 개념이다. 

```html
<div id="account">
    잔고 : <input type="text" v-model="balance" />
    <br />
    회원님의 등급 : 
    <span v-if="balance >= 1000000">Gold</span>
    <span v-else-if="balance >= 500000">Silver</span>
    <span v-else-if="balance >= 200000">Bronze</span>
    <span v-else>Basic</span>
  </div> 
```

계좌 잔고에 따라 보여지는 등급이 달리 표시되게 하려면 `v-if` 디렉티브를 사용한다. 조건에 만족하지 않으면 아예 렌더되지 않으므로 적합하다.

<br>

## 반복 렌더링 디렉티브 `v-for`

반복적인 데이터는 `v-for` 디렉티브를 사용하여 렌더링한다. 

### (1) 데이터를 반복하는 경우 

![v-for](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-19/v-for.png?raw=true)

(~~위의 번호는 더미 번호이다.~~)

다음과 같이 반복되는 값은 모델 내 배열 데이터에서 반복적으로 꺼내와 렌더해준다. 

<br>

**인덱스 반복 출력하기**

만약 배열 데이터에서 인덱스를 출력해주고 싶을 때는 다음과 같이 index 수를 증가시켜준다. 

```html
<tr v-for="(contact, index) in contacts">
  <td>{{ index + 1 }}</td>
  ...
```

<br>

### (2) 객체를 반복하는 경우

객체의 경우에는 키를 사용하여 값에 접근하는 해시맵(`HashMap`) 구조이기 때문에 `key`와 `value` 값을 얻어낼 수 있는 구조를 사용한다.


```html
<div id="example">
  <select id="regions">
    <option disabled="disabled" selected>지역을 선택하세요</option>
        <option v-for="(val, key) in regions" :value="key">{{ val }}</option>
  </select>
</div>
```
```js
<script type="text/javascript">
    var regions = {
        "A": "Asia",
        "B": "America",
        "C": "Europe",
        "D": "Africa",
        "E": "Oceania"
    }

    var list = new Vue({
      el : '#example',
      data : {regions : regions}
    })
</script>
```

`key` 값은 value 속성으로 들어가고, `val` 값은 실제 드롭다운 옵션에 표시된다. 

<br>

**인덱스 반복 출력하기**

객체 데이터의 경우 인덱스는 다음과 같이 표시할 수 있다. 

```html
<div id="example">
  <select id="regions">
    <option disabled="disabled" selected>지역을 선택하세요</option>
    <option v-for="(val, key, index) in regions" :value="key">{{ index + 1 }} : {{ val }}</option>
  </select>
</div>
```

<br>


## `v-for`과 `v-if`

`v-for`과 `v-if` 디렉티브는 함께 사용할 수 있다. 이 때 항상 `v-for`이 먼저 수행되고 나서 `v-if`가 적용된다. 

```html
<tbody id="contacts">
  <tr v-for="(contact, index) in contacts" 
    v-if="contact.address.indexOf('서울') > -1">
    <td>{{ index + 1 }}</td>
    <td>{{ contact.name }}</td>
    <td>{{ contact.tel }}</td>
    <td>{{ contact.address }}</td>
  </tr>
</tbody>
```

<br>

## 여러 개의 요소를 한번에 반복 렌더링하기

여러 개의 HTML 요소를 반복하려면 `template` 태그를 써서 `v-for` 디렉티브를 적용하려는 모든 요소를 감싸주면 된다. 

```html
<tbody id="contacts">
  <template v-for="(contact, index) in contacts">
    <tr>
      <td>{{ contact.no }}</td>
      <td>{{ contact.name }}</td>
      <td>{{ contact.tel }}</td>
      <td>{{ contact.address }}</td>
    </tr>
    
    <!-- index를 5로 나누었을 때 나머지가 4일 경우 -->
    <tr class="divider" v-if="index % 5===4">
      <td colspan="4"></td>
    </tr>
  </template>
</tbody>
```

<br>

## `v-for`과 `key` 특성

Vue.js는 가상 DOM을 지원한다. 가상 DOM은 렌더링 속도를 빠르게 하기 위해 변경된 부분만을 업데이트한다. 

`v-for`로 렌더링한 배열 데이터의 순서가 바뀌면 DOM 요소(HTML DOM Element, 즉, 여기서는 `li` 태그를 의미함)를 이동시키지 않고 기존 DOM 요소의 데이터(자체)를 변경한다. (이는 태그 자체를 이동시키지 않고 내부의 콘텐트만 바꾼다는 의미이다.)

<br>

### 배열 데이터 변경의 문제점

`v-for` 디렉티브를 주로 출력하는 데이터는 배열 속성을 가진다. 

배열 데이터를 인덱스번호로 직접 변경할 경우 콘솔에서 변경해도 화면에서는 바뀐 값으로 렌더링되지 않는다.

```js
list.contacts[0] = {no:100, name:"james", tel:"010-2020-3030", address:"제주"};
```

따라서 배열값을 직접 변경해주려면 `Vue.set` 메서드를 사용한다. 

```js
// Vue.set(엘리먼트, 인덱스 번호, 변경할 데이터)
Vue.set(list.contacts, 0, {no:100, name:"james", tel:"010-2020-3030", address:"제주"})
```

<br>

하지만 인덱스로 찾은 배열의 속성을 직접 변경할 경우에는 Vue 인스턴스 내부의 감시자(Watcher)가 추적하여 화면을 변경해준다.

```
list.contacts[0].name = "james";
```

`v-for` 디렉티브는 `push`,`pop`, `shift`, `unshift`, `splice`, `filter`, `contact`, `slice`, `reduce` 등과 같은 배열 메서드를 사용할 경우 감시자에 의해 변경을 추적할 수 있다.

<br>

## DOM 요소의 위치 직접 변경하기 `:key`

만약 DOM 요소를 추적해 그 위치를 직접 변경하고 싶은 경우에는 `v-bind` 디렉티브와 함께 `key` 속성에 고유값을 지정해준다. 

```html
<tbody id="contacts">
  <template v-for="(contact, index) in contacts">
    <tr :key="contact.no">
      <td>{{ contact.no }}</td>
      <td>{{ contact.name }}</td>
      <td>{{ contact.tel }}</td>
      <td>{{ contact.address }}</td>
    </tr>
  </template>
</tbody>
```

<br>

--- 

## 마치며 

해당 포스트는 웹에서 얻은 참고 자료들과 원형섭님의 [Vue.js Quick Start] 도서를 바탕으로 공부한 내용을 요약, 정리하였다. 

<br>
