# docker 使用笔记



> [引用链接](<https://zhuanlan.zhihu.com/p/26418829>)



下载 docker

```bash
$ curl -sSL https://get.docker.com/ | sh
```

安装成功后，使用下面的命令应该能显示Docker的版本信息，说明Docker已经被安装了

```bash
$ docker -v
Docker version 17.04.0-ce, build 4845c56
```

接着我们使用Docker创建一个nginx的容器:

```bash
$ docker run -d --name=web -p 80:80 nginx:latest
```

这条命令表示Docker基于nginx:alpine这个Docker镜像，创建一个名称为web的容器，并把容器内部的80端口与宿主机上的80端口做映射，使得通过宿主机80端口的流量转发到容器内部的80端口上。

使用docker ps命令，可以列出正在运行的容器，可以看到，刚才基于nginx镜像创建的容器已经处于运行状态了：

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
a89d281829f9        nginx:latest        "nginx -g 'daemon ..."   8 minutes ago       Up 8 minutes        0.0.0.0:80->80/tcp, 443/tcp   web
```



> 如果有权限的提示： 
>
> ```
> docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.26/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
> See 'docker run --help'.
> ```
>
> 解决方案：`sudo usermod -a -G docker $USER`



此时访问宿主的80端口，观察。



使用docker images能看到目前的镜像：

```bash
$ docker images
REPOSITORY
nginx                 latest              bedece1f06cc        10 minutes ago         54.3MB
```



了解到这个事实之后，我们使用下面的命令进入刚才创建的容器内部

```bash
$ docker exec -i -t web bash
```

现在处于的是容器内部的根文件系统(rootfs)，它跟宿主机以及其他容器的环境是隔离开的，看起来这个容器就是一个独立的操作系统环境一样。使用ps命令可以看到容器内正在运行的进程：

```bash
$ ps -l
PID   USER     TIME   COMMAND
    1 root       0:00 nginx: master process nginx -g daemon off;
    5 nginx      0:00 nginx: worker process
   23 root       0:00 ps -l
```

使用exit命令可以从容器中退出，回到宿主机的环境：

```bash
$ exit
```



### Docker官方英文资源

docker官网：[http://www.docker.com](http://www.docker.com/)

Docker Windows 入门：<https://docs.docker.com/docker-for-windows/>

Docker CE(社区版) Ubuntu：<https://docs.docker.com/install/linux/docker-ce/ubuntu/>

Docker mac 入门：<https://docs.docker.com/docker-for-mac/>

Docker 用户指引：<https://docs.docker.com/config/daemon/>

Docker 官方博客：<http://blog.docker.com/>

Docker Hub: <https://hub.docker.com/>

Docker开源： <https://www.docker.com/open-source>

### Docker中文资源

Docker中文网站：<https://www.docker-cn.com/>

Docker安装手册：<https://docs.docker-cn.com/engine/installation/>



docker参数



```bash
Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and
                                       1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight)
                                       (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler)
                                       period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print
                                       container ID
      --detach-keys string             Override the key sequence for detaching a
                                       container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a
                                       device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a
                                       device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a
                                       device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a
                                       device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h)
                                       (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to
                                       initialize before starting health-retries
                                       countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run
                                       (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that
                                       forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1'
                                       to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to
                                       100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network string                 Connect a container to a network (default
                                       "default")
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for
                                       unlimited)
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --read-only                      Mount the container's root filesystem as
                                       read only
      --restart string                 Restart policy to apply when a container
                                       exits (default "no")
      --rm                             Automatically remove the container when it
                                       exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process
                                       (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format:
                                       <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```

