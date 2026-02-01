#Login Mikrotik
Reset MikroTik
```
System -> Reset Configuration -> Yes
```
Penamaan MikroTik
```
System -> Identity -> namasiswa-ukk -> Ok
```
penambahan IP
```
IP -> Address -> + -> 172.16.100.xx/24 -> ether1 -> Ok
IP -> Address -> + -> 192.168.xx.1/29 -> ether2 -> Ok
IP -> Address -> + -> 192.168.100.1/24 -> ether3 -> Ok
```
Penambahan DNS
```
IP -> DNS -> servers = 8.8.8.8 -> ✔ allow remote request -> Ok
```
Penambahan Gateway
```
IP -> Routes -> + -> Gateway = 172.16.100.1 -> Ok
```
Penambahan DHCP
```
IP -> DHCP Server -> DHCP SETUP -> ether 3 -> NEXT TEROOOOOOOOOSSSSSSSSSSSSSSSSSS
```
Firewall
```
IP -> Firewall -> NAT -> +
== GENERAL ==
chain = srcnat
Out interfaces = ethr 1
== ACTION ==
action = masquerade
OK

# Tambahin 1 NAT lagi
IP -> Firewall -> NAT -> +
== GENERAL ==
chain = dstnat
Dst addres = 172.16.100.xx
protocol = TCP
DST Port = 9090
== ACTION ==
action = dst-nat
to address = 192.168.xx.2
port = 80
OK
