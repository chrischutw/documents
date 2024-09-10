# IMPI cheatsheet

## IPMI service port分家

- ip info path

```shell
##if path is 
/etc/systemd/network
##restart network by
systemctl daemon-reload; systemctl restart systemd-networkd

##eles
/etc/network/interface`
##restart network by 
/etc/init.d/networking restart
```

- other needed command

```shell
## show route 
ip route show ; or route -n
## add static route to specific device
ip route add 0.0.0.0/0 via 10.1.7.254 dev eno2 
## start interface 
ifup eno2
## clear ip settings in old interface
ifconfig eno1 0.0.0.0
```

## Get IPMI info

```shell
ipmitool lan print
```

## IPMI 取得用電量

```shell
ipmitool -I lan -H 10.11.0.10 -U root -P ./ipmitool dcmi power reading | grep Instantaneous | awk '{print $4,$5}'

ipmitool -I lan -H 10.2.0.102 -U root -f ./ipmitool sdr type 'Power Supply' |grep USAGE | awk '{print $9,$10}'
```

## IPMI pxe

```shell
ipmitool -I lanplus -U root -H 10.2.0.228 chassis bootdev pxe options=persistent
````

## IPMI disk

```shell
ipmitool -I lanplus -U root -H 10.2.0.228 chassis bootdev disk options=persistent
```

## IPMI bios

```shell
ipmitool -I lanplus -U root -H 10.2.0.228 chassis bootdev bios options=persistent
```

## Power reset

```shell
ipmitool -I lanplus -U root -H 10.2.1.79 chassis power reset
```

## IPMI reset cold

```shell
ipmitool -I lanplus -U root -H 10.2.1.166 mc reset cold
```
