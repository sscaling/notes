Go resources
============

-	vim-go - https://github.com/fatih/vim-go
-	visually represent memory [https://github.com/bradleyjkemp/memviz](https://github.com/bradleyjkemp/memviz)
-	monitor go routines [https://github.com/bcicen/grmon](https://github.com/bcicen/grmon)

Debugging
---------

### Go-debug

-	[https://github.com/mailgun/godebug](https://github.com/mailgun/godebug)

**Instrument files:**

```
_ = "breakpoint"
```

**Initiate debug session**

```
godebug run <files>

type 'h' for help
```

### Delve

-	[https://github.com/derekparker/delve](https://github.com/derekparker/delve)

Once installed, intelliJ will work with this nicely or can use directly from CLI. There is good documentation on the website on how to debug more complex packages.

**NOTE** If using from intelliJ, must run as 'Go Application' type. Cannot debug 'Single Go file' etc.

**Install:**

```
 brew install go-delve/delve/delve
```

**Start:**

```
dlv debug <file>
```

**Set breakpoint:**

```
b file.go:<line number>
c // continue
h // help
q // quit
```
