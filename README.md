# KECE449 Computer Networks Project2
## 2017171030 전기전자공학부 박문성
### A. linear
#### 1. topology creation
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
##### 1-1. links
```
mininet> links
h1-eth0<->s1-eth1 (OK OK) 
h2-eth0<->s2-eth1 (OK OK) 
h3-eth0<->s3-eth1 (OK OK) 
s2-eth2<->s1-eth2 (OK OK) 
s3-eth2<->s2-eth3 (OK OK)
```
##### 1-2. ports
```
mininet> ports
s1 lo:0 s1-eth1:1 s1-eth2:2 
s2 lo:0 s2-eth1:1 s2-eth2:2 s2-eth3:3 
s3 lo:0 s3-eth1:1 s3-eth2:2 
```
##### 1-3. net
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
#### 2. Setting switch forwarding table
##### 2-1. switch configuration
```
mininet> sh ovs-ofctl add-flow s1 priority=20,in_port=1,actions=output:2
mininet> sh ovs-ofctl add-flow s1 priority=20,in_port=2,actions=output:1
mininet> sh ovs-ofctl add-flow s2 priority=20,in_port=2,actions=output:3
mininet> sh ovs-ofctl add-flow s2 priority=20,in_port=3,actions=output:2
mininet> sh ovs-ofctl add-flow s3 priority=20,in_port=1,actions=output:2
mininet> sh ovs-ofctl add-flow s3 priority=20,in_port=2,actions=output:1
```
##### 2-2. ping
##### 2-2-a. before
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
##### 2-2-b. after
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

### B. tree
#### 1. topology creation
```
sudo mn --topo tree,depth=3 --switch ovs --controller remote
*** Creating network
*** Adding controller
Unable to contact the remote controller at 127.0.0.1:6653
Unable to contact the remote controller at 127.0.0.1:6633
Setting remote controller to 127.0.0.1:6653
*** Adding hosts:
h1 h2 h3 h4 h5 h6 h7 h8 
*** Adding switches:
s1 s2 s3 s4 s5 s6 s7 
*** Adding links:
(s1, s2) (s1, s5) (s2, s3) (s2, s4) (s3, h1) (s3, h2) (s4, h3) (s4, h4) (s5, s6) (s5, s7) (s6, h5) (s6, h6) (s7, h7) (s7, h8) 
*** Configuring hosts
h1 h2 h3 h4 h5 h6 h7 h8 
*** Starting controller
c0 
*** Starting 7 switches
s1 s2 s3 s4 s5 s6 s7 ...
*** Starting CLI:
```
##### 1-1. links
```
mininet> links
s1-eth1<->s2-eth3 (OK OK) 
s1-eth2<->s5-eth3 (OK OK) 
s2-eth1<->s3-eth3 (OK OK) 
s2-eth2<->s4-eth3 (OK OK) 
s3-eth1<->h1-eth0 (OK OK) 
s3-eth2<->h2-eth0 (OK OK) 
s4-eth1<->h3-eth0 (OK OK) 
s4-eth2<->h4-eth0 (OK OK) 
s5-eth1<->s6-eth3 (OK OK) 
s5-eth2<->s7-eth3 (OK OK) 
s6-eth1<->h5-eth0 (OK OK) 
s6-eth2<->h6-eth0 (OK OK) 
s7-eth1<->h7-eth0 (OK OK) 
s7-eth2<->h8-eth0 (OK OK) 
```
##### 1-2. ports
```
s1 lo:0 s1-eth1:1 s1-eth2:2 
s2 lo:0 s2-eth1:1 s2-eth2:2 s2-eth3:3 
s3 lo:0 s3-eth1:1 s3-eth2:2 s3-eth3:3 
s4 lo:0 s4-eth1:1 s4-eth2:2 s4-eth3:3 
s5 lo:0 s5-eth1:1 s5-eth2:2 s5-eth3:3 
s6 lo:0 s6-eth1:1 s6-eth2:2 s6-eth3:3 
s7 lo:0 s7-eth1:1 s7-eth2:2 s7-eth3:3 
```
##### 1-3. net
```
mininet> net
h1 h1-eth0:s3-eth1
h2 h2-eth0:s3-eth2
h3 h3-eth0:s4-eth1
h4 h4-eth0:s4-eth2
h5 h5-eth0:s6-eth1
h6 h6-eth0:s6-eth2
h7 h7-eth0:s7-eth1
h8 h8-eth0:s7-eth2
s1 lo:  s1-eth1:s2-eth3 s1-eth2:s5-eth3
s2 lo:  s2-eth1:s3-eth3 s2-eth2:s4-eth3 s2-eth3:s1-eth1
s3 lo:  s3-eth1:h1-eth0 s3-eth2:h2-eth0 s3-eth3:s2-eth1
s4 lo:  s4-eth1:h3-eth0 s4-eth2:h4-eth0 s4-eth3:s2-eth2
s5 lo:  s5-eth1:s6-eth3 s5-eth2:s7-eth3 s5-eth3:s1-eth2
s6 lo:  s6-eth1:h5-eth0 s6-eth2:h6-eth0 s6-eth3:s5-eth1
s7 lo:  s7-eth1:h7-eth0 s7-eth2:h8-eth0 s7-eth3:s5-eth2
c0
```
#### 2. Setting switch forwarding table
##### 2-1. switch configuration
```
mininet> sh ovs-ofctl add-flow s1 priority=20,in_port=1,actions=output:2
mininet> sh ovs-ofctl add-flow s2 priority=20,in_port=1,actions=output:3
mininet> sh ovs-ofctl add-flow s3 priority=20,in_port=1,actions=output:3
mininet> sh ovs-ofctl add-flow s3 priority=20,in_port=3,actions=output:1
mininet> sh ovs-ofctl add-flow s2 priority=20,in_port=3,actions=output:1
mininet> sh ovs-ofctl add-flow s1 priority=20,in_port=2,actions=output:1
mininet> sh ovs-ofctl add-flow s5 priority=20,in_port=2,actions=output:3
mininet> sh ovs-ofctl add-flow s5 priority=20,in_port=3,actions=output:2
mininet> sh ovs-ofctl add-flow s7 priority=20,in_port=3,actions=output:2
mininet> sh ovs-ofctl add-flow s7 priority=20,in_port=2,actions=output:3
```
##### 2-2. ping
##### 2-2-a. before
```
mininet> h1 ping -c3 h8
PING 10.0.0.8 (10.0.0.8) 56(84) bytes of data.
From 10.0.0.1 icmp_seq=1 Destination Host Unreachable
From 10.0.0.1 icmp_seq=2 Destination Host Unreachable
From 10.0.0.1 icmp_seq=3 Destination Host Unreachable

--- 10.0.0.8 ping statistics ---
3 packets transmitted, 0 received, +3 errors, 100% packet loss, time 2047ms
pipe 3
```
##### 2-2-b. after
```
mininet> h1 ping -c5 h8
PING 10.0.0.8 (10.0.0.8) 56(84) bytes of data.
64 bytes from 10.0.0.8: icmp_seq=1 ttl=64 time=1.97 ms
64 bytes from 10.0.0.8: icmp_seq=2 ttl=64 time=0.103 ms
64 bytes from 10.0.0.8: icmp_seq=3 ttl=64 time=0.103 ms
64 bytes from 10.0.0.8: icmp_seq=4 ttl=64 time=0.093 ms
64 bytes from 10.0.0.8: icmp_seq=5 ttl=64 time=0.059 ms

--- 10.0.0.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4068ms
rtt min/avg/max/mdev = 0.059/0.466/1.972/0.753 ms
mininet> pingall
*** Ping: testing ping reachability
h1 -> X X X X X X h8 
h2 -> X X X X X X X 
h3 -> X X X X X X X 
h4 -> X X X X X X X 
h5 -> X X X X X X X 
h6 -> X X X X X X X 
h7 -> X X X X X X X 
h8 -> h1 X X X X X X 
*** Results: 96% dropped (2/56 received)
```
