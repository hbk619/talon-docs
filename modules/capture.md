# Captures

Captures are useful for when you want to run similar code for multiple commands or the command 
always maps to a function and you don't want to implement actions for every single one.

You take advantage of the list feature of contexts to implement them.

## Enabling Captures

To make a capture available you must declare it in a module:

```python
from talon import Module

mod = Module()

# Add the list to the module
mod.list('my_list_of_commands', desc="Some long list of commands")

# Define the list
@mod.capture
def my_list_of_commands(m): list
    """Doc string for the capture list"""
```

## Defining rules for captures

Once you've enabled a capture in a module, you can create a rule and set the possible values
of the capture.

```python
from talon import Context

ctx = Context()

# Add actual values user can say
ctx.lists['self.my_list_of_commands'] =["kebab", "camel", "snake"]

# Create rule to be used in talon file as "<user.my_list_of_commands>"
@ctx.capture(rule='{self.my_list_of_commands}')
def my_list_of_commands(m):
    return m.my_list_of_commands
```

The rule is a normal talon rule, so if you wanted one of your list of commands followed by a specifc word you could

```python
@ctx.capture(rule='{self.my_list_of_commands} bob')
def my_list_of_commands(m):
    return m.my_list_of_commands
```

Or make words optional:

```python
@ctx.capture(rule='{self.my_list_of_commands} [over]')
def my_list_of_commands(m):
    return m.my_list_of_commands
```

## Example

If you want to format text as underscore separated (snake case) or each word's first letter upper 
cased (camel case) you could make an action for each of those. But if you then add other text
formatting (kebab, all caps, all lower, dot separated etc) it gets painful.

Instead if you define your functions in a simple Python map, the create a capture on the module 
and context you can remove the need for actions. For a complete example see
 https://github.com/knausj85/knausj_talon/blob/master/code/formatters.py

code/formatters.py
```python
from talon import Module, Context, actions

ctx = Context()

def format_text_helper(m, fmtrs):
    words = m._words
    tmp = []
    spaces = True
    for i, w in enumerate(words):
        for name in reversed(fmtrs):
            smash, func = formatters_dict[name]
            w = func(i, w, i == len(words) - 1)
            spaces = spaces and not smash
        tmp.append(w)
    words = tmp

    sep = " "
    if not spaces:
        sep = ""
    return sep.join(words)



formatters_dict = {
    "dunder": (True, lambda i, word, _: "__%s__" % word if i == 0 else word),
    "camel": (True, lambda i, word, _: word if i == 0 else word.capitalize()),
    "hammer" : (True, lambda i, word, _: word.capitalize()),
    "snake": (True, lambda i, word, _: word.lower() if i == 0 else "_" + word.lower()),
    "smash": (True, lambda i, word, _: word),
    "kebab": (True, lambda i, word, _: word if i == 0 else "-" + word),
    "packed": (True, lambda i, word, _: word if i == 0 else "::" + word),
    "allcaps": (False, lambda i, word, _: word.upper()),
    "alldown": (False, lambda i, word, _: word.lower()),
    "dotted": (True, lambda i, word, _: word if i == 0 else "." + word)
}

mod = Module()
mod.list('formatters', desc='list of formatters')

@mod.capture
def formatters(m) -> list:
    "Returns a list of formatters"

@mod.capture
def format_text(m) -> str:
    "Formats the text and returns a string"
        
@ctx.capture(rule='{self.formatters}+')
def formatters(m):
    return m.formatters
 
@ctx.capture(rule='<self.formatters> <phrase>')
def format_text(m):
    return FormatText(m.phrase, m.formatters)

ctx.lists['self.formatters'] = formatters_dict.keys()
```

formatters.talon
```yaml
<user.format_text> [over]: insert(format_text)
```

With the above, if you say "kebab some text please" you will get `some-text-please` inserted. 
This happens by the format_text context capture being invoked with an object that looks like:

```python
m = {
    "formatters": ["kebab"],
    "phrase": "some text please"
}
```

