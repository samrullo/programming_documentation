# colorize my ubuntu prompt

You can colorize your Ubuntu prompt by modifying the PS1 environment variable in your shell's configuration file. The PS1 variable controls the format of your command prompt, and you can use special codes to change its color.

The default location of the shell's configuration file is in the user's home directory, in the .bashrc file for the Bash shell and .zshrc for zsh shell.

You can use the "[" and "]" to enclose any non-printing characters that you want to include in the PS1 variable, such as color codes.

For example, to make the prompt red, you can add the following line to your .bashrc file or .zshrc


```PS1='\[\033[01;31m\]\u@\h:\w\$\[\033[00m\]'```
This will display the username(\u), hostname (\h) and current working directory(\w) in red color.

You can also change the color of specific parts of the prompt, for example, the following line changes the color of the username to green, hostname to yellow and current working directory to blue.


```PS1='\[\033[32m\]\u\[\033[00m\]@\[\033[33m\]\h\[\033[00m\]:\[\033[34m\]\w\[\033[00m\]\$'```
You can use the following codes to change the color of your prompt,

Black: [\033[30m]
Red: [\033[31m]
Green: [\033[32m]
Yellow: [\033[33m]
Blue: [\033[34m]
Magenta: [\033[35m]
Cyan: [\033[36m]
White: [\033[37m]
Don't forget to reset the color back to default by \[\033[00m\] after you set the color you want.

Make sure to save the changes and either logout/login or use the command source ~/.bashrc or source ~/.zshrc to apply the changes.

## to show only part of current working directory and colorize it

Below example shows last two parts of current working directory

```
PS1='\[\033[32m\]\u\[\033[00m\]@\[\033[33m\]\h\[\033[00m\]:\[\033[34m\]$(pwd | sed "s:.*/\(.*/[^/]*\).*:\1:")\[\033[00m\]\$'
```


```\(.*/[^/]*\)``` means

This is a regular expression that matches a specific pattern in a string. The expression matches:

- ".*/" which matches any characters (including zero or more characters) followed by a "/"
- "[^/]*" which matches any number of non-slash characters.

The whole expression is captured as a group by paranthese (opening and closing paranthesis escaped)
so that it can be referenced later in the substitution operation.
In this regular expression, the "." character matches any character (except a newline character), the "*" character means "zero or more occurrences", the "[^/]" means "any character except a slash", and the opening and closing parantheses characters indicate a capturing group.



