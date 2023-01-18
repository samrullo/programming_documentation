# Install

To install pyenv first you will need following libraries on ubuntu

```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl
```

Then run

```
$ curl https://pyenv.run | bash
```

This will install

- pyenv: The actual pyenv application
- pyenv-virtualenv: Plugin for pyenv and virtual environments
- pyenv-update: Plugin for updating pyenv
- pyenv-doctor: Plugin to verify that pyenv and build dependencies are installed
- pyenv-which-ext: Plugin to automatically lookup system commands

# Install python versions with pyenv

There are many versions of python to choose from
To see for instance available CPython versions

```
$ pyenv install --list | grep " 3\.[678]"
```

Then let's say we want to install python version 3.10.4

```
pyenv install -v 3.10.4
```

All your python versions installed by `pyenv` is located in `~/.pyenv/versions`

To uninstall a certain version

```
pyenv uninstall 3.10.4
```

To see installed python versions by `pyenv`

```
pyenv versions
```

To make certain python version global

```
pyenv global 3.10.4
```
