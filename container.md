# Container 運作原理

## Open Container Initiative (OCI)
- OCI 是 Linux Foundation 所提出的計畫，主要是希望能夠針對 Linux Container (容器) 制定一個基於作業系統層級的虛擬化開放標準
- 有 Runtime Spec 和 Image Spec 兩種
- Runtime Spec 規範的範疇
    1. 設定檔案格式: 該內容基本上會根據不同平台而有不同的規範，同時也明確表示創建該平台上 container 所需要的一切參數。
    2. 執行環境一致: 確保 container 運行期間能夠有一致的運行環境
    3. Container 的生命週期: 支持對 Container 的操作行為，例如 Create, Start, Delete, Kill 等
- Image Spec 規範的範疇
    1. Layer: 相關的檔案系統配置，檔案的位置/內容/權限
    2. Image Index: 用來描述該 Image 的檔案
    3. Configuration: 應用程式相關的設定檔案，包含了使用的參數，用到的環境變數等
    4. 其他細節可參考這篇[文章](https://ithelp.ithome.com.tw/articles/10216215)

## Containerd 
containerd（container daemon）是一個 daemon process 用來管理和運行容器，可以用來 pull/push 鏡像和管理容器的存儲和網絡。其中可以使用 runC 來創建和運行容器

## Containerd-Shim
為了滿足一些軟體設計上的需求，containerd 並沒有直接呼叫 runC containerd-shim, containerd 會創建一個獨立的 process containerd-shim 並由其呼叫 runC 來真正創建 container

## runC (Run container)
- OCI 標準實作的輕量级容器運行工具，用来創建和運行容器。而 Containerd 是用来維持通過 runC 創建的容器的運行狀態。即 runC 用来創建和運行容器，containerd 為常駐程序用来管理容器
- runC 包含 libcontainer，包括對 namespace 和 cgroup 的操作

## Namespace 
- 讓一個行程只能看見作業系統中的部分資源的機制。不同種類的 namespace 可以用來隔離不同的資源
- 目前 namespace 種類有[八種](https://man7.org/linux/man-pages/man7/namespaces.7.html): Cgroup, IPC, Network, Mount, PID, Time, User, UTS
- 透過將資源隔離達到 container 的目的
![示意圖](./Image/Namespace.png)

## Cgroup(Control Groups)
- 可以用來限制，控制與隔離 Process 所使用到的系統資源，例如 CPU, Memory, Disk I/O, Network 等
- 主要提供有底下四個功能 
   1. Resource Limiting: 可以設定資源的使用上限，其中也包含 File System 的 Cache
   2. Prioritization: 不同的 Group 可以擁有不同的 CPU 跟 Disk I/O 使用優先順序
   3. Accounting: 計算 Group 內的資源使用狀況，例如可以拿來當作計費的依據
   4. Control: 凍結或是重啟一整個 Group 的 Process
- 主要由四個元素構成
   1. Task: 運行於作業系統內的 Process，在 cgroups 內被稱為 Task
   2. Subsystem: 一種資源控制器，一個 Subsystem 負責管理一種資源，例如 CPU 或是 Memory，不同的作業系統所提供的 Subsystem 種類可能不盡相同
   3. Cgroup: Task 與 Subsystem 的關係連結，用來定義 Task 的資源管理策略，例如把一個 Task 加入到某個 cgroup 內，那麼這個 Task 使用資源時就必須遵守該 cgroup 中所定義的規範
   4. Hierarchy: 一群 cgroup 所組成的樹狀結構，每個節點都是一個 cgroup，一個 cgroup 可以有多個子節點，子節點預設繼承父節點的屬性；系統中可以有多個 hierarchy，換言之，就像一座森林一樣

## Summary
1. OCI 定義了 container 的規範，包含 Runtime Spec 跟 Image Spec
2. Client 端透過 containerd 直接呼叫 runC 來控制 container
3. Containerd 也可以透過 Containerd-Shim 呼叫 runC 來控制 container 
4. Namespace 用來隔離 container 的資源
5. 用 Cgroup 限制 process 所使用的系統資源


## Reference:
1. https://blog.tienyulin.com/open-container-initiative-oci/
2. https://ithelp.ithome.com.tw/users/20151857/ironman/5419
3. https://www.hwchiu.com/container-design-ii.html
4. https://www.qikqiak.com/k3s/runtime/cgroups-namespaces/
5. https://medium.com/starbugs/%E7%AC%AC%E4%B8%80%E5%8D%83%E9%9B%B6%E4%B8%80%E7%AF%87%E7%9A%84-cgroups-%E4%BB%8B%E7%B4%B9-a1c5005be88c
6. https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/resource_management_guide/sec-relationships_between_subsystems_hierarchies_control_groups_and_tasks
