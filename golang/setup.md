# Go dev setup

Main thing is GOPATH environment var.

## MacOSX

	brew install golang

Follow the instructions, and update path

	export PATH=$PATH:/usr/local/opt/go/libexec/bin

Create a workspace

	mkdir ~/Documents/go

Update GOPATH

	export GOPATH="$HOME/Documents/go"

Importing projects from github.com

	git clone git@github.com:user/project.git $GOPATH/src/github.com/project


## Go Tool

### build

To test if something compiles

	go build [package]

### install

This builds and installs

	# from anywhere
	go install <package>
	# from inside the package
	go install
	# e.g.
	go install /github.com/user/project

Installs to main programs to

	$GOPATH/bin

Installs packages to

	$GOPATH/pkg

*NOTE* dependencies are automatically installed into pkg when using install


### Dependencies

	go get github.com/golang/example/hello


## Other Tools

	godoc <pkg>
	godoc <pkg> <function>
	gofmt -s -w <file|glob>  # write simplified code back to file
