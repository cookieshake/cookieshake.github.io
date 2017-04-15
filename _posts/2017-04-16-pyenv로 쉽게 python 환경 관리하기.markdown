---
layout: post
title:  "pyenv로 쉽게 python 환경 관리하기"
date:   2017-04-16 03:01:00 +0900
categories: python
tags: python pyenv 팁
---
{% raw %}
Python은 그 쓰임새 만큼이나 개발 환경이 다양해서 우리를 힘들게 합니다. Python2 vs. Python3은 기본이고, 필요한 Python 모듈마다 지원하는 버전이 다르곤 합니다. 그래서 필요에 따라 여러 Python 버전을 설치하는 경험을 하게 됩니다.

설치된 여러 Python들끼리 잘 지내주었으면 좋겠지만, 아쉽게도 우리는 쉽게 동족상잔의 비극을 목격하게 됩니다. 여러 IDLE들이 서로 자기를 실행해달라고 싸운다던가, 3.4로는 잘 실행되던 파일이 3.5을 설치한 이후로는 에러메세지로 화면을 가득 채운다던가 하는 가슴아픈 상황이 자주 일어납니다.

pyenv는 이를 해결하기 위한 툴로서, 필요에 따라 Python 버전을 설치하고 삭제하는 것을 도와줍니다.

또한 우리는 여러 python 모듈을 이용한 개발을 위해 [virtualenv](http://ulismoon.tistory.com/2)를 사용하게 됩니다. 하지만 virtualenv 폴더를 관리하고 사용하는 것 또한 매우 귀찮은 일입니다. pyenv의 모듈 중 하나인 pyenv-virtualenv는 이러한 귀찮은 일을 쉽게 만들어줍니다.

그럼 이제부터 pyenv와 pyenv-virtualenv를 설치해보겠습니다.

> 아쉽게도 pyenv는 Windows 사용자들을 도와주지 못합니다. Windows를 사용하고 계신다면 [Anaconda](http://agiantmind.tistory.com/171)를 참고해주세요.

### 1. 설치하기

#### MacOS

pyenv를 편하게 설치하기 위해 MacOS용 패키지 관리 툴인 [brew](http://www.popit.kr/%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EB%A7%A5mac-%EC%A0%95%EB%B3%B4-%ED%8C%A8%ED%82%A4%EC%A7%80%EA%B4%80%EB%A6%AC%EC%9E%90-homebrew/)가 필요합니다. brew를 설치하지 않으신 분은 다음 명령어를 shell에 입력해주세요.

``` sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

brew를 설치하셨다면 이제 brew로 pyenv와 pyenv-virtualenv를 설치해 봅시다.

``` sh
brew install pyenv pyenv-virtualenv
```

#### Linux

Linux에서는 자동화된 스크립트로 pyenv를 설치할 수 있습니다. 다음의 명령어를 shell에 입력해주세요.

``` sh
curl -L https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
```

### 2. 필요한 도구들 설치하기

새로운 python 버전을 설치하려 하면, pyenv는 python 소스를 받아와 컴파일합니다. 이 과정에서 다른 도구들의 도움이 필요하기 때문에, pyenv를 제대로 이용하려면 여러 도구들을 설치해야 합니다. 다음의 명령어들을 통해 설치해주세요.

#### MacOS

``` sh
brew install readline xz
```

#### Linux

- Debian 계열(Debian, Ubuntu, Linux Mint 등)

``` sh
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

- RedHat 계열(RedHat Enterprise, Cent OS 등)

``` sh
sudo yum install zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel
```

다른 패키지를 이용하고 계시다면 굳이 이 글의 도움이 필요하지 않을 것 같습니다..

### 3. pyenv를 shell에서 쓸 수 있도록 하기

pyenv를 설치했다고 바로 사용할 수 있는 것이 아니라, shell이 실행될 때 마다 pyenv가 동작하도록 설정해야 합니다. 다음 명령어를 shell에 입력하시면 됩니다.

> 아래의 예시는 많은 분들이 이용하는 bash shell 기준입니다. 자신이 어떤 shell을 사용하시는지 모를 경우 높은 확률로 bash일 것입니다. 혹시 다른 shell을 이용하고 계신다면 아래의 `~/.bashrc` 부분을 사용하시는 shell에 맞게 수정해주세요. 예) zsh => `~/.zshrc`

``` sh
echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
```

### 4. pyenv 이용하기

이제 설치가 끝났으니 용도에 맞게 pyenv를 이용하시면 됩니다! 자주 이용하는 기능들은 다음과 같습니다.

- pyenv로 설치할 수 있는 Python 버전 확인하기

``` sh
pyenv install --list
```

- 새로운 Python 버전 설치하기

``` sh
pyenv install 3.6.1
```

- 새로운 virtualenv 만들기

``` sh
pyenv virtualenv 3.6.1 myvenv
```

- 만들어진 virtualenv에 들어가기

``` sh
pyenv activate myvenv
```

- virtualenv에서 빠져나오기

``` sh
source deactivate
```

- 설치한 Python 버전이나 virtualenv 삭제하기

``` sh
pyenv uninstall myvenv
pyenv uninstall 3.6.1
```

### 참고자료
- [pyenv](https://github.com/pyenv/pyenv)

{% endraw %}
