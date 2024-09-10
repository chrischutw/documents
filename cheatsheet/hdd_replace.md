# HDD replace

<https://pixnet.slack.com/archives/C082R65U1/p1595384682021900?thread_ts=1595384682.021900>
<https://pixnet.slack.com/archives/C082R65U1/p1595383312020100?thread_ts=1595383312.020100>

1. 將換掉的硬碟退出 raid 1

    ```shell
    mdadm --manage /dev/md0 --remove /dev/sda1
    mdadm --manage /dev/md1 --remove /dev/sda2

    if encounter mdadm: hot remove failed, do the following first
    mdadm --manage /dev/md0 --fail /dev/sda1
    ```

2. 檢查raid 狀態

    ```shell
    mdadm --detail /dev/md0
    mdadm --detail /dev/md1
    ```

3. 拔硬碟前

    ```shell
    echo 1 > /sys/block/sda/device/delete
    ```

4. 確認 swap 是否移除

    ```shell
    cat /proc/swaps
    ```

5. 將好的sdb partition table copy到新的sda上

    ```shell
    sfdisk -d /dev/sdb | sfdisk /dev/sda 
    or 
    sdfisk -d /dev/sdb > sdb-table 
    sfdisk /dev/sda < sdb-table
    ```

6. 將sda1 跟sda2 分別加入 md0 md1

    ```shell
    mdadm /dev/md0 --add /dev/sda1
    mdadm /dev/md1 --add /dev/sda2
    ```

7. 將sda5 加入swap

    ```shell
    mkswap /dev/sda5 
    swapon /dev/sda5 
    ```

## Manual check disks

```shell
dd if=/dev/sdc of=/dev/null  bs=512M count=100000
```

## other mdstat command

- check status: `cat /proc/mdstat`
- stop md `sudo mdstat --stop /dev/md127`

## remove md127

```shell
mdadm --stop /dev/md127
mdadm --remove /dev/md127
```

## fdisk

```shell
fdisk /dev/sda
g 
n
```

## make raid 1 disk from bare matel

ref: <https://wiki.debian.org/SoftwareRAID>

```shell
# make partition 
fdisk /dev/sda and sdb
# install xfs 
apt install xfsprogs
# set up raid 1
#ref: https://wiki.debian.org/SoftwareRAID
mdadm --create /dev/md1 --level=1 --raid-devices=2 /dev/sda3 /dev/sdb3
# Create the mdadm config file
mdadm --detail --scan /dev/md0 >> /etc/mdadm/mdadm.conf
# mkfs.xfs
mkfs.xfs -f /dev/md1
```

## zpool

- check which disk is in zoopl

```shell
ls -al /dev/disk/by-partlabel/
```

## zfs

```shell
# 把 sdb 分割表倒出來
sfdisk -d /dev/sdb > sdb.table
# 編輯它，把 uuid 欄位通通砍掉，然後把 name="storage02" 換成 name="storage01"
vim sdb.table
# 倒進去 sda 蓋掉分割表
cat sdb.table | sfdisk /dev/sda
```
