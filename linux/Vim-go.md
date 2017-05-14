Vim-go cheatsheet
=================

See [https://github.com/fatih/vim-go](https://github.com/fatih/vim-go)

## Help

```
:help vim-go
```

Functions
---------

```
GoImports       # Fix up all imports
GoAlternate     # Switch between file and file_test
GoDef           # go to definition
GoDefStack      # View definition stack, help with traversal
GoDoc           # Go documentation for current object
GoInfo      # Print method params to status line
GoSameIds       # Highlight all similar Ids
GoFiles         # List files in package (excluding tests)
GoDeps          # Show package dependencies
GoReferrers     # Show references
GoDescribe      # Show method set and struct fields of a type
GoImplements    # What interfaces a type implements
GoWhicherrs     # Which constants, global variables and concrete types that may appear
GoChannelPeers  # Possible send/receives on the channel
GoCallees       # Targets of function call
GoCallers       # Callers of function
GoCallstack     # Show callstack of function calls
GoRename        # Rename identifier
GoFreevars      # In visual mode, display dependent variables. Useful fo refactoring
GoGenerate      # Invoke go generate
GoImpl          # Generate method stubs that implement an interface

A               # Shortcut for GoAlternate
AV              # Open alternative file in vertical split
AS              # Open alternative file in horizontal split
AT              # Open alterantive file in new tab

GoDecls+        # List type / func
GoDeclsDir+     # Also list everything in this directory (pacakge)
```

-	Requires ctrlpvim/ctrlp.vim plugin

### Ctrlp shortcuts

```
:help ctrlp-commands
CTRL-c          # quit ctrl-p window
CTRL-j / k      # navigate list
```

Shortcuts (normal mode)
-----------------------

```
[[ / ]]         # Previous / Next function
n[[ / n]]       # move to the nth previous / next function
vif             # Visually select inner function
vof             # Visually select outer function
vaf             # Visually select A function, including doc comment
gd / CTRL-]     # Go to definition
CTRL-t          # jump to previous location
K               # Go to documentation - GoDoc shortcut
```
