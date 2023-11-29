# Cisco commands

## Маршрутизация статическая

```bash
ip route
show ip route
show ip route static
show ip route Сетевой
ipv6 route
ipv6 unicast-routing    # активация ipv6 роутинга
show ipv6 route
show ipv6 route static
show ipv6 route сеть
ipv6 route ::/0         # по умолчанию
traceroute
```

## Маршрутизация динамическая

```bash
router rip 
version 2
version 1
no version
network 192.168.1.0
no auto-summary                 #не суммировать маршруты
passive-interface               #отключает интерфейс для пересылки обновлений маршрутизации
passive-interface default       #все интерфейсы пассивные
no passive-interface            #сделать интерфейс активным
default-information originate   #передать инфо по умолчанию (маршрут по умолчанию на роутере)
no router rip
show ip protocols
```

## admin distance

```bash
EIGRP =    90   D
IGRP =    100   
OSPF =    110   O
IS-IS =   115
RIP =     120   R
Static =    1   S   1
Connected = 0   C   0
```

```bash
cdp run                         #включить CDP (cisco discovery protocol)
show cdp neighbors detail       #соседние устройства
```

## DHCP

```bash
ip dhcp excluded-address
ip dhcp pool DEM
network 192.168.1.32 255.255.255.0
default-router
dns-server
```

## Настройка коммутатора

```bash
show boot
boot system 
erase startup-config
vlan <vlan_id>
name vlan_name
exit
interface <vlan_id>
interface interface_id 
switchport access vlan <vlan_id>
duplex auto
speed auto
mdix auto
show mac address-table
show history
show version
show interfaces
```

## SSH

```bash
show ip ssh
ip domain-name
crypto key generate rsa
crypto key zeroize rsa          #удалить ключ RSA
username <name> secret <password>
line vty 0 15
transport input ssh
login local
ip ssh version 2
```

## порты

```bash
shutdown
no shutdown
interface range                                                     # диапазон
switchport port-security                                            # включение безопасности портов
switchport port-security maximum 10                                 # кол-во мас-адресов
switchport port-security mac-address
switchport port-security mac-address sticky
no switchport port-security mac-address sticky
switchport port-security violation {protect | restrict | shutdown)  # реакция на нарушение (shutdown по-умолчанию)
show port-security interface <interface_id>
show port-security address
```

## VLAN

```bash
show vlan brief
vlan id <number>                                                #  2-1001  
name имя
switchport mode access
switchport access vlan id
switchport voice vlan id
mls qos trust[cos | device cisco-phone | dscp | ip-precedence]  # доверенное состояние интерфейса
no switchport access vlan
no vlan
delete (flash:)vlan.dat - del all vlan
show vlan summary
switchport mode trunk
switchport trunk native vlan id
switchport trunk allowed vlan 
no switchport trunk native vlan
тщ switchport trunk allowed vlan
show interfaces <interface_id> switchport
show interfaces trunk
```

## маршрутизация

```bash
interface g0/0.10
encapsulation dot1q (native) vlan_id
traceroute
```

## ACL

```bash
access-list 10 permit 192.168.10.0 0.0.1.255
masks:
host=0.0.0.0
any=255.255.255.255
access-list <acl_number> { deny | permit | remark } source [ source-wildcard ][ log ]
no access-list
show access-list
show access-lists
remark
ip access-group { access-list-number | access-list-name } { in | out }
no ip access-group
no access-list
```

## именнованные ACL

```bash
ip access-list {standard | extended} name
{permit | deny | remark} source [ source-wildcard ][ log ]
ip access-group name { in | out }
no 10
10 deny host 192.168.10.10
```

```bash
clear access-list counters - сбросить счетчики совпадения
access-class <acl_number> { in [ vrf-also ] | out }
```
