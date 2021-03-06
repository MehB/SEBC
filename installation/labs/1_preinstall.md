1. Check the `vm.swappiness` on all nodes:

`$ sysctl vm.swappiness=1`
`$ echo "vm.swappiness = 1" >> /etc/sysctl.conf`

```
# cat /proc/sys/vm/swappiness
1
```

2. Show mount attributes of all volumes:

```
mount -l
/dev/xvde on / type ext4 (rw) [centos_root]
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
```

Only 1 root volume. Data will be stored in the same disk.


3. No non-root volume. To configure the reserve space of any non-root, ext-based volumes:

`mkfs.ext4 -m 0 /dev/sdx`

on existing partitions:

`tune2fs -m 0 /dev/sdx`


4. Disable Transparent hugepage:

``
echo never /sys/kernel/mm/transparent_hugepage/defrag
never /sys/kernel/mm/transparent_hugepage/defrag
``

5. List network interface configuration:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc pfifo_fast state UP qlen 1000
    link/ether 0a:cf:31:02:e0:15 brd ff:ff:ff:ff:ff:ff
    inet 172.31.14.88/20 brd 172.31.15.255 scope global eth0
    inet6 fe80::8cf:31ff:fe02:e015/64 scope link
       valid_lft forever preferred_lft forever
```

6. Nslookup from host 1 to host 2:

```
[root@ip-172-31-14-88 ~]# nslookup ip-172-31-31-1
Server:		172.31.0.2
Address:	172.31.0.2#53

Non-authoritative answer:
Name:	ip-172-31-31-1.us-west-2.compute.internal
Address: 172.31.31.1
```
```
[root@ip-172-31-14-88 ~]# host ip-172-31-31-1
ip-172-31-31-1.us-west-2.compute.internal has address 172.31.31.1
```
```
[root@ip-172-31-14-88 ~]# cat /etc/resolv.conf
; generated by /sbin/dhclient-script
search us-west-2.compute.internal
nameserver 172.31.0.2
```

7. NSCD service:
```
service nscd status
nscd (pid 1170) is running...
```

8. NTPD service:
```
service ntpd status
ntpd (pid  1250) is running...
```
