# Network Debug Tools

## mtr

ref: <https://blog.gtwang.org/linux/mtr-linux-network-diagnostic-tool/>

- Get report with IP and entire hostname

```shell
sudo mtr --tcp --report -c 100 -b -w $IP
sudo mtr --tcp --port 80 --report -c 50 $IP
```

## iperf3

ref: <https://blog.gtwang.org/useful-tools/iperf-network-bandwidth-testing-tool-tutorial/>

- Server side

```shell
iperf3 -s -p 5001
```

- Client side

```shell
iperf3 -c $IP -t 30 -p 5001 -P 32
```

- Default test upload speed
- Test download speed

```shell
iperf3 -c $IP -t 30 -p 5001 -P 32 -R
```
