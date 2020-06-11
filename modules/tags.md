# Tags

Tags can be used to avoid duplicated context across multiple files. A tag must be defined somewhere in the definition
section of a talon file. It can be used in another file in the context section.

## Define a tag

```
app: terminal.exe
-
tag(): terminal
```

This means the terminal tag is active whenever the app is terminal.exe

## Use a tag

```
tag: terminal
-
change up: insert("cd ../")
```
...

This means these commands are active whenever the terminal tag is.