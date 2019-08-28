#### Installing golang using Homebrew

First install `golang` using [Homebrew](http://brew.sh/) as follows:

```bash
$ brew install golang
```

This will install to the following location:

```bash
$ which go
/usr/local/bin/go
```

#### Setting GOROOT

The `golang` binary distributions expect to be installed to the standard location of `/usr/local/go`. This will not be the case, as Homebrew installs into `/usr/local/bin/go`, so we need to manually set the `GOROOT` environment variable to point the correct location as follows:

```bash
export GOROOT=/usr/local/opt/go/libexec
```

See [https://golang.org/doc/install#tarball_non_standard] for more information on `GOROOT`.

#### Setting GOPATH

First set a `DEVELOPMENT_HOME` environment variable. This will point to the top-level directory under which all code artefacts will exist, regardless of language.

```bash
export DEVELOPMENT_HOME=$HOME/Development
```

Next, set the `GOPATH` environment variable. This will point to the root directory where all `golang`-related artefacts will exist. Make this a direct subdirectory of the `DEVELOPMENT_HOME` directory.

```bash
export GOPATH=$DEVELOPMENT_HOME/golang
```

See [https://golang.org/cmd/go/#hdr-GOPATH_environment_variable] for information on `GOPATH`.

#### Appending GOPATH to PATH

Finally, add `GOPATH` to `PATH` as follows:

```bash
export PATH=$PATH:$GOPATH/bin
```

#### Test the Installation

```bash
$ go get github.com/golang/example/hello

$ ls $GOPATH/src/github.com/golang/example/hello
hello.go

$ ls $GOPATH/bin/hello
-rwxr-xr-x  1 paul  staff   1603584  9 Dec 18:03 hello

$ $GOPATH/bin/hello
Hello, Go examples!
```

See [https://golang.org/doc/code.html#remote] for more information.