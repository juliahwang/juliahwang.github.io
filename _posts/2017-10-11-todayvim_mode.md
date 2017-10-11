---
layout: post
title: 오늘의 vim - vim 에디터의 다양한 모드들
categories: [Vim]
tags: [Vim]
description: 
---

`오늘의 vim`에서는 알아두면 유용한 `vim` 단축키 및 기능을 하루에 하나씩 정리합니다. 이번 포스트에서는 빔 에디터에서 지원하는 6개의 모드들에 대해 소개합니다.

<br>

## Vim의 모드들

빔 에디터는 6개의 모드를 지원한다. 간단하게 어떤 모드들이 있는지 알아보자.

### 1. Normal (=Command) mode

- 빔 에디터를 실행했을 때(~~아무런 초기모드 옵션이 없을 때~~) 기본 창의 상태이다.
- 해당 모드에서 입력되는 글자들은 모두 명령으로 인식된다.
- `evim` 인터페이스를 사용하는 경우에는 기본 창이 `Insert mode`이다. (이 때는 `Ctrl + O`를 통해 `Normal mode`로 변경할 수 있다.) 
- `Command mode`라고도 부른다.
- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Normal-mode`을 입력하면 된다.

<br>

### 2. Insert mode

- 새로운 텍스트를 입력하거나 수정, 삭제를 할 경우 사용할 수 있는 편집모드이다.
- 커서 키가 활성화된다.
- 다양한 단축키로 해당 모드를 실행할 수 있다.

|단축키|설명|
|:--:|:--:|
|`a`|커서 위치 뒤에 추가|
|`i`|커서 위치 앞에 삽입|
|`A`|해당 줄의 끝에 추가|
|`I`|해당 줄의 처음에 삽입|
|`C`|해당 줄 전체를 선택하여 삭제하고 대체|
|`s`|커서 앞의 문자 대체|

<br>

- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Insert-mode`을 입력하면 된다.

<br>

### 3. Visual mode

- `Normal mode`와 비슷하지만 이동 명령을 하이라이트 영역에서 사용할 수 있다.
- 비이동 명령들을 사용할 경우 하이라이트된 영역에 적용된다.
- `showmode` 옵션이 활성화되어있을 경우 창 하단에 `--VISUAL--` 표시가 뜬다.
- `Visual mode`에는 `plain`, `block`, `linewise` 이렇게 3가지의 강조 모드가 지원된다. 
- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Visual-mode`을 입력하면 된다.

<br>

#### (1) Plain visual mode

- 기본 `Visual mode`의 형태로 이동명령을 수행한다.
- 만약 명령이 원하는 대로 동작하지 않았을 경우에는 `u`(undo)를 통해 이전 상태로 되돌릴 수 있다.

<br>

#### (2) block visual mode

![block1](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-12/block1.png?raw=true)

블록 형태로 원하는 영역을 선택하여 `Insert mode`에서 텍스트를 삽입(변경)할 수 있다.

<br>

![block2](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-12/block2.png?raw=true)

여러 줄에 특정 문자를 동시에 삽입하고 싶을 경우 편리하다.

<br>

![block3](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-12/block3.png?raw=true)

`Insert mode`에서 `c`를 입력하면  문자를 변경할 수 있다. 

`Insert mode`에서는 커서의 위치에만 입력한 문자가 표시된다.(모드를 나갈 때까지는 선택한 블록영역에 적용되지 않는다.)

<br>

![block4](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-12/block4.png?raw=true)

`ESC`를 누르면 변경하려는 문자가 선택한 영역 모두에 적용된다.

- 문자를 변경하지 않고 입력하고 싶은 경우에는 `I` 또는 `A`를 사용한다. 
- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help visual-block`을 입력하면 된다.

<br>

#### (3) linewise visual mode

![linewise](https://github.com/juliahwang/juliahwang.github.io/blob/master/_posts/images/2017-10-12/linewise.png?raw=true)

- `plain visual mode`와 유사하다.
- 다른 점이 있다면 모드를 실행했을 때 커서가 있는 줄 전체가 하이라이트 처리된다.
- 화살표 키로 여러 줄을 선택할 수도 있다.

<br>

#### (4) 모드별 단축키 요약

||plain|block|linewise|
|:---:|:---:|:----:|:-----:|
|실행|`Normal mode`에서 `v`|`Normal mode`에서 `Ctrl+V` 또는 `Ctrl+Q`(`cmd` 아님)|`Shift+V`|
|종료|`ESC` 또는 `v`|`ESC`|`ESC`|
|동작|선택된 영역에 이동명령 수행|선택된 여러줄의 블록영역에 문자 삽입 또는 변경|커서가 위치한 줄 전체를 선택가능|
|돌아가기|`u`|-|-|

<br>

### 4. Select mode

- `Visual mode`와 비슷하지만 사용가능한 명령어가 다르다.
- 출력가능한 문자열을 입력할 경우 선택된 부분을 삭제하고 `Insert mode`로 변한다.
- `showmode` 옵션이 활성화되어 있을 경우 창 하단에 `--SELECT--` 표시가 뜬다.
- 다음과 같이 명령행에 입력하여 활성화시킨다. 

|단축키|설명|
|:--:|:--:|
|`gh`|`Normal mode`에서 셀렉트 모드 활성화 |
|`gH`|`Normal mode`에서 셀렉트 모드 활성화|
|`g+CTRL+H`|`Normal mode`에서 셀렉트 모드 활성화|
|`CTRL+G`|`Visual mode`에서 셀렉트 모드 활성화|

- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Select-mode`을 입력하면 된다.

<br>

### 5. Command-line mode

- 에디터 창 하단에 명령어를 입력할 수 있는 모드이다.
- `Normal mode`에서 `:`를 입력하면 해당 모드가 활성화되며 창 하단에 명령어를 입력할 수 있다.  

```txt
:set number
:help Command-line-mode
```

- `/`와 함께 찾으려는 문자를 입력하면 파일내에서 검색이 가능하다.
- 정규표현식을 사용하여 일치하는 패턴을 찾을 수도 있다.

```txt
/\<worth\>

정규표현식을 사용하면 heyworth, worthless와 같은 단어도 검색이 된다.
```

- 명령어 입력 후에는 에디터가 `Normal mode`로 돌아온다.
- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Command-line-mode`을 입력하면 된다.

<br>

### 6. Ex mode

- `Command-line mode`와 비슷해서 실행되면 `Ex mode`도 실행된다.
- 하지만 커맨드 라인 모드와 다르게 `Normal mode`로 자동으로 돌아오지 않는다. 
- Ex 모드는 일괄처리(batch processing)를 위해 설계되었으므로 매핑이나 명령줄 편집은 지원하지 않는다.
- 일괄처리를 위해서는 빔을 실행할 때 `-E`를 입력하여 바로 해당 모드를 활성화할 수 있다.
- 되돌아오려면 `:visual`을 입력한다.
- 더 자세한 설명을 보려면 빔 에디터창 명령행에 `:help Ex-mode`을 입력하면 된다.

<br>

---

## 마치며

참고자료

<a href="https://en.wikibooks.org/wiki/Learning_the_vi_Editor/Vim/Modes" target="_blank">Learning the vi Editor/Vim/Modes</a>



