# Django App Overview

Django project **App**이라는 단위로 서비스를 분리하여 관리한다.
Django app은 `python manage.py startapp myapp`으로 템플릿을 생성할 수 있다.

## App Structure

App structure의 예시이다.

```txt
myapp1/
    apps.py
    exceptions.py
    management/
        commands/
            mycommand1.py
            mycommand2.py
    migrations/
        0001_initial.py
    models.py
    serializers.py
    templates/
        uripath/
            index.html
    tests.py
    urls.py
    views.py
```

## `apps.py`

Project의 config가 `settings.py`였다면 App의 configuration은 `apps.py`에 설정.
App과 그 Configuration을 나타내는 `django.apps.AppConfig`를 상속하는 클래스를 만들어 사용.
클래스 명은 `<AppName>Config`로 자동 생성됨. 설정값들은 class attribute.

* `name`: `settings.py`에 `INSTALLED_APPS`에 넣어줄 App 이름
* `default_auto_field`: `settings.py`의 `DEFAULT_AUTO_FIELD`가 default.

## `management/commands/`

`manage.py`에 추가할 app의 command를 작성하는 directory. Command는 파일명이다.

```py
class Command(BaseCommand):
    def add_arguments(self, parser): ...
    def handle(self, *args, **options): ...
```

## `migrations/`

`makemigrations`로 생성되는 migration 파일들을 저장하는 directory.
Migration 파일명은 자동 생성된다.

## `models.py`

앱의 data model들을 여기서 정의한다.

## `serializers.py`

Django Rest Framework에서 JSON을 model로, model을 JSON으로 변환하는 일을 담당한다.

## `templates/`

렌더링할 템플릿 파일들을 저장하는 directory.

## `tests.py`

여기에 테스트 작성

## `urls.py`

이 앱의 routing table을 작성하고 project의 `urls.py`에 `include("app_name.urls")로 nesting해줘야 한다.

## `views.py`

이 앱의 view들을 여기서 작성한다.
view는 `fn(request) -> response`, 즉 앱의 비즈니스 로직이다.
`render`로 template을 rendering해서 return할 수 있다.

View는 FBV(Function-Based View), CBV(Class-Based View), 그리고 ViewSet이 있다.
