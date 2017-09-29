---
layout: post
title: Two Scoops of Django - 5장. settings와 requirements 파일 <2부>
categories: [도서 리뷰]
tags: [django, 장고]
description: 
---


여러 개의 세팅파일을 적용할 경우 각 세팅파일에 해당하는 `requirements` 파일도 따로 관리해주어야 한다. 이는 서버마다 그 환경에 필요한 컴포넌트 및 패키지만 설치하자는 뜻이다. 이번 포스트에서는 패키지의 버전 관리를 도와주는 `requiremnets` 파일에 대해 알아볼 것이다. 또, 설정에서 템플릿 및 미디어 파일에 대한 경로를 설정하는 방법에 대해서도 알아보자. 

<br>

## 여러 개의 requirements 파일 이용하기

저장소 루트 아래 requirements/ 폴더를 생성하여 세팅폴더 내 파일들과 동일한 이름의 텍스트 파일을 생성한다. 

```powershell
requirements/
  base.txt
  local.txt
  staging.txt
  production.txt
```

<br>

`base.txt`에는 모든 설정 환경에 걸쳐 공통적으로 설치할 의존성을 넣어준다. 

```txt
Django==1.8.0
psycopg2==2.6
djangorestframework==3.1.1
```

<br>

`base.txt`를 사용하면서 로컬환경에서만 사용하는 `local.txt`는 다음과 같이 작성한다. 

```txt
-r base.txt 
coverage==3.7.1
django-debug-toolbar==1.3.0
```

`-r base.txt`는 `base.txt`의 의존성을 포함한다는 명령어이다.

<br>

### 설치

각 환경에 따라 다음과 같이 파이썬 패키지 관리자 명령어 (`pip`)를 사용하여 설치할 수 있다.

```powershell
$ pip install -r requirements/local.txt
$ pip install -r requirements/production.txt
```

<br>

## settings와 파일 경로

여러 개의 settings 파일이 있는 경우 프로젝트 내부의 템플릿과 미디어 파일을 찾을 수 있도록 경로를 설정할 때 주의할 점은 **하드 코딩하지 말라**는 것이다.

1부에서 잠깐 언급하였지만, 장고의 기본 설정파일에는 상대 경로를 설정할 수 있도록 프로젝트의 `BASE_DIR` 설정을 제공하고 있다. 

`BASE_DIR`은 `base.py`의 위치에 따라 결정되기 때문에 어떤 설정이라도 상대적 위치만 정해놓으면 프로젝트를 구동할 수 있게 해준다. 

1부에서 사용한 방식으로 `os` 모듈의 `join` 메서드를 사용해도 되고, 도서에서는 `Unipath`라는 파이썬의 경로 정의 패키지를 소개하고 있다. 

### (1) os.path 라이브러리 사용

```python
# settings/base.py

# 상대 임포트를 위해 지정해준 BASE_DIR 경로
BASE_DIR = os.path.dirname(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

# 저장소루트 경로
ROOT_DIR = os.path.dirname(BASE_DIR)

# 비밀값들이 저장된 폴더 경로
SECRET_DIR = os.path.join(ROOT_DIR, '.secret')

# 시크릿 JSON 파일의 위치
SECRET_FILE = os.path.join(SECRET_DIR, 'secret.json')

# 시크릿 파일 내부를 읽어와 그 값을 저장 (실사용 방식)
secrets = json.loads(open(SECRET_FILE).read())
```

<br>

### (2) Unipath 패키지 사용

```python
# settings/base.py

from unipath import Path

BASE_DIR = Path(__file__).ancestor(3)
MEDIA_ROOT = BASE_DIR.child("media")
STATIC_ROOT = BASE_DIR.child("static")
STATICFILES_DIRS = BASE_DIR.child("assets")
```

<br>

---

## 마치며

이상으로 여러 개의 세팅을 사용할 때 그에 따른 의존성을 `requirements`로 관리하는 방법에 대해 정리해보았다. 또, 부가적인 템플릿 및 미디어파일 또한 경로설정을 통해 상대적으로 지정해줄 수 있다는 것을 배웠다. 

무엇보다도, **보안에 관계된 값을 제외한 모든 설정은 버전 컨트롤로 관리하는 것이 좋다.** 패키지 관리도 마찬가지다. 특히 오픈소스가 강조되는 요즘 시대에서 패키지 버전관리는 버전 컨트롤에 필수적으로 포함시키는 것이 좋다.

추가로, 커스텀 설정을 적용할 경우 어떤 부분이 달라졌는지(~~마치 git diff처럼~~) 알고싶다면 콘솔에서 해당 파일에 대해 `diffsettings` 명령어를 실행해볼 수 있다. 

<br>
