NOTE : REDO THIS EXERCISE USING THE OTHER WAY
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734969217000ircczm.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594637000us29r4.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594695000r6m7d5.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1736594741000zgmf2b.png)





![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734401545000n5d73y.png)

## Interface Configuration
##### R3
```
int g1/0
ip add 133.99.1.1 255.255.255.252
ipv6 add 2001:133:99:1::0/127
no sh
```

##### R2
```
int g1/0
ip add 133.99.1.2 255.255.255.252 
ipv6 add 2001:133:99:1::1/127
no sh

int g2/0
ip add 133.99.1.5 255.255.255.252 
ipv6 add 2001:133:99:2::0/127
no sh
```

##### R1
```
int g2/0
ip add 133.99.1.6 255.255.255.252 
ipv6 add 2001:133:99:2::1/127
no sh

int g3/0
ip add 101.100.133.99 255.255.255.0
ipv6 add 2001:101:100:133::99/64
no sh
```

##### R1(Physical)
```
int g0/1
ip add 133.99.1.6 255.255.255.252 
ipv6 add 2001:133:99:2::1/127
no sh

int g0/2
ip add 101.100.133.99 255.255.255.0
ipv6 add 2001:101:100:133::99/64
no sh
```


## Create loopback
##### R3
```
int l0
ip add 133.99.99.99 255.255.255.255
ipv6 add 2001:133:99:99::99/128
```
##### R1
```
int l0
ip add 133.99.99.100 255.255.255.255
```
## Configure OSPF

##### R3
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 1.1.1.1

int l0
ip ospf 1 area 0

int g1/0
ip ospf 1 area 0
```

```
int l0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0

int g1/0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0
```
##### R2(Default Gateway)
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 2.2.2.2
default-information originate always

int g1/0
ip ospf 1 area 0
```

```
int g1/0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0

ipv6 router ospf 1
default-information originate always
```

Verify
```
show ospf neighbors
show ip route ospf
```

buang static route
## Configure eBGP
##### R1
```
router bgp 19
bgp router-id 1.1.1.1
neighbor 101.100.133.95 remote-as 15
network 133.99.0.0 mask 255.255.0.0

bgp log-neighbor-changes
neighbor 2001:101:100:133::95 remote-as 159
address-family ipv6
neighbor 2001:101:100:133::95 activate
network 2001:133:99::/48
```
R1 Haiqal
```
router bgp 159
bgp router-id 2.2.2.2
neighbor 101.100.133.99 remote-as 19
network 133.95.0.0 mask 255.255.0.0

bgp log-neighbor-changes
address-family ipv6
neighbor 2001:101:100:133::99 remote-as 19
neighbor 2001:101:100:133::99 activate
network 2001:133:95::/48
```
Configure Null Route
```
ip route 133.99.0.0 255.255.0.0 Null0
ipv6 route 2001:133:99::/48 Null0
```
Don't forget to add the Null route because the route needs to be in the routing table for bgp to work.

Verify
```
show running-config | section router bgp
show ip bgp
show bgp ipv6 unicast

network 101.100.133.0 mask 255.255.255.0
network 2001:101:100:133::/64
```

## Configure Static routes on R2 and R1

##### R2
```
ip route 133.95.0.0 255.255.0.0 g2/0
ipv6 route 2001:133:95::/48 2001:133:99:2::1

ip route 0.0.0.0 0.0.0.0 g2/0
ipv6 route ::/0 2001:133:99:2::1
```

##### R1
```
ip route 133.99.99.99 255.255.255.255 g2/0
ipv6 route 2001:133:99:99::99/128 2001:133:99:2::

ip route 133.99.1.0 255.255.255.252 g2/0
ipv6 route 2001:133:99:1::/127 2001:133:99:2::
```
##### R1(Physical)
```
ip route 133.99.99.99 255.255.255.255 g0/1
ipv6 route 2001:133:99:99::99/128 2001:133:99:2::

ip route 133.99.1.0 255.255.255.252 g0/1
ipv6 route 2001:133:99:1::/127 2001:133:99:2::
```
Because of the route being configured as a /32, the only way to ping from one loopback to another loopback is to use the source ip of the loopback itself. 
For example:
```
ping 133.99.99.99 source 133.71.99.99
```
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1734309160000af22gt.png)

If you want to configure so that it doesn't need to use the source ip of the loopback, configure a static route for the next link.

For creating ipv6 routes, use the next hop ip instead of the ip address.
In IPv6, routing requires either a next-hop IPv6 address or the interface to have a directly connected neighbor reachable through Neighbor Discovery Protocol (NDP).



--------------------------------------------------------------------------
## Config Haiqal
## Interface Configuration
##### R6
```
int g1/0
ip add 133.95.1.1 255.255.255.252
ipv6 add 2001:133:95:1::0/127
no sh
```

##### R5
```
int g1/0
ip add 133.95.1.2 255.255.255.252 
ipv6 add 2001:133:95:1::1/127
no sh

int g2/0
ip add 133.95.1.5 255.255.255.252 
ipv6 add 2001:133:95:2::0/127
no sh
```

##### R4
```
int g2/0
ip add 133.95.1.6 255.255.255.252 
ipv6 add 2001:133:95:2::1/127
no sh

int g3/0
ip add 101.100.133.95 255.255.255.0
ipv6 add 2001:101:100:133::95/64
no sh
```

## Create loopback
##### R6
```
int l0
ip add 133.95.99.99 255.255.255.255
ipv6 add 2001:133:95:99::99/128
```

##### R4
```
int l0
ip add 133.95.99.100 255.255.255.255
```

## Configure OSPF

##### R6
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 1.1.1.1

int l0
ip ospf 1 area 0

int g1/0
ip ospf 1 area 0
```

```
int l0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0

int g1/0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0
```

##### R5(Default Gateway)
```
ip routing
ipv6 unicast-routing

router ospf 1
router-id 2.2.2.2
default-information originate always

int g1/0
ip ospf 1 area 0
```

```
int g1/0
ipv6 ospf network point-to-point
ipv6 ospf 1 area 0

ipv6 router ospf 1
default-information originate always
```

Verify
```
show ospf neighbors
show ip route ospf
```

## Configure eBGP
##### R4
```
router bgp 159
bgp router-id 2.2.2.2
neighbor 101.100.133.99 remote-as 19
network 133.95.0.0 mask 255.255.0.0

bgp log-neighbor-changes
address-family ipv6
neighbor 2001:101:100:133::99 remote-as 19
neighbor 2001:101:100:133::99 activate
network 2001:133:95::/48
```
Configure Null Route
```
ip route 133.95.0.0 255.255.0.0 Null0
ipv6 route 2001:133:95::/48 Null0
```
Don't forget to add the Null route because the route needs to be in the routing table for bgp to work.

Verify
```
show running-config | section router bgp
show ip bgp
show bgp ipv6 unicast

network 101.100.133.0 mask 255.255.255.0
network 2001:101:100:133::/64
```

## Configure Static routes on R5 and R4

##### R5
```
ip route 133.95.0.0 255.255.0.0 g2/0
ipv6 route 2001:133:95::/48 2001:133:99:2::1

ip route 0.0.0.0 0.0.0.0 g2/0
ipv6 route ::/0 2001:133:95:2::1
```

##### R4
```
ip route 133.95.99.99 255.255.255.255 g2/0
ipv6 route 2001:133:95:99::99/128 2001:133:95:2::

ip route 133.95.1.0 255.255.255.252 g2/0
ipv6 route 2001:133:95:1::/127 2001:133:95:2::
```


R2
no ip route 0.0.0.0 0.0.0.0 g2/0
ip route 133.95.0.0 255.255.0.0 g2/0