# KECE449 Computer Networks Project2
## 2017171030 전기전자공학부 박문성
### 1. linear
#### a. topology creation
```
sudo mn --topo linear,3 --switch ovs --controller remote
*** Creating network
*** Adding controller
Unable to contact the remote controller at 127.0.0.1:6653
Unable to contact the remote controller at 127.0.0.1:6633
Setting remote controller to 127.0.0.1:6653
*** Adding hosts:
h1 h2 h3 
*** Adding switches:
s1 s2 s3 
*** Adding links:
(h1, s1) (h2, s2) (h3, s3) (s2, s1) (s3, s2) 
*** Configuring hosts
h1 h2 h3 
*** Starting controller
c0 
*** Starting 3 switches
s1 s2 s3 ...
*** Starting CLI:
```
#### b. links
```
mininet> links
h1-eth0<->s1-eth1 (OK OK) 
h2-eth0<->s2-eth1 (OK OK) 
h3-eth0<->s3-eth1 (OK OK) 
s2-eth2<->s1-eth2 (OK OK) 
s3-eth2<->s2-eth3 (OK OK)
```
#### c. ports
```
mininet> ports
s1 lo:0 s1-eth1:1 s1-eth2:2 
s2 lo:0 s2-eth1:1 s2-eth2:2 s2-eth3:3 
s3 lo:0 s3-eth1:1 s3-eth2:2 
```
#### d. net
```
mininet> net
h1 h1-eth0:s1-eth1
h2 h2-eth0:s2-eth1
h3 h3-eth0:s3-eth1
s1 lo:  s1-eth1:h1-eth0 s1-eth2:s2-eth2
s2 lo:  s2-eth1:h2-eth0 s2-eth2:s1-eth2 s2-eth3:s3-eth2
s3 lo:  s3-eth1:h3-eth0 s3-eth2:s2-eth3
c0
```
#### e. ping
#### e-1. before
```
mininet> h1 ping -c5 h3
PING 10.0.0.3 (10.0.0.3) 56(84) bytes of data.
From 10.0.0.1 icmp_seq=1 Destination Host Unreachable
From 10.0.0.1 icmp_seq=2 Destination Host Unreachable
From 10.0.0.1 icmp_seq=3 Destination Host Unreachable
From 10.0.0.1 icmp_seq=4 Destination Host Unreachable
From 10.0.0.1 icmp_seq=5 Destination Host Unreachable

--- 10.0.0.3 ping statistics ---
5 packets transmitted, 0 received, +5 errors, 100% packet loss, time 4093ms
pipe 4
```
#### e-2. after
```
mininet> pingall
*** Ping: testing ping reachability
h1 -> X h3 
h2 -> X X 
h3 -> h1 X 
*** Results: 66% dropped (2/6 received)
mininet> h1 ping -c5 h3
PING 10.0.0.3 (10.0.0.3) 56(84) bytes of data.
64 bytes from 10.0.0.3: icmp_seq=1 ttl=64 time=0.061 ms
64 bytes from 10.0.0.3: icmp_seq=2 ttl=64 time=0.094 ms
64 bytes from 10.0.0.3: icmp_seq=3 ttl=64 time=0.077 ms
64 bytes from 10.0.0.3: icmp_seq=4 ttl=64 time=0.060 ms
64 bytes from 10.0.0.3: icmp_seq=5 ttl=64 time=0.102 ms

--- 10.0.0.3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4079ms
rtt min/avg/max/mdev = 0.060/0.078/0.102/0.020 ms
```

### 2. tree
