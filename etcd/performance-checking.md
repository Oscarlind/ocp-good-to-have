# Troubleshooting / Performance checking
Small stuff for troubleshooting and performance checking ETCD.

Some things from the article has been extracted directly below. Use the article for full details.
## ETCD troubleshooting guide
A must read for this topic:

* [ETCD Troubleshooting](https://access.redhat.com/articles/6271341)

* [Backend performance](https://access.redhat.com/solutions/4770281)
## Description
The most common issues with etcd are caused by slow **storage**, **CPU overload** or **etcd database size growth**. Applying a request should normally take fewer than 50 milliseconds. If the average apply duration exceeds 100 milliseconds, etcd will warn that entries are taking too long to apply (took too long messages in the logs).

## Storage
One of the most commonly seen issue with ETCD stems from slow disks. Look in the troubleshooting article for a detailed recommendation and troubleshooting guide.

In short:

* Ensure storage performance that is to be used or is already in use (if cluster is installed).
* Check ETCD metrics in the OCP console.
    - **etcd_disk_backend_commit_duration_seconds_bucket** (p99 duration should be less than 25ms)
    - **etcd_disk_wal_fsync_duration_seconds_bucket** (p99 duration should be less than 10ms) to confirm the storage is reasonably fast.

## Network
Network can handle enough IO and bandwidth between masters is fast enough (ps: having masters in different DCs is not recommended as rather RHACM should be used, but in case there's no other option, network latency should be below 2ms.

Big network latency and packet drops can also bring an unreliable etcd cluster state, so network health values (RTT and packet drops) should be monitored. You can monitor metric etcd_network_peer_round_trip_time_seconds_bucket (p99 duration should be less than 50ms).
### time_connect
* Get into a master node, e.g. via SSH.
* curl -k https://api.<url>:6443 -w "%{time_connect}\n" -o /dev/null -s

where time_connect is expected below 2ms (0.002 in output) but usually anything above 6-8ms (0.006-0.008) means performance issues.

### Packet drops
Dropped packets or RX/TX errors can be viewed with

ip -s link show

ifstat <NIC>

ifstat -d 10     (run ifstat on all NICs every 10 seconds)

## CPU
Lack of CPU resources can also be a reason for ETCD issues. This is something we have observed on VMware environments previously.

* Monitor the control plane. Look for CPU overcommits and/or lack of resources to both VM and host machine (ESXi host)
* Use dedicated machines for the control plane.  