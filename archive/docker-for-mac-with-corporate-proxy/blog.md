#### Background
Although enabling internet access for [Docker for Mac](https://docs.docker.com/docker-for-mac/) when behind a corporate proxy appears to be difficult, the steps required are surprisingly simple.
    
#### Prerequisite
A local proxy server should be running on the Mac where Docker is running in order to authenticate with the dreaded corporate proxy. Both [Charles Proxy](https://www.charlesproxy.com/) and [SquidMan](http://squidman.net/squidman/) work well for this purpose. It is assumed below that the local proxy server is listening on port `8099`; feel free to change this value throughout, if required.

#### Getting the IP Address of the Docker VM Gateway
Connect to the VM, as detailed in [Accessing the Docker For Mac Virtual Machine](https://nextmetaphor.io/2016/10/18/accessing-the-docker-for-mac-virtual-machine/).

```bash
$ screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
```

Press the Enter key, and validate we are on the Docker VM.
```bash
/ # hostname
moby
```

Examine the routing table to see the default gateway: in the example below, this is `192.168.65.1`
```bash
/ # netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.65.1    0.0.0.0         UG        0 0          0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0
172.18.0.0      0.0.0.0         255.255.0.0     U         0 0          0 br-b46215017f01
192.168.65.0    0.0.0.0         255.255.255.248 U         0 0          0 eth0
```

Disconnect from the VM using **`ctrl`** **`a`** **`\`**, answering `y` to the prompt:

```bash
Really quit and kill all your windows [y/n] y
```

#### Set the Docker for Mac Proxy
Finally, set the proxy in Docker itself, as shown below:
![Docker for Mac Proxy Settings](https://nextmetaphor.files.wordpress.com/2017/01/docker-for-mac-proxy-settings.png)

The IP address is the gateway IP address from the Docker VM (i.e. `192.168.65.1`); the port is what the local proxy is listening on (i.e. `8099`). Apply the changes and restart Docker, at which point it will now be able to communicate, via the local proxy server, to the internet. Easy!