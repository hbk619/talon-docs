# Actions

Actions are things Talon will do for you. You can define and implement them in Python or just implement them
in a talon file.

## Defining in Python

To simply make an action available you can specify empty functions with a doc string:

```python
from talon import Module

mod = Module()

@mod.action_class
class Actions:
    def ide_multi_select_all():
        """Select all occurrences of word"""

```
    
## Implementing in Python

The module action_class annotation is used to specify a class is an Action class.

```python
from talon import Module

mod = Module()

@mod.action_class
class Actions:
    def idea(commands: str):
        """Send a command to Jetbrains product"""
        idea_commands(commands)

```

By implementing in Python you have the full power of Python to do things for you, read/write files, send http requests 
etc

## Implementing in Talon

To implement a global action, or a user defined action, use the `action` keyword of Talon files.

```yaml
action(user.ide_multi_select_all):
    key(ctrl-alt-shift-j)
```

This means you can use context filters to change what happens in different contexts.

See [talon files full example](talonfiles.md) for more info.


## Global Actions

Some actions are already defined by Talon. To find the latest list use the repl and run

```python
talon.actions.list()
```

You can define them in talon files [as above](#implementing-in-talon)