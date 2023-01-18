# virtualenvwrapper

`virtualenvwrapper` allows to manage python virtual environments easily

First install it

```
pip install virtualenvwrapper
```

For windows

```
pip install virtualenvwrapper-win
```

Once it’s installed, we’ll need to activate its shell functions. We can do this by running source on the installed virtualenvwrapper.sh script. When you first install it with pip, the output of the installation will tell you the exact location of virtualenvwrapper.sh. Or you can simply run the following:

```
$ which virtualenvwrapper.sh
```

Using that path, add the following three lines to your shell’s startup file. If you’re using the Bash shell, you would place these lines in either the ~/.bashrc file or the ~/.profile file. For other shells, like zsh, csh, or fish, you would need to use the startup files specific to that shell. All that matters is that these commands are executed when you log in or open a new shell:

```
export WORKON_HOME=$HOME/.virtualenvs   # Optional
export PROJECT_HOME=$HOME/projects      # Optional
source /usr/local/bin/virtualenvwrapper.sh
```

You’ll also now have the shell commands available to you to help you manage the environments. Here are just a few of the ones available:

```
workon
deactivate
mkvirtualenv
cdvirtualenv
rmvirtualenv
```

If you have many environments to choose from, you can list them all with the workon function:

```
workon
```

Finally, here’s how to activate:

```
workon <virtualenv>
```

If you would like to be able to use a single tool and switch between Python versions, virtualenv will allow you to do just that. virtualenv has a parameter -p that allows you to select which version of Python to use. Combine that with the which command, and we can easily select your preferred version of Python to use in a simple manner. For example, let’s say that we want Python 3 as our preferred version:

```
$ virtualenv -p $(which python3) blog_virtualenv
```
