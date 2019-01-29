Python
======

virtualenv
----------

[docs](https://virtualenv.pypa.io/en/stable/userguide/#usage)

```
pip install virtualenv
# setup
virtualenv <name>
# configure
source <name>/bin/activate
# destroy
deactivate
```

virtualenvwrapper
-----------------

[docs](http://virtualenvwrapper.readthedocs.io/en/latest/)

```
pip install virtualenvwrapper
# Add to ~/.bash_profile
# [ -f /usr/local/bin/virtualenvwrapper.sh ] && . /usr/local/bin/virtualenvwrapper.sh

# Create a virtual env
mkvirtualenv -p `which python3` <project name>
workon <project name>
```

Pyenv
-----

[Freecodecamp blog](https://medium.freecodecamp.org/manage-multiple-python-versions-and-virtual-environments-venv-pyenv-pyvenv-a29fb00c296f)

Multiple versions of Python manager

```
brew update
brew install pyenv pyenv-virtualenv
```

Add to `.bash_profile`

```
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

**NOTE**: uses the `PYENV_VERSION` environment var and the local projects `.python-version` file for auto switching

### Usage

[Official docs](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md)

```
pyenv install <version>
pyenv virtualenv <version> <name-to-give-it>
pyenv activate <name>
pyenv deactivate
```
