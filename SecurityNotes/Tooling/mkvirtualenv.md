# Installing mkvirtualenv

So the annoying parts about this is that there's no basic install script for virtualenvwrapper.

Here's a 411 on what you need:
```
$ pip install virtualenvwrapper 
 ^ What they don't tell you is that this command embeds a virtualenvwrapper.sh file somewhere.
$ export WORKON_HOME=~/.virtualenvs
$ mkdir -p $WORKON_HOME]
$ find / -name "virtualenvwrapper.sh"

$ source the output of the ^ command
$ mkvirtualenv env1
```

Also, make sure that you haven't installed virtualenv in anything more than 1 out of: `pip, pip3, apt, apt-get`, otherwise it ALL breaks with really stupid errors.

THEN you have to place these environment variables in your `.zshrc` or `.bashrc`

```
export WORKON_HOME=$HOME/.virtualenvs                                                                                   export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3.9                                                                      export VIRTUALENVWRAPPER_VIRTUALENV=/usr/bin/virtualenv                                                                   source /home/exiled/.local/bin/virtualenvwrapper.sh
```

THEN you can test it out with

```
mkvirtualenv test
lsvirtualenv
deactivate
rmvirtualenv test
```

And **Hopefully** that works :/