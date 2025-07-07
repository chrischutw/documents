# Cheat Sheet

## Add swap on ubuntu 22.04

ref: <https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-22-04>
ref: <https://itsfoss.com/swap-size/>

```shell
sudo fallocate -l 1G /swapfile
ls -lh /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

## Clean up journal log

### 1. 配置 `journal` 日誌的大小和保留時間

可以透過編輯 `/etc/systemd/journald.conf` 設置 auto roration 的條件，主要的參數包括：

- **`SystemMaxUse`**：系統級日誌的最大磁碟空間使用量。
- **`SystemKeepFree`**：保留的最小可用磁碟空間，以避免日誌填滿磁碟。
- **`SystemMaxFileSize`**：單個日誌檔案的最大大小。
- **`MaxRetentionSec`**：日誌保留的最長時間，超過該時間的日誌將被刪除。

例如，編輯 `/etc/systemd/journald.conf` 並設置如下：

```ini
[Journal]
Compress=yes
SystemMaxUse=500M
SystemMaxFileSize=50M
MaxRetentionSec=1month
Seal=yes
```

修改完成後，重新啟動 `systemd-journald` 來應用更改：

```bash
sudo systemctl restart systemd-journald
```

### 2. 手動清理和 rotate `journal` 日誌

可以使用 `journalctl` 命令手動清理舊的日誌檔案：

- **清理舊日誌**：指定保留的磁碟空間或保留時間。例如：

  ```bash
  sudo journalctl --vacuum-size=200M  # 將總大小減少到 200M 以下
  sudo journalctl --vacuum-time=2weeks  # 刪除超過兩週的日誌
  ```

這些命令將根據條件手動刪除過期日誌，並保留最新的日誌。

### 3. 查看並管理日誌的當前使用狀態

使用以下命令查看日誌的當前空間使用情況：

```bash
journalctl -

## WSL2 clock goes out of sync after resuming from sleep/hibernate

```shell
sudo hwclock -s
```

## openssl

```shell
# check certificate chain
openssl x509 -in wildcard-sinyi-in-20250321.crt -issuer -noout

# verify certificate chain
openssl verify -untrusted intermediate.pem server.crt
```

## tmux sync on

```shell
:setw synchronize-panes
```

## snap in WSL2

```shell
sudo apt-get update && sudo apt-get install -yqq daemonize dbus-user-session fontconfig
sudo daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
exec sudo nsenter -t $(pidof systemd) -a su - $LOGNAME
snap version
```

## install latest k9s

ref: <https://github.com/derailed/k9s?tab=readme-ov-file#installation>

```shell
# Linux
curl -sS https://webinstall.dev/k9s | bash
# Windows
curl.exe -A MS https://webinstall.dev/k9s | powershell
```

## Helm cheatsheet

ref: <https://helm.sh/docs/intro/cheatsheet/>

```shell
# Get values.yaml
helm show values harbor/harbor --version 1.9.3 >> values.yaml

# Install with namespace created
helm install postgres oci://registry-1.docker.io/bitnamicharts/postgresql-ha -f override-values.yaml --namespace postgres --create-namespace --version 12.2.0

# get K8s manifest
helm template vault hashicorp/vault --namespace vault --version 0.39.0 -f vault-values.yaml > ./manifests/vault.yaml

# Upgrade 
helm upgrade -f values.yaml harbor harbor/harbor --version 1.9.1 -n harbor
```

## Add permission to K8s to pull private container registry from GCP

ref: <https://support.count.ly/hc/en-us/articles/4411604040217-Docker-and-Kubernetes-Connecting-to-Private-GCR-and-Pulling-Images>

- Create `service account` and grant Container Registry Service Agent permission
- Download service account key and add it as a secret to k8s cluster

```shell
# grant service account with `Container Registry Service Agent`
kubectl create secret docker-registry container-registry \
  --docker-server=https://gcr.io \
  --docker-username=_json_key \
  --docker-password="$(cat sinyi-cloud-poc_sinyi-container-registry.json)"
```

- Add the imagePullSecrets to yaml file

```shell
spec:
  imagePullSecrets:
    - name: container-registry
```

### Add permission to K8s to pull private container registry from Harbor

ref: <https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/>

- Create `Robot Accounts` from harbor

```shell
kubectl create secret docker-registry harbor-registry --docker-server=harbor.intra.sinyi.com.tw --docker-username=harbor-reader --docker-password=8gqAMriFCILE8dQfAue9fwaiAPZHrQZc
```

## Blackbox exporter post monitor

ref: <https://abiydv.github.io/posts/prometheus-blackbox-monitor-post-api/>

## Grafana template

ref: <https://community.grafana.com/t/how-to-use-alert-message-templates-in-grafana/67537/3>
ref: <https://github.com/grafana/grafana/issues/57846>
ref: <https://grafana.com/docs/grafana/v9.1/alerting/contact-points/message-templating/example-template/>

```go
{{ with $values }}
{{ range $k, $v := . }}
  {{  index $v.Labels "resource.label.database_id" }} Cloud SQL CPU utilization > 80
  value: {{ humanize (index $v.Value )}}
{{ end }}
{{ end }}
```

### Go template functions

ref: <https://grafana.com/docs/grafana/latest/alerting/fundamentals/annotation-label/variables-label-annotation/#functions>

## gsutil

### rewrite storage class to archive

```shll
gsutil -m rewrite -s archive -r gs://sinyi-cloud-web-log/sinyi-cloud-web-code
```

## gcloud

### filter

```shell
# filter with no user attributes
gcloud compute disks list --filter="-users:*" --format="value(name, users)"

# cloudrun list minscale
gcloud run services list --format="table(metadata.name,spec.template.metadata.annotations.'autoscaling.knative.dev/minScale')" --filter="spec.template.metadata.annotations.'aut
oscaling.knative.dev/minScale'>0"
```

### format

```shell
gcloud compute instances list --format="value(name, networkInterfaces[0].accessConfigs.natIP)" --sort-by=name         
```

### Load Balancer Describe

```shell
gcloud compute url-maps describe sinyi-cloud-cloudrun-ilb --region=asia-east1
```

## DNS cache clear

```shell
resolvectl flush-caches
```

## useradd

```shell
groupadd ansible
useradd -m -s /bin/bash -g ansible ansible
```

## lvextend

```shell
lvdisplay
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv
```

## install pm2 CentOS7

```shell
# install nodejs 
curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -
sudo yum install nodejs -y

# install pm2
npm install pm2@latest --global
```

## List all systemctl service

```shell
systemctl list-units --type=service
```

## Eanble smb and mount NFS

ref: <https://learn.microsoft.com/zh-tw/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3?tabs=server>
ref: <https://learn.microsoft.com/en-us/powershell/module/smbshare/new-smbglobalmapping?view=windowsserver2022-ps>

```shell
Enable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\nt154.tw.sinyi.com\M200$ -Credential $creds -LocalPath X:
```

## Mount NFS in windows container docker

ref: <https://stackoverflow.com/questions/56170499/docker-windows-containers-and-nfs-volume>

```powershell
$creds = Get-Credential
New-SmbMapping -RemotePath \\10.42.205.122\nas\nas -Credential $creds -LocalPath X:
```

```shell
docker run -it --name test -v x:\1119\:c:\test mcr.microsoft.com/windows/servercore:ltsc2019 powershell
```

## Change Virtualbox image uuid

```powershell
cd D:\Program Files\Oracle\VirtualBox
.\VBoxManage internalcommands sethduuid 'D:\My Documents\UbuntuServer_22.04_VB-2\UbuntuServer_22.04_2.vdi'
```

- edit .vbox file
- replace `HardDisk uuid` and `Image uuid` with the new uuid from command
- replace `Machine uuid` with a random uuid generator
- replace vbox and harddisk name
- change MAC Address
