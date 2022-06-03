# Angr
First off. There's a prerequisite of either having `venv` or `mkvirtualenv` installed.

Also you need these packages

```
sudo apt-get install python3-dev libffi-dev build-essential virtualenvwrapper
```

**NOTE:** Look at [[mkvirtualenv]] to see how it's installed. Otherwise you're gonna have an annoying ass time with conflicting virtual envs.

THEN you can type

```sh
mkvirtualenv --python=$(which python3) angr && pip install angr
```
