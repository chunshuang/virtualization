# Which to use? - taskset, numactl or cgroup

To obtain CPU/memory affinity in Linux, we can use several userland tools, including:

1 taskset

2 numactl

3 cgroup

Are they equal? Short answer is: yes. We should select according to following rule:

For functionalities: cgroup = numactl > taskset

For easy-to-use: taskset > cgroup = numactl

For source code complexity: taskset > numactl > cgroup

# Pros and Cons

Name |Pros| Cons
|---|---|---
|tasket| quite easy-to-use | Cannot set memory affinity|
|numactl| easy-to-use | Cannot change affinity of exsiting process|
|cgroup|easy-to-use and full of control of CPU and memory affinity |lack of flexibility|


# Under the hook

All three interfaces employ Linux kernel system calls to implement CPU and memory affinity.

taskset --> sched_setaffinity

numactl --> sched_setaffinity (for CPU)
        --> mbind and set_mempolicy (for memory)

cgroup --> sched_setaffinity (for CPU)
       --> mbind and set_mempolicy (for memory)

The CPU affinity system calls were introduced in Linux kernel 2.5.8.
The mbind and set_mempolicy system calls was added to the Linux kernel in version 2.6.7.

# Useful commands

## taskset

设置亲和性 taskset -cp mask pid

```
$> taskset -cp 0-3 1234
```

获取亲和性 taskset -cp pid

```
$> taskset -cp 1234
```

## numactl

Get NUMA info:

```
grep -i numa /var/log/dmesg
```

```
[root@ceph-osd1 ~]# numactl --hardware
available: 2 nodes (0-1)
node 0 cpus: 0 1 2 3 4 5 6 7 8 9 20 21 22 23 24 25 26 27 28 29
node 0 size: 65430 MB
node 0 free: 3096 MB
node 1 cpus: 10 11 12 13 14 15 16 17 18 19 30 31 32 33 34 35 36 37 38 39
node 1 size: 65536 MB
node 1 free: 7359 MB
node distances:
node   0   1
  0:  10  21     <<< distance indicate how long it will take to communicate betwee nodes
  1:  21  10
```

Set CPU and memory affinity

```
#> numactl --cpunodebind=1 --membind=1 emacs
```

## cgroup

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/resource_management_guide/ch01


# Extend reading

[Managing Process Affinity in Linux](./http://www.glennklockwood.com/hpc-howtos/process-affinity.html#3-5-getfreesocket)


# Reference

https://codywu2010.wordpress.com/2015/09/27/isolcpus-numactl-and-taskset/

https://fatmin.com/2016/01/06/numa-cpu-pinning-with-kvmvirsh/

http://cenalulu.github.io/linux/numa/

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/resource_management_guide/ch01

https://www.kernel.org/doc/Documentation/cgroup-v1/cpusets.txt

http://www.ksingh.co.in/blog/2016/09/08/working-with-numa-cpu-pinning/

http://fishcried.com/2015-01-09/cpu_bindings/

https://www.v2ex.com/t/246792

http://linux.vbird.org/linux_enterprise/cputune.php#1.3

https://huataihuang.gitbooks.io/cloud-atlas/content/os/linux/kernel/cpu/cpu_affinity.html

http://www.sohu.com/a/136174060_610730
