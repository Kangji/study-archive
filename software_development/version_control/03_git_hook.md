# Git Hook

Git도 다른 버전 관리 시스템처럼 어떤 이벤트가 생겼을 때 자동으로 특정 script를 실행하도록 할 수 있다.
이 hook은 client hook과 server hook으로 나눌 수 있다.
Client hook은 커밋이나 Merge 할 때 실행되고 server hook은 Push 할 때 서버에서 실행된다.

## Installation

Hook은 repo의 `.git/hooks/`에 저장한다.
실행할 수 있는 script 파일을 확장자 없이 저장소의 hooks 디렉토리에 넣으면 hook script가 켜진다.
Hook은 실행할 수만 있으면 되고, Shell script 말고 Perl이나 Python, Ruby 등이어도 상관 없다.

한편, 이 directory에는 git에 기본으로 포함된 유용한 예제 script가 몇 개 있다.
예제 script의 파일 이름에는 `.sample` 확장자가 붙어 있어서 이름만 바꿔주면 바로 사용할 수 있다.

## Commit Hooks (Client)

Client hook은 여러 가지가 있지만 이 중 commit과 관련된 4가지 hook을 알아보자.

### Pre-Commit

Pre-commit hook은 commit할 때 가장 먼저 호출되는 hook으로 commit msg를 작성하기 전에 호출된다.
이 hook은 commit하려는 snapshot을 점검한다. 예를 들어 linter나 formatter 등을 실행할 수 있다.
이 hook의 exit code가 0이 아니면 commit은 취소된다.

Commit할 때 `--no-verify` 옵션을 주면 이 hook을 생략할 수 있다.

한편, pre-commit이라는 CLI tool을 사용하여 편하게 pre-commit hook을 작성할 수 있다.
이 tool은 Python으로 작성되었으며, Python 프로젝트라면 가상환경에 dev-dependency로 추가하길 권장한다.

Repo에 `.pre-commit-config.yaml` 파일을 추가하여 pre-commit hook을 설정할 수 있다.
예시는 다음과 같다.

```yaml
epos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v2.3.0
    hooks:
    -   id: check-yaml
    -   id: end-of-file-fixer
    -   id: trailing-whitespace
-   repo: https://github.com/psf/black
    rev: 22.10.0
    hooks:
    -   id: black
-   repo: local
    hooks:
      - id: check-django-migrations
        name: check for missing django migrations
        entry: python ./manage.py makemigrations
        language: system
        types: [python]
        pass_filenames: false
        args: [--check, --no-input, --settings=settings.ci]
```

마지막 hook은 직접 hook을 만든 것이고, 나머지는 이미 존재하는 hook을 가져다 사용하는 것이다.

`.pre-commit-config.yaml`을 작성했다면, `pre-commit install`로 pre-commit hook을 설치할 수 있다.

### Prepare-Commit-Msg

Prepare-commit-msg hook은 git이 commit msg를 생성하고 나서 editor를 실행하기 전에 실행된다.
주로 사람이 commit msg를 직접 수정하기 전에 실행하여 commit msg를 자동으로 생성하기 위해 사용한다.

### Commit-Msg

Commit-msg hook은 최종적으로 commit이 완료되기 전에 프로젝트나 commit msg를 검증한다.

### Post-Commit

Post-commit hook은 commit이 완료된 후에 실행되며, 일반적으로 commit된 것을 누군가에게 알리기 위해 사용된다.

## Push Hooks (Server)

Server hook은 모두 push hook이다. 즉, push와 관련된 hook이다.
GitHub에서 branch protection은 push hook을 사용한다.

### Pre-Receive

Pre-receive hook은 push하면 가장 처음 실행되는 hook이다.
Fast-forward push가 아니면 거절하는 등 push 권한을 control할 수 있다.

### Update

Update hook은 브랜치마다 실행된다는 점을 제외하면 pre-receive hook과 동일하다.
여러 branch를 push하면 pre-receive hook은 한 번만 실행되지만, update hook은 push한 브랜치마다 실행된다.

### Post-Receive

Post-receive hook은 push한 후에 실행된다. 이 hook으로 사용자나 서비스에 알림을 보낼 수 있다.
한편, post-receive hook의 실행이 종료될 때까지 client와의 연결은 유지되고 push를 중단시킬 수 없다.
따라서 post-receive로 오래 걸리는 일을 할 땐 조심해야 한다.
