# Virtual Environment

자바는 maven으로, Rust는 Cargo로 패키지 관리를 할 수 있다면,
파이썬은 가상환경을 이용하여 프로젝트별로 runtime environment를 고립시켜 사용한다.
하나의 가상환경은 python interpreter와 dependency들로 구성된다.

## Creation

```sh
# install virtualenv package first
$ pip install --upgrade virtualenv

# change to project directory
$ cd /your/project/directory

# create virtual environment
# Conventionaly, `venv` or `.venv`
$ virtualenv venv

# or create virtual environment with python interpreter version specified
# make sure the specified version of interpreter is installed on your PC
$ virtualenv venv --python={version}
```

## Activation

```sh
# activation
$ source venv/bin/activate

# check python interpreter version
(venv) $ python --version
Python 3.9.1

# check python interpreter location
(venv) $ which python
/your/project/directory/venv/bin/python
```

## Deactivation

```sh
# deactivation
(venv) $ deactivate

# after deactivation
$ 
```

## Package Mangement

가상환경이 활성화된 상태에서 `pip` 등을 이용해 패키지를 설치하면 전역이 아닌, 활성화된 가상환경에 패키지가 설치된다.
주로 프로젝트 개발 시 필요한 패키지가 많기 때문에, `requirements.txt` 파일을 이용하여 한 번에 관리한다.
마찬가지로 requirements.txt라는 파일명은 관습이다.

```sh
# create requirements.txt
(venv) $ pip freeze > requirements.txt

# install requirements.txt
(venv) $ pip install -r requirements.txt
```

`pip freeze` 명령어를 사용하면 설치된 패키지들과 그 버젼을 확인할 수 있다.
Shell pipe를 이용해서 그 내용을 `requirements.txt`에 담을 수 있다.

`pip install`에서 `-r` 옵션을 사용하면 파일을 인자로 받아서 그 파일 안의 패키지를 명시된 버젼으로 설치할 수 있다.
