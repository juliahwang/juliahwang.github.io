---
layout: post
title: 2. vue파일에서 MVVM 패턴 구현하기
categories: [Vue.js]
tags: [Vue.js]
description: 
---

이번 포스트에서는 앞선 포스트에서 만든 예제를 바탕으로 Vue.js의 `MVVM 패턴`을 설명해보았다. 

<br>

## Vue.js의 `MVVM` 패턴

Vue는 `Model`-`View`-`ViewModel`으로 구성된 `MVVM 패턴`을 따른다.

**어플리케이션 로직과 UI를 분리하기 위해 설계된 패턴으로, UI를 가지는 응용 프로그램을 위한 아키텍처 패턴**이다. 

장고에서 일반적으로 사용하는 `MVC 패턴`의 변형으로 뷰를 추상화하여 재사용하거나 테스트하기 쉽게 만든 것이다. 

모델과 뷰는 `MVC 패턴`과 동일하다. 다른 점은 `MVC 패턴`의 컨트롤러(Controller)가 `MVVM 패턴`에서는 뷰모델(ViewModel)의 역할을 한다는 것이다. 

<br>

![msdn-mvvm](https://i-msdn.sec.s-msft.com/dynimg/IC416621.png)

- View 
	- 유저 인터페이스. 
	- HTML/CSS/XML/YAML 등으로 작성

- View Model 
	- 상태와 연산(명령)
	- View의 실제 논리 및 데이터 흐름을 담당한다.
	- 상태 데이터를 변경하면 즉시 View에 반영된다.

- Model 
	- 도메인 특화 데이터

<br>

`뷰모델(ViewModel)`은 '뷰의 모형'으로 이해하면 되며, 세 가지 특징이 있다. 

첫째, '추상화된 뷰 상태(ViewState)'로 존재한다. 뷰는 HTML과 CSS를 조합하여 사용자에게 시각적으로 접근한다면 뷰모델은 이 뷰를 `추상화`하여 텍스트 입력기 컨트롤을 추상화하거나 뷰 상태 컬렉션을 사용하는 식이다.
추상화된 뷰모델은 구체적인 뷰에 대한 정보를 가지고 있지 않기 때문에 다양한 플랫폼에서 재사용할 수 있다는 장점이 있다. 

두번째로, 모델에서 제공하는 데이터를 사용자 인터페이스에 맞게 '상대적인 표현'으로 변환하여 표기하는 경우 뷰모델은 `값 변환기`(ValueConverters)를 가진다. 

마지막으로, 사용자가 서비스에서 특정 동작을 수행할 수 있도록 그 수단을 제공하는 것도 뷰모델의 `명령`(Commands) 기능이 있기 때문이다. 

그렇다면 추상화된 뷰와 실제 뷰를 연결해줄 수 있는 수단이 필요한데, `MVVM 패턴`에서는 기존의 컨트롤러가 했던 작업흐름 제어의 역할보다는 뷰와 뷰모델의 상태를 동기화해줄 `데이터 바인딩(Data Binding)`을 필요로 한다. 해당 요소를 통해 뷰모델이나 뷰가 변경되었을 경우 서로에게 변경사항이 반영되는 것이다.

참고 1 - <a href="https://justhackem.wordpress.com/2017/03/05/mvvm-architectural-pattern/" target="_blank">MVVM 아키텍처 패턴 - 프로그래머 이규원의 블로그</a> (~~꼭 읽어보길 추천한다~~)

참고 2 : <a href="https://msdn.microsoft.com/en-us/library/ff798384.aspx" target="_blank">Implementing the Model-View-ViewModel Pattern - MSDN</a> 

<br>

## 예제에서 알아보기

이전 포스트에서 `simple` 템플릿으로 만든 html 파일 코드는 다음과 같다. 아래 파일은 html과 javascript 코드로 구성되어 있는 하나의 파일이다. (블록을 구별하기 쉽게 나누어 설명하였다.)

```html
<!--index.html-->

<!DOCTYPE html>
<html>
<head>
  <title>Start Vue.js</title>
  <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
</head>
<body>
  <div id="simple">
    <!--보간법을 사용하여 문자열을 삽입하는 {{}} 형태의 템플릿표현식-->
    <h2>`{{`message`}}`</h2>
  </div>
```

```js
  <script type="text/javascript">
    // 데이터를 저장하는 model 객체 선언 
    var model = {
      message : '첫번째 vue 앱이다!'
    };
    
    // Vue 객체이자 ViewModel 객체인 simple 객체 선언
    var simple = new Vue({
      // HTML 요소를 나타냄
      el : '#simple',
      // 모델 객체 참조
      data : model
    })
  </script>
```

위에서 데이터(모델)가 변경되면 뷰모델 객체는 즉시 html 요소(뷰)에 반영한다.
 
```html
</body>
</html>
```

위의 코드로 모든 작업은 반응형을 지향한다. 즉, 모델이 변경되면 뷰모델 객체를 통해 HTML DOM이 즉시 변경된다.

위의 코드를 `MVVM 패턴`에 각각 대입해보면 다음과 같다.

### 1) View

사용자에게 보여줄 틀, 구조

```html
<div id="simple">
  <h2>{{ message }}</h2>
</div>
```

<br>

### 2) ViewModel 

뷰 객체이자 뷰모델 객체를 선언하여 뷰(구조) 와 모델(데이터)를 연결하고 보여줄 정보를 제어.

지금 예제에서는 Vue 객체가 데이터만 가지고 있지만 MVVM 패턴에서는 상태(state, 데이터)와 연산(operations, 메서드) 모두 가질 수 있다.

```js
var simple = new Vue({
  el : #simple,
  data : model
})
```

<br>

### 3) Model 

보여줄 데이터를 담은 객체를 선언하고 저장.

```js
var model = {
  message : "hello!"
}
```

<br>

---

## 마치며

Vue.js 프레임워크의 기본 동작원리를 파악하는 것은 매우 중요하다. 특히 ViewModel이 어떻게 뷰와 모델을 가지고 추상화되는지에 대해서는 심화적인 공부가 필요할 것 같다. 

해당 포스트는 웹에서 얻은 참고 자료들과 원형섭님의 [Vue.js Quick Start] 도서를 바탕으로 공부한 내용을 요약, 정리하였다. 
<br>
