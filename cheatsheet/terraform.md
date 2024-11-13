# Terraform cheatsheet

## **Terraform 基本命令**

| 指令                        | 功能                                       |
|-----------------------------|--------------------------------------------|
| `terraform init`             | 初始化 Terraform 工作目錄，下載所需模組與 provider。 |
| `terraform plan`             | 顯示即將執行的基礎架構變更（不會實際執行）。 |
| `terraform apply`            | 應用變更到基礎架構。                      |
| `terraform destroy`          | 銷毀已建立的所有基礎架構資源。            |
| `terraform validate`         | 驗證 Terraform 配置文件是否正確。         |
| `terraform fmt`              | 自動格式化 Terraform 文件。               |
| `terraform graph`            | 生成依賴關係圖表。                        |

---

## **資源和模組操作**

| 指令                                   | 功能                                                                 |
|----------------------------------------|----------------------------------------------------------------------|
| `terraform state list`                 | 列出狀態檔案中管理的所有資源。                                        |
| `terraform taint <resource>`           | 標記一個資源為「需要重建」。                                          |
| `terraform untaint <resource>`         | 移除資源的「需要重建」標記。                                          |
| `terraform import <resource> <id>`     | 將現有的基礎架構資源導入 Terraform 管理。                            |
| `terraform state show <resource>`      | 顯示指定資源的狀態和屬性。                                            |
| `terraform plan -target=<resource>`    | 僅檢查並計劃指定資源的變更。                                          |
| `terraform apply -target=<resource>`   | 只應用指定資源的變更。                                                |
| `terraform plan -target=module.<mod>`  | 只檢查指定模組的變更。                                                |

---

## **工作空間（Workspace）**

| 指令                         | 功能                                                                  |
|------------------------------|-----------------------------------------------------------------------|
| `terraform workspace list`    | 列出所有工作空間。                                                   |
| `terraform workspace select <workspace>` | 切換到特定的工作空間。                                           |
| `terraform workspace new <name>`       | 創建新的工作空間。                                                |
| `terraform workspace delete <name>`    | 刪除指定的工作空間。                                              |

---

## **變數與輸出**

| 指令                                      | 功能                                                                 |
|-------------------------------------------|----------------------------------------------------------------------|
| `terraform output`                        | 顯示所有輸出變數。                                                   |
| `terraform output <name>`                 | 顯示特定輸出變數。                                                   |
| `terraform apply -var 'key=value'`        | 臨時傳遞變數值（單個變數）。                                         |
| `terraform apply -var-file="file.tfvars"` | 使用特定變數文件進行變更應用。                                       |

---

## **鎖定與排錯**

| 指令                              | 功能                                                                 |
|-----------------------------------|----------------------------------------------------------------------|
| `terraform refresh`               | 同步當前狀態與遠端資源，更新狀態檔案。                                |
| `terraform plan -refresh=false`   | 禁用自動狀態刷新（避免影響計劃的性能）。                              |
| `terraform apply -parallelism=N`  | 設定並行處理的資源數量來加速應用速度（預設 10）。                     |
| `TF_LOG=DEBUG terraform plan`     | 使用 debug 模式來排查問題，查看更多詳細日誌。                         |

---

## **遠端狀態管理**

| 指令                         | 功能                                                                  |
|------------------------------|-----------------------------------------------------------------------|
| `terraform state pull`        | 下載並查看當前的狀態檔案。                                            |
| `terraform state push`        | 將狀態檔案上傳至遠端狀態存儲。                                        |
| `terraform state mv`          | 移動狀態檔案中的資源。                                                |
| `terraform state rm <resource>`| 從狀態檔案中移除指定資源（不會銷毀實際資源）。                        |

---

## **最佳實踐**

- **模組化**：使用模組來組織和重用代碼。
- **遠端狀態**：使用遠端狀態（如 S3 或 Terraform Cloud）來管理狀態檔案。
- **工作空間**：使用工作空間來分離不同環境（如 `dev`、`staging`、`prod`）。
- **鎖定狀態**：啟用狀態鎖定，防止多個用戶同時修改狀態，避免衝突。

---

### **結論**

這份 cheatsheet 涵蓋了 Terraform 常見的操作場景，並提供了常用的指令以便快速參考。希望這能加速你在使用 Terraform 進行基礎架構管理時的工作流程！
