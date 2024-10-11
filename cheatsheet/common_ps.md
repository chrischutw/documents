# Common powershell command cheatsheet

| Linux 指令               | PowerShell 指令             | 說明                                                |
|--------------------------|-----------------------------|-----------------------------------------------------|
| `ls`                     | `Get-ChildItem` (`gci`)      | 列出目錄內容                                        |
| `cd`                     | `Set-Location` (`sl`)        | 切換目錄                                            |
| `pwd`                    | `Get-Location` (`gl`)        | 顯示當前目錄                                        |
| `cp`                     | `Copy-Item` (`cp`)           | 複製檔案或目錄                                      |
| `mv`                     | `Move-Item` (`mv`)           | 移動或重新命名檔案或目錄                            |
| `rm`                     | `Remove-Item` (`rm`)         | 刪除檔案或目錄                                      |
| `mkdir`                  | `New-Item -ItemType Directory` (`mkdir`) | 建立新目錄                            |
| `rmdir`                  | `Remove-Item` (`rmdir`)      | 刪除空目錄                                          |
| `cat`                    | `Get-Content` (`gc`)         | 顯示檔案內容                                        |
| `echo`                   | `Write-Output` (`echo`)      | 輸出文字或內容                                      |
| `touch`                  | `New-Item`                   | 建立新檔案                                          |
| `find`                   | `Get-ChildItem -Recurse`     | 遞迴搜尋檔案或目錄                                  |
| `grep`                   | `Select-String`              | 搜尋檔案中的特定模式                                |
| `head`                   | `Get-Content -Head`          | 顯示檔案的前幾行                                    |
| `tail`                   | `Get-Content -Tail`          | 顯示檔案的後幾行                                    |
| `ps`                     | `Get-Process` (`ps`)         | 顯示正在執行的程序列表                              |
| `kill`                   | `Stop-Process`               | 終止程序                                            |
| `chmod`                  | `icacls`                     | 修改檔案權限                                        |
| `chown`                  | `icacls`                     | 修改檔案擁有者                                      |
| `df`                     | `Get-PSDrive`                | 顯示磁碟使用情況                                    |
| `du`                     | `Get-ChildItem -Recurse \| Measure-Object -Sum Length` | 顯示目錄或檔案的大小         |
| `top`                    | `Get-Process`                | 實時顯示系統資源使用狀況                            |
| `history`                | `Get-History`                | 顯示命令歷史記錄                                    |
| `alias`                  | `Set-Alias`                  | 設定命令別名                                        |
| `which`                  | `Get-Command`                | 找到指令的位置                                      |
| `uname`                  | `Get-ComputerInfo`           | 顯示系統資訊                                        |
| `ifconfig`               | `Get-NetIPAddress`           | 顯示網路介面資訊                                    |
| `netstat`                | `Get-NetTCPConnection`       | 顯示網路連接情況                                    |
| `curl`                   | `Invoke-RestMethod`          | 透過 URL 獲取或傳送數據                             |
| `wget`                   | `Invoke-WebRequest`          | 下載檔案                                             |
| `ssh`                    | `Enter-PSSession`            | 使用遠端會話登入遠端系統                            |
| `scp`                    | `Copy-Item -ToSession`       | 在本機與遠端系統間傳輸檔案                          |
| `env`                    | `Get-ChildItem Env:`         | 顯示環境變數                                        |
| `export`                 | `$env:VARIABLE=value`        | 設定環境變數                                        |
| `man`                    | `Get-Help`                   | 查看命令的幫助文件                                  |
