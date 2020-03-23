# Developing Talon commands

There are a few things that can help you when writing Talon files.

## IDE auto complete

If you use the talon virtual env in your IDE you will get auto complete of most Talon modules.

### Linux

 `~/.talon/bin/python`

### MacOS

`~/.talon/bin/python` or `/Applications/Talon.app/Contents/Resources/venv_bin/posix/python`

### Jetbrains

In Jetbrains 

File -> Settings -> Project -> Project Interpreter -> Add env (cog button) -> Existing Environment

## Talon file syntax highlighting

A few community made Syntax highlighters are available

### VS Code

https://marketplace.visualstudio.com/items?itemName=mrob95.vscode-talonscript

### Sublime

Courtesy of [2shea](https://github.com/2shea)

```yaml
%YAML 1.2
---
# http://www.sublimetext.com/docs/3/syntax.html
name: Talon
file_extensions: [talon]
scope: source.txt
variables:
  illegal_names: (?:and|as|assert|break|class|continue|def|del|elif|else|except|finally|for|from|global|if|import|in|is|lambda|not|or|pass|raise|return|try|while|with|yield)
  identifier: '\b[[:alpha:]_]{{identifier_continue}}*\b'
  identifier_continue: '[[:alnum:]_]'
  string: |-
    (?x:
      [urfb]*"(?:\\.|[^"])*" # strings
    )*
contexts:
  main:
    - include: comments
  # voice command
    - match: ^(\S[^:#]*)(?=:)
      push: voice_command
    # end brackets with no beginning
    - match: \(
      push: brackets
    - match: \)
      scope: invalid.illegal.stray-bracket-end
    - match: \{
      push: brackets
    - match: \}
      scope: invalid.illegal.stray-bracket-end
    - match: \[
      push: brackets
    - match: \]
      scope: invalid.illegal.stray-bracket-end
    # single quoted string
    - match: "('(?:\\.|[^'])*')"
      captures:
        1: string.quoted.double
    # double quoted string
    - match: '("(?:\\.|[^"])*")'
      captures:
        1: string.quoted.double
    # function calls
    - match: (\b[\w-._]+\b)(?=\()
      scope: keyword
  voice_command:
    - meta_scope: constant.numeric
    - match: '\:'
      pop: true
    - include: main
  comments:
    - match: "#"
      scope: punctuation.definition.comment
      push:
        - meta_scope: comment.line.number-sign
        - match: \n
          pop: true
  brackets:
    - match: \)
      pop: true
    - match: \}
      pop: true
    - match: \]
      pop: true
    - include: main
```

## REPL

A [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) is provided to allow you to explore
the talon api via the command line. It has very good support for auto complete.

### Running the REPL

From a terminal run:

```shell script
~/.talon/bin/repl
```

This is a Python REPL so you can run any Python code.

To try out the auto complete type `talon.` and hit tab a few times.