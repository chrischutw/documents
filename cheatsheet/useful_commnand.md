# Useful command

## curl specific domain and ip 
```shell
curl https://rtb.tenmax.io/hello --resolve 'rtb.tenmax.io:443:10.10.128.11'
```


## smbclient
```shell
smbclient -U username%password //tenmaxsgwarehouse.file.core.windows.net/airflow-dags-stage -c 'prompt OFF;recurse ON; lcd /mnt/airflow-dags-stage; mget *'
```

## get public ip
```shell
curl ifconfig.io
```

## schedule Cron to Run Last Day of Month
```shell
59 23 28-31 * * [ "$(date +%d -d tomorrow)" = "01" ] && /root/script.sh
```

## select memory from sql
```shell
select name, value_in_use, description
from sys.configurations
where name like '%server memory%'
ORDER by name OPTION (RECOMPILE);
```

## load environment variables from file
```shell
if [ -f .env ]; then
  export $(echo $(cat .env | sed 's/#.*//g'| xargs) | envsubst)
fi
```

## remove passphrase
```shell
openssl rsa -in ~/.ssh/jenkins_id_rsa -out ~/.ssh/de_jenkins_id_rsa
```

## generate X-FarmID
```shell
< /dev/urandom head -c32 | sha256sum | awk '{print $1}'
```

## jq related
```shell
jq .response[].text file.json
curl -s https://www.cloudflarestatus.com/api/v2/summary.json | jq -c '.components[] | select(.name | . and contains("Taipei"))' | jq .status
```

## Extract content between two brackets
ref: https://stackoverflow.com/questions/18219030/how-can-i-extract-the-content-between-two-brackets
```shell
grep -oP '\(\K[^\)]+'
grep -oP 'array\(\K[^\)]+' ~/work/temp/aws.log | awk '($1 > 2000){ ++count } END{print count}'
```


## Download GD by wget
ref:https://medium.com/@acpanjan/download-google-drive-files-using-wget-3c2c025a8b99
- extract file id 

```shell
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1eg5NdANCkv6nboKRVS7ZCL05G7e52xgw' -O Nessus-8.15.0-debian6_amd64.deb
```

## Random characters
```shell
env LC_ALL=C tr -dc 'abcdefghjkmnprstwxyABCDEFGHJKLMNPRSTWXY3456789' < /dev/urandom | head -c 10 ; echo
```

## access log counting
```shell
sudo egrep “2021:20:3[0-9]” front.pixnet.net-2021-05-05_00000 |grep -v  robots.txt | awk ‘{print $1}’ | sort -n | uniq -c | sort -nr | head -20
```

## modify all serial number
```shell
sed -i "s/20[0-9][0-9]\{7\}/"$(date '+%Y%m%d')01"/g"
```

## replace strings in multiple files
- Linux
```shell
grep -r "string"  |awk -F":" '{print $1}' | sort | uniq | xargs -i sed -i 's/string1/string2/g' {}
```
- macOS
```shell
grep -r "string"  |awk -F":" '{print $1}' | cut -c 3- | sort | uniq| xargs sed -i '' 's/string1/string2/g'
```

## combine 5 lines in one line 
```shell
awk '{ printf "%s", $0; if (NR % 5 == 0) print ""; else printf "\t" }' temp > temp.csv
```

## replace the last ocurrence 
ref:https://stackoverflow.com/questions/11865845/replace-last-occurrence-in-line
ref:http://vimdoc.sourceforge.net/htmldoc/pattern.html#/%5Czs
```shell
:%s;.*\zs-;==;
```

## replace space to new line
```shell
:%s; ;crtl+v enter;g
```

## calculate netstat connection status
```shell
netstat -nat | awk '{print $6}' | sort | uniq -c | sort -n
netstat -nat | grep WAIT | awk '{print $5}' | sort | cut -d : -f 1 | uniq -c | sort -n
```

## TCPDUMP
```shell
sudo tcpdump -i eth0 -vnX 'port 3128 and dst host 10.1.1.48 and (not net 10.1.1.240/29)and (not net 10.1.0.248/30)' -w /tmp/test.package
```

## analyse accesslog 
```shell
sudo grep -e "2021:14:[2-3]9" /var/log/apache2/front.pixnet.net/access-2021-05-04.log | awk '{print $1}' | sort -n | uniq -c | sort -nr | head -50
```

## cut and sed
```shell
grep In temp.list1 | awk '{print $6}' | sed 's/\./-/4' | cut -d - -f 1 |cut -d : -f 1 |sort -u
```

## RRD calculator
```shell
/net/web/mrtg.pixnet.tw/95percent.pl -S -s "00:00 20210301" -e "00:00 20210401" /net/web/mrtg.pixnet.tw/ex4300-colocation/ex4300-colocation_734.rrd
```

## RYSNC single file to remote rsync daemon
```shell
rsync -av --progress /net/account/admin/chrischu/work/pixfront/webroot/.htaccess rsync://webfront-other-21:/pixnet/front.pixnet.net/webroot/
```

## ZFS send 
```shell
sudo zfs snapshot big@202106011020
sudo zfs send big@202106011020 | ssh pixdata100 'sudo zfs recv storage1/aqua_big@202106011020'
sudo zfs set compression=lz4 storage1/aqua_big@202106011020
```

## Tar ssh pipe
```shell
sudo tar cf - /bar | ssh pixdata100 ‘cd somewhere ； sudo tar xf - ‘
```

## 看 NVMe drive 的壽命
ref: https://www.plurk.com/p/odabt1
```shell
sudo apt install nvme-cli 後，可以用 sudo nvme smart-log /dev/nvme0n1 
```
