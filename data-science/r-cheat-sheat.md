R Language
==========

A cheat sheet for common functions

**Getting help**

`?xxx` or `help.search("xxx")` where 'xxx' is a function. This will bring up the help.

Find arguments with the `args("xxx")` function.

-	[Cheat Sheet](https://www.rstudio.com/wp-content/uploads/2016/10/r-cheat-sheet-3.pdf)
-	[Reference Card](https://cran.r-project.org/doc/contrib/Short-refcard.pdf)

**Installing**

[Download package](https://cran.r-project.org/). Can install RStudio from [here](https://www.rstudio.com/). Rtools used in Windows for building packages. Not required for Linux / Mac.

**Packages**

Packages can be downloaded via `CRAN` (Comprehensive R Archive Network).

at the REPL, enter `available.packages()` to view packages, install with `install.packages("<package name>")` including dependencies.

3rd party via bioconductor, see [http://www.bioconductor.org/install](http://www.bioconductor.org/install)

To load a library from a package and inspect functions, use

```
library("name")
search()
```

Help
----

```
help()
help.search("<term>")
help( ("getwd"), ("base") ) // specific topic / package help
?function // help on function
str(file) // print the stringified version of a function object (i.e. see it's prototype)
```

Working Directory
-----------------

```
getwd() / setwd()
dir() // objects in current working directory
ls() // objects in workspace
source("<R file>") // where <R file> is name of code (i.e. program.R)
```

Syntax
------

```
#  // comment
<- // assignment, x <- 1, creates a vector of '1' element.
n:m  // create a sequence, x <- 1:20
```

Keywords
--------

```
print // print object. Also just type object name to print out it's value
```

Working with files
------------------

Can use `read.csv` to read a csv, or create a connection, i.e.

```
con <- file("<file>", "r")
x <- readLines(con, 100) // only read the first 100 lines, rather than whole file
close(con)
```

`dumping` and `dputting` writes data, but with meta-data.
