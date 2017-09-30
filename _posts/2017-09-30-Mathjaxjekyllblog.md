---
layout: post
title: Mathjax로 지킬 블로그에 수식 적용하기 
categories: [Jekyll Blog]
tags: [mathjax, blog]
description:
---

알고리즘 관련 포스팅을 하면서 수식을 사용할 필요성이 생겼다. 브라우저에 수식을 렌더해주는 `MathJax`를 지킬 블로그에 적용하는 방법을 소개하려고 한다. 웹에서 비교적 쉬운 방법을 찾아 번역하였다. 출처는 포스트 하단을 참고하기 바란다.

<br>

$$
   |\psi_1\rangle = a|0\rangle + b|1\rangle
$$

적용한 후에는 위와 같이 브라우저에서 수식이 표시되는 것을 볼 수 있다. 

<br>

## 1. `_include` 폴더에 mathjax 설정 생성하기
 
기본 테마나 지금 사용중인 블로그 테마에는 수식이 적용되지 않아서 알아본 결과 `Mathjax`를 사용하겠다는 설정파일을 추가하고 수식이 있는 마크다운 파일에 옵션값을 주면 해당 파일에만 `Mathjax`가 적용된다. 

원글에서는 마크다운 프로세서를 `maruku`로 변경하라고 했지만 테마를 사용하는 사용자들은 테마에서 이미 사용하고 있는 프로세서와 충돌할 수 있으므로 기존 설정인 `kramdown`으로 남겨두었다. (~~그래도 별다른 문제없이 설정됐다.~~)

```js
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    // 해당 부분은 태그 옆에 숫자를 표시해준다. 싫다면 생략.
    // TeX: {
    //  equationNumbers: {
    //    autoNumber: "AMS"
    //  }
    },
    tex2jax: {
      inlineMath: [ ['$','$'], ['\(', '\)'] ],
      displayMath: [ ['$$','$$'] ],
      processEscapes: true,
    }
  });
</script>
<script type="text/javascript"
        src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

<br>

## 2. 페이지 기본 레이아웃에 설정 적용하기

다음과 같이 `default.html`이나 `layout.html`에 설정을 추가한다. `<head>` 태그 내에 페이지, 즉 마크다운 파일 하나하나를 등록할 때마다 `use_math` 설정이 `true`이면 앞서 추가해준 `mathjax_support.html`을 렌더해준다는 의미이다.  

```html
<!DOCTYPE html>
<html>
  <head>
    ...
    {% if page.use_math %}
      {% include mathjax_support %}
    {% endif %}
  </head>
  <body>
    ...
  </body>
</html>
```

<br>

## 3. 포스트 상단에 `use_math` 변수 등록하기

게시물에서 수학 수식을 사용하려면 게시물의 앞부분에 `use_math` 변수가 있어야하며 해당 값을 true로 설정해준다.

```txt
---
layout: post
title: "Test math"
author: Haixing Hu
category: misc
tags: [test]
use_math: true
---
```

포스트 최상단에 위와 같이 설정을 적어주고 수식을 작성하면 브라우저에서 수식이 표현된다. 

<br>

## 4. `Mathjax`의 간단한 규칙 소개

- 인라인 수식은 `$ ... $`와 같이 적용한다.
- 블록 수식은 `$$ ... $$`와 같이 적용한다.
- 예를 들어 `\begin{equation} ... \end{equation}` 또는 `\begin{align} ... \end{align}`과 같이 수학 환경을 직접 사용할 수 있다.

- 인라인 또는 블록영역에서 `*`는 이스케이프 처리해야 한다. 
- 여러 줄의 수식을 작성하는 경우, 줄바꿈 기호 이중 백슬래시 '\\'는 이스케이프처리한다. 
	- 즉, 네 개의 백 슬래시 '\\\\'를 사용하면 된다. 수학 수식을 입력하는 동안 오류가 발생하면 특수 문자를 이스케이프 처리하면 된다.

<br>

---

원문 출처 : <a href="https://haixing-hu.github.io/programming/2013/09/20/how-to-use-mathjax-in-jekyll-generated-github-pages/" target="_blank">How to use MathJax in Jekyll generated Github pages</a>

<br>
