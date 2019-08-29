#### Background
For a local installation of [Kubernetes](https://kubernetes.io) on macOS the [getting started guide](https://kubernetes.io/docs/getting-started-guides/#local-machine-solutions) recommends [minikube](https://github.com/kubernetes/minikube) is used. This page details the various installation steps required to install this.

#### Prerequisites
Ensure that the software below is installed on the host macOS machine before continuing.
* [Docker for Mac](https://docs.docker.com/docker-for-mac/)
* [Homebrew](http://brew.sh/)

#### Installing minikube
As per the [minikube documentation](https://github.com/kubernetes/minikube)...

*"Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day."*

To install `minikube`, follow the instructions at [https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases). At the time of writing, version `v0.15.0` is the latest release; the appropriate installation commands for this version are as follows:

```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.15.0/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

#### Installing the xhyve Driver
By default, `minikube` uses [VirtualBox](https://www.virtualbox.org/) to host the Kubernetes environment. However, as Docker for Mac uses [xhyve](https://github.com/mist64/xhyve), it makes more sense to use the same hypervisor to host our Kubernetes environment.

For this we need to install the appropriate xhyve driver as detailed at [https://github.com/kubernetes/minikube/blob/master/DRIVERS.md#xhyve-driver](https://github.com/kubernetes/minikube/blob/master/DRIVERS.md#xhyve-driver)

At the time of writing, the appropriate installation commands for this are as follows:

```bash
$ brew install docker-machine-driver-xhyve
$ sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
$ sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
```

#### Installing kubectl
In order to control Kubernetes from the command-line, the `kubectl` tool is used. Refer to [https://kubernetes.io/docs/user-guide/kubectl](https://kubernetes.io/docs/user-guide/kubectl/) for more information.

To install `kubectl`, follow the instructions at [https://kubernetes.io/docs/user-guide/prereqs](https://kubernetes.io/docs/user-guide/prereqs/). At the time of writing the latest stable version is `v1.5.2`; the appropriate installation commands for this version are as follows:

```bash
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin/kubectl
```

#### Starting, Verifying, Inspecting and Stopping minikube
Starting `minikube` is trivial, as shown below. The first time this command is executed will take longer than for subsequent calls as it will pull the ISO, storing in `~/.minikube/cache/iso/`.
```
$ minikube start --vm-driver=xhyve
Starting local Kubernetes cluster...
Downloading Minikube ISO
 68.95 MB / 68.95 MB [==============================================] 100.00% 0s
Kubectl is now configured to use the cluster.
```

Verify the status of `minikube`:
```
$ minikube status
minikubeVM: Running
localkube: Running
```

Inspect the environment by bringing up the dashboard, which will open up in the browser.
```
$ minikube dashboard
Opening kubernetes dashboard in default browser...
```

Stop the `minikube` environment:
```
$ minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
```