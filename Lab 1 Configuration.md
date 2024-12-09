![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733746637000phtu3y.png)
![gh](https://raw.githubusercontent.com/ndriannazriel04/Advanced-Network-Tech/main/obsidian/images1733747790000t2ts3t.png)

## Create VLANs and Enable VTP

DSW1 and DSW2 in server mode whilst others are VTP clients.

```
vlan 101
name VLAN101
vlan 102
name VLAN102
vlan 103
name VLAN103

VTP mode
vtp mode server
vtp domain ANDRIAN
vtp password ANDRIAN

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN
```

## Enable IP routing
```
ip routing
ipv6 unicast-routing
```

## Configure STP root bridge
Root bridge vlan 101,103 on DSW1
Root bridge vlan 102 on DSW2

- The default STP priority is `32768`. To make a switch the root bridge for a VLAN, assign it a **lower priority** than other switches for that VLAN.
- Priority values increment in multiples of `4096`. Use a value like `24576` for root bridge preference

##### DSW1
```
spanning-tree vlan 101 priority 24576
spanning-tree vlan 103 priority 24576
```

##### DSW2
```
spanning-tree vlan 102 priority 24576
```

## Configuring Basic Connectivity

##### DSW1
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int vlan 101
ip add 142.99.2.129 255.255.255.128 
ipv6 add 2001:142:99:101::1/64

int vlan 102
ip add 142.99.2.1 255.255.255.128
ipv6 add 2001:142:99:102::1/64

int vlan 103
ip add 142.99.0.1 255.255.254.0
ipv6 add 2001:142:99:103::1/64


```

##### DSW2
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all

int vlan 101
ip add 142.99.2.130 255.255.255.128 
ipv6 add 2001:142:99:101::2/64

int vlan 102
ip add 142.99.2.2 255.255.255.128
ipv6 add 2001:142:99:102::2/64

int vlan 103
ip add 142.99.0.2 255.255.254.0
ipv6 add 2001:142:99:103::2/64

```

##### DSW3
```
int <>
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan all
```

##### DSW4
```

```


##### PC1
```

```

##### PC2
```

```

##### PC3
```

```

##### PC4
```

```