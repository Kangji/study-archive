# Django Overview

Django는 DRY(Don't Repeat Yourself)를 철학으로 하는 Python 기반 Web Framework이다.
Django의 기본 structure는 pluggable/reusable app이다,
그리고 MVT(Model-View-Template) 패턴이다.

* Model: Data
* View: fn(request) -> response
* Template: Response template (HTML)

## Django Rest Framework

Modern Web 아키텍쳐는 Frontend와 Backend를 분리하고 서로 RESTful API로 연결하는데,
Django는 Django Rest Framework라는 별도의 Framework를 제공한다.

Django는 Template을 통한 Server-Side Rendered Web page를 제공하는 데 초점을 맞추는 반면,
Django Rest Framework는 JSON 형태의 RESTful API를 지원하는 데 초점을 맞춘다.

## Project Initialization

```sh
pip install django djangorestframework
```

## Project Structure

Django project는 App 단위로 개발되기 때문에 기본적으로 domain driven이다.
프로젝트 특성에 따라 조금씩 다르겠지만 프로젝트 구조의 한 예시는 다음과 같다.

```txt
myproject/
    apps/
        myapp1/
        myapp2/
        myapp3/
        ...
    manage.py
    settings.py
    urls.py
    asgi.py
    wsgi.py
    ...
```

`django-admin startproject`로 프로젝트를 생성할 경우 프로젝트 설정과 관련된 필수 파일들을 자동으로 작성해준다.

## `manage.py`

프로젝트 매니지먼트를 담당하는 command line utility의 entrypoint이다.
대표적인 sub-command들은 다음과 같다.

* `runserver [addrport]`: Run django server
* `startapp appname`: Create django app => 프로젝트 root directory에 appname directory를 생성하고 기본 파일들을 그 안에 생성한다
* `makemigrations`: Create migrations based on the changes of models
    * `settings.py`의 `INSTALLED_APPS`에 등록된 각 APP들을 traverse하며 `migrations/`에 migration을 생성
* `optimizemigrations`: Optimize existing migrations
* `migrate [appname] [migrationname]`: Apply unapplied migrations
    * `settings.py`의 `INSTALLED_APPS`에 등록된 각 APP들을 traverse하며 `migrations/`의 migration을 실행
    * `appname`과 `migrationname`으로 특정 app/version을 지칭하여 upgrade/downgrade 가능
* `shell`: Runs a Python interactive interpreter

전체 command는 [여기](https://docs.djangoproject.com/en/5.0/ref/django-admin/)를 확인.

## `urls.py`

Web application의 URI routing table을 정의하는 곳이다.
각 app마다 작성된 URL configuration을 include하거나 여기서 모든 path를 명시적으로 선언할 수 있다.

암묵적 선언:
```py
# myproject/urls.py
from django.urls import include, path

urlpatterns = [
    path("polls/", include("polls.urls")),   # My "polls" app, always included by `include()`
]

# myapp1/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path("", views.index, name="index"),
]
```

명시적 선언:
```py
from django.contrib import admin
from django.urls import path

urlpatterns= [
    path("polls/", polls.views.index),
]
```

## `settings.py`

프로젝트 및 서버 관리의 편의성을 위해 여러 설정값을 해당하는 constant들을 global namespace에 정의하고 있으며,
필요에 따라 알맞게 수정하거나 custom setting을 추가할 수 있다.

이론적으로는 아무데서나 설정값을 정의하고 수정할 수 있지만,
clean code를 위해 모든 설정값은 이곳에서 관리한다.

그러나 이 또한 Python 파일이기 때문에 Python 문법을 가지고 장난질을 얼마든지 할 수 있으니,
반드시 올바르게 사용해야 프로젝트를 정상적으로 관리할 수 있다.

다음은 몇몇 설정값들의 예시이다.

* `BASE_DIR`: Project의 base(root) directory
* `SECRET_KEY`: Project의 secret key, production에서 노출시키면 안됨.
* `DEBUG`: Debug mode
* `ROOT_URLCONF`: Web application의 URI routing table이 정의된 경로 - 위 structure에선 `myproject.urls` (`myproject/urls.py`)
* `DATABASES`: Database binding configuration.
    ```py
    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": BASE_DIR / "db.sqlite3",
        }
    }
    ```
    * `ENGINE`: Database engine
    * `NAME`: Database name
    * `HOST`: Database host
    * `PORT`: Database port
    * `USER`: Database user
    * `PASSWORD`: Database password
* `DEFAULT_AUTO_FIELD`: Default type of PK(id)
* `INSTALLED_APPS`: Django server에서 활성화된 모든 app들의 list. `manage.py`에서 참조하는 설정값이며, 여기에 추가하지 않아도 `urls.py`에서 routing하는데는 문제 없음.

전체 설정값은 [여기](https://docs.djangoproject.com/en/5.0/ref/settings/)를 확인.
