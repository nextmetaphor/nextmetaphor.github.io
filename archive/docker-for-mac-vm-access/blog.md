#### Background
Although [Docker for Mac](https://docs.docker.com/docker-for-mac/) helpfully goes out of its way to appear to be running natively on macOS, behind the scenes it actually runs on an Alpine Linux VM using [xhyve](https://github.com/mist64/xhyve/). Refer to [https://docs.docker.com/engine/installation/mac/](https://docs.docker.com/engine/installation/mac/) for more details.

Under most circumstances we won't need to understand the underlying implementation, but there are occasions where it is helpful to be able to see exactly what Docker is doing. In order to do this, we need access to the actual VM itself. This isn't a problem when using [Docker Machine](https://docs.docker.com/machine/) as we can easily connect to the VirtualBox VM; it is not quite so obvious how to achieve this on the new architecture. 

#### Accessing the Docker Device

The *callin device* for the Docker VM can be found at `~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty`. 


    $ ls -l ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
    lrwxr-xr-x  1 paul  staff  12 18 Oct 16:54 /Users/paul/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty -> /dev/ttys001

As can be seen above, this file is actually a link to the underlying *character device* file:

    $ ls -l /dev/ttys001
    crw--w----  1 paul  tty   16,   1 18 Oct 16:54 /dev/ttys001

To access this device interactively, we can simply use the `screen` utility as follows:

    $ screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
    
If the resulting screen is blank, simply hit the Enter key; you should then be presented with the following:

    Welcome to Moby
    Kernel 4.4.20-moby on an x86_64 (/dev/ttyS0)
    
                            ##         .
                      ## ## ##        ==
                   ## ## ## ## ##    ===
               /"""""""""""""""""___/ ===
          ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
               \______ o           __/
                 \    \         __/
                  \____\_______/
    
    moby login: root
    Welcome to Moby, based on Alpine Linux.
    moby:~#

As shown, the login is `root`; no password is required. And that's all there is to it: you now have a session on the Docker VM. 

For example, to check the Docker images that have been loaded on your Docker for Mac installation:

    moby:~# docker images
    REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
    ubuntu                               16.04               c73a085dc378        3 weeks ago         127.1 MB
    tykio/tyk-gateway                    latest              34c572d12a1f        9 weeks ago         246.9 MB
    redis                                latest              e5181bd07b8e        10 weeks ago        185 MB
    swaggerapi/swagger-editor            latest              3727b21e15fc        3 months ago        105.6 MB
    tykio/tyk-dashboard                  latest              6249f7af1db2        3 months ago        567.1 MB
    tykio/tyk-pump-docker-pub            latest              b0df8680eaab        3 months ago        246.7 MB
    alpine                               3.3                 47cf20d8c26c        3 months ago        4.797 MB
    jenkins                              latest              d5c0410b1b44        4 months ago        736.9 MB
    registry                             latest              bca04f698ba8        8 months ago        422.9 MB
    moby:~#

#### That's Great ... But How do I Get Out of Here?
The key combination you're looking for is **`ctrl`** **`a`** **`\`** then answer `y` to the prompt:

    Really quit and kill all your windows [y/n] y