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
