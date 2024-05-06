# Pyenv

Python을 쓰다 보면 보통 여러 버젼의 runtime을 사용하게 된다.
여러 버전의 runtime을 설치하는 것은 apt나 Homebrew 등 여러 방법이 있지만,
global Python version을 매번 변경하는 것은 번거로운 일이다.

Pyenv는 여러 버전의 runtime을 설치하고, global Python version을 관리하는 tool이다.
Pyenv는 Python으로 개발되지 않아 Python에 의존하지 않는다.
따라서 Python을 설치하지 않아도 Pyenv를 설치할 수 있다.

게다가, Python runtime은 설치 방법에 따라 설치되는 경로가 달라서 runtime 관리가 생각보다 번거롭다.
그러니 컴퓨터를 새로 샀다면, 그리고 Python을 쓸 일이 있다면 Pyenv부터 깔고 보자.

## Installation

필자가 MacOS를 사용하니 MacOS 기준으로 정리한다.

```sh
brew install pyenv
```

Homebrew로 설치했으니 PATH 설정은 불필요하다.
만약 필요한 경우 PATH 설정을 해주자.

## Runtime Management

Pyenv는 관리하는 Python runtime이나 symlink 등 data를 `$PYENV_ROOT`에 저장한다.
예를 들어, pyenv로 설치한 runtime들은 `$PYENV_ROOT/versions/{version}/`에 위치한다.
Default `PYENV_ROOT`는 `$HOME/.pyenv`이다([Configuration](#configuration) 참조).

Global version selection은 다음과 같은 순서를 따른다.

1. `$PYENV_VERSION` 환경변수
2. CWD의 `.python-version` 파일
3. (Recursive) 부모의 `.python-version` 파일
4. `$PYENV_ROOT/version` 파일

## Configuration

Zsh 기준, `~/.zshrc`에 다음을 추가하자.

```zsh
export PYENV_ROOT="$HOME/.pyenv"                                # Default $PYENV_ROOT
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH" # Add Pyenv-managed versions to $PATH
eval "$(pyenv init -)"                                          # Add pyenv as shell function
```

## Usage

```sh
pyenv install {version} # Install runtime
pyenv global {version}  # Set global runtime
```
