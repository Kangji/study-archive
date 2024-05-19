# Pipx

Python application은 어떤 방법으로 설치하던, python runtime에 의해 실행된다.
따라서, Python runtime과 dependency들이 실행 환경에 설치되어 있어야 한다.
만약 여러 Python application을 설치할 경우, 관련 runtime과 dependency들이 모두 local environment에 설치되어 관리가 복잡해진다.

Pipx는 Python application 각각을 고립된 virtual environment에서 관리해주는 application manager이다.
한편, pipx 또한 Python application이기에 pipx로 설치할 수 있긴 하지만, 그러지 말자.

Pipx 공식 repo에서는 MacOS의 경우 Homebrew로 설치하기를 권장한다.
만약 pip로 설치할 경우 pipx가 global python runtime에 의존해서,
pyenv 등으로 global runtime을 관리하는 경우 모든 runtime마다 pipx를 설치하거나 pipx를 사용하기 위해 특정 runtime으로 매번 바꿔줘야 한다.

그러나 Homebrew로 설치할 경우 pipx는 homebrew가 관리하는 별도의 python runtime에 의존하며,
설치경로 또한 homebrew의 root가 되어 편리하다.

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
