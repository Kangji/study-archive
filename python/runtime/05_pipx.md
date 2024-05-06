# Pipx

Python application은 어떤 방법으로 설치하던, python runtime에 의해 실행된다.
따라서, Python runtime과 dependency들이 실행 환경에 설치되어 있어야 한다.
만약 여러 Python application을 설치할 경우, 관련 runtime과 dependency들이 모두 local environment에 설치되어 관리가 복잡해진다.

Pipx는 Python application 각각을 고립된 virtual environment에서 관리해주는 application manager이다.
한편, pipx 또한 Python application이기에 pipx로 설치할 수 있긴 하지만, 그러지 말자.

Pipx 공식 repo에서는 MacOS의 경우 Homebrew로 설치하기를 권장한다.
그러나 Homebrew로 설치할 경우 pipx를 homebrew가 관리하는 Python runtime에 설치하려고 하기 때문에 (`{python_path}/bin/`),
Homebrew는 dependency인 최신 버젼 Python runtime을 설치한다.
필자는 pyenv로만 Python runtime을 관리하고 싶기 때문에 Homebrew로 Python application을 설치하는 것을 지양한다.

Pip로 설치할 경우 pip와 동일한 Python runtime을 사용하게 된다.

## Configuration

Pipx 관련 data는 `$HOME/.local/`에 저장한다.
* `bin`: 설치한 executable
* `pipx/venvs`: 각 application별 virtual environment

따라서 `$HOME/.local/bin`을 PATH에 추가해주어야 한다.

## Usage

```sh
pipx install {app}
pipx uninstall {app}
```

## Pitfalls

만약 설치하려는 application이 특정 project에서 사용된다면 (ex. `uvicorn`) 그 application은 해당 프로젝트의 가상환경에 설치하는 게 옳다.
