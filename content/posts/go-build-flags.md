---
title: "Injecting build values into your go applications with ldflags"
date: 2020-03-03T00:49:36-08:00
draft: false
---

Adding build information to your application will make your life easier when you push your application into production. When an issue arises, you can use this information to narrow down the problem (ie. down to a git commit) and it can help users to participate in the bug reporting process.

For example for docker, when you run ```docker version```, you'll get a detailed version report.

```
$ sudo docker version
Client: Docker Engine - Community
 Version:           19.03.6
 API version:       1.40
 Go version:        go1.12.16
 Git commit:        369ce74a3c
 Built:             Thu Feb 13 01:27:52 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.6
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.16
  Git commit:       369ce74a3c
  Built:            Thu Feb 13 01:26:25 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.13
  GitCommit:        7ad184331fa3e55e52b890ea95e65ba581ae3429
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

## How do you inject build information into your Go application?

Adding build information into your Go application requires you to build your application with ```-ldflags``` and appending it with ```-X importpath.name=value```. Values can only be strings.


I've written up an [example application](https://github.com/al31n/go-build-flag-example.git). Be sure to clone this into your GOPATH.

```
$ mkdir -p $GOPATH/src/github.com/al31n/go-build-flag-example
$ cd $GOPATH/src/github.com/al31n/go-build-flag-example
$ git clone https://github.com/al31n/go-build-flag-example
$ cd go-build-flag-example
```

Without ldflags, the variables would just be zero values

```
$ go build -o bin/BuildFlagTest .
$ ./bin/BuildFlagTest
Version: 
Git Commit: 
Build Date: 
OS/Arch: linux/amd64
Go Version: go1.12.9
Experimental: false
```

And now with ldflags, 
```
$ go build -o bin/BuildFlagTest -ldflags "-X github.com/al31n/go-build-flag-example/pkg/version.Version=v0.99" .
$ ./bin/BuildFlagTest
Version: v0.99
Git Commit: 
Build Date: 
OS/Arch: linux/amd64
Go Version: go1.12.9
Experimental: false
```

To inject the other fields, you can check out the Makefile and run ```make```. 

```
$ make && bin/BuildFlagTest 
mkdir -p bin
go build -o bin/BuildFlagTest -v -ldflags "-s -X github.com/al31n/go-build-flag-example/pkg/version.Version=v0.99 -X github.com/al31n/go-build-flag-example/pkg/version.GitCommit=6d07814 -X 'github.com/al31n/go-build-flag-example/pkg/version.BuildDate=Wed 04 Mar 2020 12:16:44 AM PST' -X 'github.com/al31n/go-build-flag-example/pkg/version.Experimental=true'" .
Version: v0.99
Git Commit: 6d07814
Build Date: Wed 04 Mar 2020 12:16:44 AM PST
OS/Arch: linux/amd64
Go Version: go1.12.9
Experimental: true
```

And that's it!

## References and more examples 
- Go Link Tool Doc: [https://golang.org/cmd/link/](https://golang.org/cmd/link/)
- Go Runtime package: [https://golang.org/pkg/runtime/](https://golang.org/pkg/runtime/)
- Example build version from Prometheus: [github.com/prometheus/common/version](https://github.com/prometheus/common/blob/master/version/info.go)