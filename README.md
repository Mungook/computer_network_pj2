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
make linear topology with 3 hosts and 3 switches(by default, 1 host assigns to 1 switch)
##### 1-1. links
```
mininet> links
h1-eth0<->s1-eth1 (OK OK) 
h2-eth0<->s2-eth1 (OK OK) 
h3-eth0<->s3-eth1 (OK OK) 
s2-eth2<->s1-eth2 (OK OK) 
s3-eth2<->s2-eth3 (OK OK)
```
check links between nodes
##### 1-2. ports
```
mininet> ports
s1 lo:0 s1-eth1:1 s1-eth2:2 
s2 lo:0 s2-eth1:1 s2-eth2:2 s2-eth3:3 
s3 lo:0 s3-eth1:1 s3-eth2:2 
```
check the port number of links
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
check the net to know how to connect between nodes
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
add flow rules to connect between host1 and host3
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
Without flow rules, there is no connection
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
With flow rules, successful connection
##### 2-3. switch information
```
mininet> sh ovs-ofctl show s1
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000001
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s1-eth1): addr:ea:52:85:33:12:0d
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s1-eth2): addr:52:27:98:cb:ce:2c
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s1): addr:ca:55:07:ae:a3:4b
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s2
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000002
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s2-eth1): addr:ee:21:c5:92:6a:62
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s2-eth2): addr:2e:5e:4b:fb:0a:12
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s2-eth3): addr:fa:82:b1:cb:58:60
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s2): addr:36:b4:6f:ce:4d:4a
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s3
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000003
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s3-eth1): addr:fe:09:07:04:42:d6
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s3-eth2): addr:5a:1a:c8:f5:2e:7a
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s3): addr:72:07:79:87:a4:46
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
```
check switch information(s1, s2, s3)
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
make tree topology with 8 hosts and 7 switches(2 hosts assign to 1 leaf switch)
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
check links between nodes
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
check the port number of links
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
check the net to know how to connect between nodes
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
add flow rules to connect between host1 and host8
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
Without flow rules, there is no connection
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
With flow rules, successful connection
##### 2-3. switch information
```
mininet> sh ovs-ofctl show s1
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000001
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s1-eth1): addr:b6:3c:b6:b2:1e:31
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s1-eth2): addr:2a:65:1f:80:06:38
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s1): addr:92:fe:86:ef:a8:40
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s2
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000002
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s2-eth1): addr:d6:27:67:ce:6f:b2
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s2-eth2): addr:0a:84:09:3d:0d:ec
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s2-eth3): addr:be:c3:ae:36:89:df
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s2): addr:5e:ca:09:2e:88:4f
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s3
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000003
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s3-eth1): addr:ae:aa:2e:5e:44:43
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s3-eth2): addr:f2:10:b9:03:b2:d4
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s3-eth3): addr:3e:77:ab:b9:37:57
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s3): addr:b2:c0:c3:b9:03:48
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s4
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000004
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s4-eth1): addr:ca:79:8c:27:f9:46
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s4-eth2): addr:4a:76:b1:24:54:c3
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s4-eth3): addr:f2:5d:38:76:5c:0e
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s4): addr:6e:39:d4:19:51:47
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s5
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000005
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s5-eth1): addr:82:da:a7:c0:4e:5b
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s5-eth2): addr:2e:60:e1:6c:48:71
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s5-eth3): addr:66:40:35:06:ec:22
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s5): addr:96:be:fe:15:39:41
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s6
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000006
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s6-eth1): addr:9a:54:9b:35:76:9c
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s6-eth2): addr:3a:65:cf:8d:c7:cf
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s6-eth3): addr:5e:6f:5f:55:bf:66
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s6): addr:06:d3:32:d6:7f:44
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
mininet> sh ovs-ofctl show s7
OFPT_FEATURES_REPLY (xid=0x2): dpid:0000000000000007
n_tables:254, n_buffers:256
capabilities: FLOW_STATS TABLE_STATS PORT_STATS QUEUE_STATS ARP_MATCH_IP
actions: output enqueue set_vlan_vid set_vlan_pcp strip_vlan mod_dl_src mod_dl_dst mod_nw_src mod_nw_dst mod_nw_tos mod_tp_src mod_tp_dst
 1(s7-eth1): addr:a2:27:4d:a9:64:24
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 2(s7-eth2): addr:ae:61:77:c6:63:0c
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 3(s7-eth3): addr:6e:02:31:d3:a3:7c
     config:     0
     state:      0
     current:    10GB-FD COPPER
     speed: 10000 Mbps now, 0 Mbps max
 LOCAL(s7): addr:2e:f4:09:6a:fd:48
     config:     PORT_DOWN
     state:      LINK_DOWN
     speed: 0 Mbps now, 0 Mbps max
OFPT_GET_CONFIG_REPLY (xid=0x4): frags=normal miss_send_len=0
```
check switch information(s1, s2, s3, s4, s5, s6, s7)
