# kubernetes 架構介紹

## Master Node 
1. kube-apiserver：作為 Kubernetes API 的前端，接收和處理集群內外的 Authentication、Authorization、Access Control、API registration & discovery 請求
2. etcd：分布式鍵值存儲庫，負責保存整個 cluster 的運行狀態
3. kube-controller-manager：負責維護 cluster 的狀態，包含 failure detection、auto scale in/out、rolling update 等等
4. kube-scheduler：負責進行任務的調度，根據不同應用的需求，將 workload 調度到不同的機器去執行

## Worker Node
1. kubelet：負責接收來自 API server 的命令、維護 container 的生命周期，並負責 CSI(Container Storage Interface) & CNI(Container Network Interface) 的管理
2. kube-proxy：負責為服務提供網絡代理和負載平衡
    - 會在 node 中藉由 iptables 模式撰寫規則，完成轉遞流量目的（負載平衡），service 越多則 iptables 規則也越多
    - 目前內建的實作主流有兩派，分別是基於 iptables 與 ipvs 兩種不同的方式。
3. Container Runtime：常見的包括 Docker、containerd 等

## 核心元件之間的通訊

### External <–> Master
- 管理者從外面僅能透過 API server 進行對 Kubernetes cluster 的管理 (傳送 JSON 格式的資料)

### Inside Master
- 不論是負責管理 cluster 狀態的 controller manager，或是負責任務調度的 scheduler，要調整 worker 的工作狀態，都必須透過送 request 給 API server，再由 API server 統一對 host 上的 kubelet 進行操作 (這幾個元件之間傳送的資料格式為 protobuf)
- 在 master node 中的元件，唯一會對 etcd 服務進行 cluster 狀態存取的僅有 API server，雙方使用 gRPC 進行通訊

### Master <–> Worker
- 所有對 worker node 上的狀態變更，都是由 master node 上的 API server 所發過來，並由 kubelet 接收後進行處理 (透過 protobuf) 資料格式傳送資料)

### Inside Worker
- kubelet 透過 CRI(Container Runtime Interface)，控制 container runtime 進行 pod 的管理
- contain runtime 則是透過符合 OCI(Open Container Initiative) 介面，在 OS 中實際的產生出相對應的 container
- kubelet 透過 CNI(Container Network Interface)，呼叫 CNI plugin 來進行 pod network 的設定

## CRI
- 對於 kubernetes 來說，希望能夠透過一個標準介面與各個 contaienr runtime 解決方案銜接，這個銜接的接口標準就是所謂的 Container Runtime Interface(CRI)

## CNI
CNI 是一個與 CRI 相當的標準架構，其針對 kubernetes 內的網路功能，這網路功能就目前所知，可以包括以下幾項
1. Network Connectivity提供 Pod 上網能力，通常都會希望能夠有連接外網的能力
2. IPAM: 分配 IP 地址，幫每個 Pod 找一個獨立不重複的 IP，至於是 ipv4 或是 ipv6 都可以
3. 幫忙實現 Network Policy， kubernetes 內部有 Network Policy 去限制 Pod 與 Pod 之間的網路傳輸，然而 kubernetes 本身只有定義介面，仰賴各個 CNI 去完成

## K8s 架構
- 以Components 角度看Kubernetes
- 以服務角度看Kubernetes
- 網路狀態看Kubernetes



## Reference
1. https://godleon.github.io/blog/Kubernetes/k8s-CoreConcept-Cluster-Architecture/
2. https://www.hwchiu.com/ithome/2019/network/day10.html
3. https://ithelp.ithome.com.tw/articles/10284158
4. https://iter01.com/600456.html
5. https://www.hwchiu.com/k8s-network-issue.html