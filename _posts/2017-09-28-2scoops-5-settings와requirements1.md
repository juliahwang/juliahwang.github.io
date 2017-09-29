---
layout: post
title: Two Scoops of Django - 5장. settings와 requirements 파일 <1부>
categories: [도서 리뷰]
tags: [django, 장고]
description: 
---


장고 1.8에서는 140가지 설정을 제어할 수 있는 세팅 모듈과 버전별 필요 패키지를 기록할 수 있는 `requirements` 모듈을 통해 프로젝트를 실행 환경별로 구동할 수 있도록 관리할 수 있다. 이번 포스트에서는 실행 환경별 최선의 설정 관리법에 대해 알아본다. 2부에서는 그에 따라 요구되는 패키지의 버전 관리에 대해 알아볼 것이다. 

<br>

## 환경별 최선의 settings 모듈 관리하기

장고에서 최선의 설정 방법은 다음을 따른다.

- 버전 컨트롤 시스템(ex_ `git`)으로 모든 설정파일을 관리한다. 어떤 부분을 변경했는지가 문서화되어야 협업환경에서 일어나는 혼란을 방지할 수 있다.
- 반복되는 설정은 기본 설정파일로 관리하고, 실행환경별로 추가적인 세팅을 나누어 관리한다.
- 암호 및 비밀키 등은 버전컨트롤 시스템에서 제외하여야 한다.

<br>

### 버전관리를 반드시 적용하자.

버전 관리에 포함되지 않는 `local_settings 안티패턴`을 사용한 적도 있다. 즉, 개발자에게만 필요한 설정으로, 스테이징이나 운영서버 및 디버그 환경에 대한 설정들 말이다. 이외에도 버전 컨트롤 시스템에 등록하면 안되는 비밀 값이나 개인 정보 등은 로컬 세팅에 등록하여 사용해왔다. 

예를 들어, `local_settings.py`라는 모듈을 생성하여 파일을 각각의 서버나 개발머신에 위치시킨 후 버전 컨트롤 시스템에서 제외해버리면 개발환경에 특화된 설정을 개인적으로 변경할 수 있는 것이다. 또, 스테이징 서버와 개발 서버에서는 버전 컨트롤 관리에 구애받지 않고 설정을 유지할 수 있었다. 하지만 다음의 문제들로 인해 이러한 로컬세팅 설정방식을 지양해야만 한다. 

- 모든 머신에 버전컨트롤에 기록되지 않는 코드가 존재하므로 개발자 별로 공통된 세팅을 적용할 수 없다.
- 운영환경에서 발생하는 버그를 로컬에서 구현해보지만 설정이 달라 동일하게 적용되지 않는다.
- 로컬환경의 `local_settings` 떄문에 생긴 버그가 운영환경에서는 보이지 않는다.
- 개발자가 해당 파일을 복사해서 자신만의 설정을 붙여 사용할 경우 문제는 커진다.

<br>

### 버전 관리의 예외 : 비밀 정보

장고 프로젝트 자체의 시크릿 키 같은 경우에는 절대로 버전 컨트롤 시스템에 노출되어선 안된다. 해당 `SECRET_KEY` 세팅은 장고의 암호화 인증 기능에 사용되고 이 세팅값은 다른 프로젝트의 시크릿 키 값과 겹쳐서는 안된다. 이 키가 외부에 노출되면 장고의 보안 기능을 무력화시킬 수 있기 때문이다. 이 뿐만이 아니라 배포에 사용되는 AWS 키, OAuth 토큰, 여러 데이터베이스 설정정보 등도 마찬가지다. 

이러한 정보를 버전컨트롤 시스템에서 제외시키는 방법에는 여러가지가 있다. 이는 해당 포스트 내 **코드에서 설정 분리하기** 파트에서 더 자세히 다루었다. 

<br>

### 여러 개의 세팅파일로 설정 분리하기
 
설정을 하나의 파일로 사용하기보다는 `settings` 모듈을 만들고 내부에 환경별로 파일을 만들어 분리하도록 한다. 

```powershell
settings/
  __init__.py
  # 프로젝트 내 모든 인스턴스에 적용되는 공통 설정
  base.py
  
  # 로컬환경용. 개발 전용 로컬파일
  local.py
  
  # 운영환경서버 내 프라이빗 버전(관리자페이지)의 설정
  staging.py
  
  # 테스트러너, 인메모리 DB, 로그설정
  test.py
  
  # 운영서버의 실제 운영 설정파일
  production.py
```

**ci.py**

큰 규모의 프로젝트에 특수한 목적으로만 쓰이는 서버가 있을 경우에는 각각의 목적에 맞는 커스텀 세팅 파일을 만들어서 사용한다. 이 `ci.py`파일은 지속적 통합 서버에서 사용된다.

<br>


### 세팅파일 적용하기 

위와 같이 분리된 세팅파일은 터미널에서 다음과 같이 지정하여 사용할 수 있다.

```powershell
# 로컬 세팅파일을 사용하여 서버를 실행할 경우
$ python manage.py runserver --settings=config.settings.local
```

<br>

하지만 계속해서 위와 같은 명령어를 입력하기가 번거로울 수 있다. 따라서 특정 쉘에서 항상 같은 세팅파일을 적용하게 하려면 `DJANGO_SETTINGS_MODULE`과 `PYTHON_PATH` 환경변수를 지정하여 사용할 수 있다. 

```powershell
# DJANGO_SETTINGS_MODULE을 local 서버로 설정
$ export DJANGO_SETTINGS_MODULE=config.settings.local
```

`virtualenv`를 사용할 경우 `postactivate` 스크립트에 위의 명령어를 삽입하여 저장하면 `virtualenv`를 실행할 때 자동으로 해당 설정을 적용시켜 사용할 수 있다.

<br>

### 다중 개발환경 세팅

세팅파일에서 공유하여야하는 기본 `base.py` 설정파일은 버전 컨트롤 시스템에 등록하여 모두가 같은 설정을 사용할 수 있게끔 한다. 장고 세팅파일에서 `base.py`는 유일하게 `import *` 구문을 사용해도 되는 파일이다. 세팅파일의 모든 네임스페이스는 전부 오버라이드해서 사용하기 때문이다.

한편으로, 프로젝트에 참여한 협업자 각각이 자기만의 설정 환경을 사용하고 싶을 때도 있다. 이 때는 **개개인의 필요에 의해 작성된 파일명을 `dev_julia.py`, `dev_katie.py` 처럼 이름을 붙여 이 또한 버전 컨트롤 시스템에서 관리될 수 있도록** 한다. 이렇게 하면 팀원끼리 코드를 공유할 수도 있고 설정이 달라 생기는 차이를 빨리 발견할 수 있기 때문이다. 

<br>

## 코드와 설정 분리하기 

앞서 이야기한 `local_settings 안티 패턴`을 사용한 이유는 서버 구동에 필요한 개인정보 및 비밀 키 등을 버전 컨트롤 시스템에서 제외하기 위함이었다. 이러한 값들은 반드시 세팅 파일에 들어가야하기 때문인데, 설정파일 내의 코드는 그대로 두고 값들만 따로 빼서 관리되지 않게 할 수 있다.

- 비밀 키는 반드시 외부에서 알 수 없어야 하므로 버전 컨트롤 시스템에 포함시키면 안된다.
- 비밀 키는 설정값들이지 코드가 아니다.
- 설정은 배포 환경에 따라 다르지만 코드는 그렇지 않다.
- PaaS 환경에서는 독립된 서버에서 코드를 수정하도록 허용하지 않고 있다.

그렇다면 **설정과 코드의 차이**는 무엇일까? 설정에는 다음과 같은 값을 포함한다. 

- 데이터베이스, 캐시 등의 백엔드 서비스 리소스 핸들
- 아마존 S3나 트위터 등의 외부 서비스 인증정보
- 배포된 호스트의 이름 및 배포 환경마다 달라지는 값들

**앱은 설정을 상수로 코드에 저장하곤 하는데, 배포 관련 프로젝트 설정 지침인 `Twelve-factor app`을 참고하면 설정을 코드에서 엄격하게 분리하는 것이 좋다**고 한다. 즉, 어떠한 인증정보나 비밀 값들도 유출되지 않고 코드베이스가 공개될 수 있다면 이러한 원칙을 지킨 것이다. 

<br>

**분리된 설정값들은 `환경변수`를 이용하여 코드 변경 없이 배포시 쉽게 변경할 수 있도록 관리**하라고 충고하고 있다. 이를 통해 얻는 장점은 다음과 같다. 

- 비밀 키를 세팅파일을 환경변수에 넣으면 걱정없이 세팅파일을 버전 컨트롤 시스템에 올릴 수 있다.
- settings_local 파일을 팀원 각자가 만들어 쓰는 것이 아니라 버전 컨트롤 시스템에서 관리되는 하나의 파일로 공유하여 쓸 수 있다.
- 파이썬 코드 수정 없이 프로젝트 코드를 쉽게 배치할 수 있다.
- 대부분의 PaaS 기반 시스템들이 환경변수 사용을 장려하고 있다.

환경변수를 사용함으로서 얻는 효과는 <a href="https://12factor.net/ko/config" target="_blank">더 읽어보기(12factor app 홈페이지)</a>를 참고하기 바란다.

<br>

### 로컬에서 환경변수 설정하기

맥과 리눅스의 경우에는 다음 명령어를 `bashrc`, `.bash_profile`, `.profile` 등에 추가해준다. 

```powershell
$ export <KEY>=<value>
$ export SOME_SECRET_KEY=fdsjlfk-sdfjlsfjl-dsfjlsdkfj
```

<br>

윈도우 시스템에서는 명령행(`cmd.exe`)에서 `setx` 명령어를 사용하여 하나하나 설정해준 후 명령창을 재실행해주어야 한다. 이는 매우 번거로우므로 `virtualenv` 내 `bin/activate.bat` 스크립트에 원하는 환경변수를 추가해주면 된다. 그러면 `virtualenv`가 실행되면서 설정이 바로 적용될 것이다.

```powershell
> set SOME_SECRET_KEY=djlfsdjlk-sdfjlsdfjlkds-sdkfjslkfj
```

<br>

### 운영환경에서 환경변수 설정하기

운영 환경은 사용하는 배포 도구나 서버 설정에 따라 다른 방법을 사용하지만 파이썬을 사용할 경우 다음과 같이 지정하고 불러와 사용한다. 

```powershell
$ export DJANGO_SETTINGS_MODULE=config.settings.local
$ python manage.py shell_plus  # 환경변수가 적용된 인터프리터 실행
In [1]: import os

In [2]: os.environ["DJANGO_SETTINGS_MODULE"]
Out[2]: 'config.settings.local'
```

위 방식을 활용하여 설정 파일 내에서 환경변수 값을 불러온다.

```python
# settings/production.py

import os
SOME_SECRET_KEY = os.environ["SOME_SECRET_KEY"]
``` 

<br>

### 비밀키가 존재하지 않을 때?

위의 방식으로 설정파일에서 비밀 값들을 임포트해와 사용한다. 그러나 운영환경에서 불러올 환경변수가 지정되어 있지 않다면 에러가 발생하고 프로젝트 실행에 문제가 생긴다. 또, 임포트해올 값들이 많을 경우 단순 `KeyError` 메세지는 별 도움이 되지 않을 것이다. 따라서 환경변수가 존재하지 않을 때 원인을 알려주는 예외처리가 필요하다. 

```python
# settings/base.py
import os
from django.core.exceptions import ImproperlyConfigured

def get_env_variable(var_name):
    """
    환경변수를 가져오거나 커스텀 예외문구를 반환
    """
    try:
        return os.environ[var_name]
    except KeyError:
        error_msg = "Set the {} environ variable".format(var_name)
        raise ImproperlyConfigured(error_msg)

SOME_SECRET_KEY = get_env_variable("SOME_SECRET_KEY")
``` 

다음 메서드를 실행하여 환경변수가 존재하지 않을 경우에는 다음과 같이 커스텀 에러메세지를 확인할 수 있다. 

```powershell
django.core.exceptions.ImproperlyConfigured: Set the SOME_SECRET_KEY environ variable.
```

<br>

**세팅 모듈에서 장고 컴포넌트 임포트는 금물!**

어떤 세팅파일에서라도 장고 컴포넌트는 임포트하면 안된다. 위에서 사용한 `ImproperlyConfigured`는 에러메세지에 문제가 되는 세팅 이름을 표시해주기 위하여 예외적으로 사용해 준 것이다. 일반적으로 **장고로부터 무언가를 설정파일에 임포트할 일은 아예 없고 해서도 안된다!**

<br>

**manage.py 대신 django-admin.py를 사용하자**

<a href="https://docs.djangoproject.com/en/1.8/ref/django-admin/" target="_blank">장고 공식문서 - django-admin</a>에 따르면 여러 세팅 파일이 있을 경우에는 `django-admin.py`를 사용하여 환경변수 세팅 명령어를 실행하는 것을 추천하고있다. 하지만 `manage.py`를 사용해도 무방하다. 원문은 아래를 참고하기 바란다.

> Generally, when working on a single Django project, it’s easier to use manage.py than django-admin. **If you need to switch between multiple Django settings files, use django-admin with DJANGO_SETTINGS_MODULE or the --settings command line option.**
 
<br>

## 환경변수를 사용할 수 없을 때

환경변수를 사용할 경우에는 저장할 비밀정보를 어떻게 관리할 것인지와 어떤 배포환경을 적용할 것인지를 먼저 생각해야한다. 특히 아파치는 독립적인 환경변수 시스템을 가지고 있기 때문에 운영체제에서 설정해준 환경변수가 운영환경에서 작동하지 않을 것이다. 또, `Nginx`를 사용할 경우에도 환경 변수를 사용하는 방식이 작동하지 않을 수 있다. 

이 떄는 `local_settings 안티패턴` 보다는 **비밀파일 패턴**을 사용한다. 즉, 장고에서 실행되지 않는 형식의 파일 (`JSON`, `Config`, `YAML`, `XML` 등의 파일)을 버전 컨트롤 시스템에서 제외하여 사용하는 것이다.

<br>

### JSON 파일 이용하기

다음과 같이 설정값만 저장할 `secret.json` 파일을 만들어준다.

```json
{
    "DJANGO_KEY": "<VALUE>",
    "FACEBOOK_API_KEY": "<VALUE>"
}
```

위의 값을 임포트하여 사용하기 위해서는 다음과 같이 세팅 모듈에 코드를 추가해준다.

```python
# setings/base.py

import os

from django.core.exceptions import ImproperlyConfigured


# JSON 기반 비밀 모듈 (도서)
with open("secret.json") as f:
    secrets = json.loads(f.read())

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

def get_secret(setting, secrets=secrets):
    """
    비밀변수를 가져오거나 명시적 예외를 반환
    """
    try:
        return secrets[setting]
    except KeyError:
        error_msg = "Set the {} environ variable".format(setting)
        raise ImproperlyConfigured(error_msg)
        
SECRET_KEY = get_secret("DJANGO_KEY")
```

<br>

---

## 마치며

이번 장에서는 프로젝트 환경에 맞는 설정과 관련한 내용을 공부했다. 이번 포스트의 중점은 버전 컨트롤 시스템에서 관리할 수 있는 설정과 아닌 것들을 잘 구분하자는 것이다. 그리고 보안에 대비하여 공개되어서는 안되는 설정값들을 따로 관리하여야 한다. 

<a href="http://juliahwang.kr/%EB%8F%84%EC%84%9C%20%EB%A6%AC%EB%B7%B0/2017/09/29/2scoops-5-settings%EC%99%80requirements2.html" target="_blank">5. settings와 requirements <2부></a>에서는 설정환경 별로 필요한 패키지와 버전을 관리하는 `requirements`에 대해 알아볼 것이다. 또, 설정에서 템플릿 및 미디어 파일에 대한 경로를 설정하는 방법에 대해서도 알아보자. 

 <br>
 
