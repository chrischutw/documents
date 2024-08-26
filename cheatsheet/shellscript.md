# Shell Script Cheatsheet

##  :-, :+, :=, :? 
- :- 用於提供預設值，但不改變變數的值
    - 用途：如果變數未設置或為空，則使用指定的預設值
    - 格式：${variable:-default_value}
    - 作用：當 variable 沒有設置（未設置或設置為空）時，使用 default_value
    ```shell
    name=${user_name:-"default_user"}
    echo "The name is $name"
    The name is default_user
    ```
- :+ 用於提供替代值，僅在變數已設置時使用替代值。
    - 用途：如果變數設置了值，則使用指定的替代值；如果變數未設置或為空，則不進行替代
    - 格式：${variable:+alternate_value}
    - 作用：當 variable 設置了值時，返回 alternate_value；否則返回空字串
    ```shell
    greeting="Hello"
    message=${greeting:+ "Hi there!"}
    echo "$message"
    Hi there!
    ```
- := 用於提供預設值，並在變數未設置時修改變數的值。
    - 用途：如果變數未設置或為空，則將變數設置為指定的預設值並使用該值
    - 格式：${variable:=default_value}
    - 作用：如果 variable 未設置或為空，則將其設置為 default_value，並返回該值
    ```shell
    : ${NAME:="Default Name"}
    echo "Name is: $NAME"
    Name is: Default Name
    ```
- :? 用於在變數未設置時提供錯誤消息並退出腳本。
    - 用途：如果變數未設置或為空，則顯示錯誤並退出腳本。
    - 格式：${variable:?error_message}
    - 作用：如果 variable 未設置或為空，則打印 error_message，並退出腳本。
    ```shell
    filename=${1:? "Error: No filename provided"}
    echo "Processing file $filename"

    ```

## More :?
這裡有幾個使用 `:?` 運算符的範例，可以幫助你更好地理解它的用途：

### 範例 1：檢查環境變數是否設置
假設你在運行腳本時需要使用特定的環境變數，例如 `DATABASE_URL`，但該變數可能沒有設置。在這種情況下，你可以使用 `:?` 運算符來檢查變數是否存在，如果不存在，則提示錯誤並終止腳本。

```bash
#!/bin/bash

DATABASE_URL=${DATABASE_URL:? "Error: DATABASE_URL is not set."}

echo "Connecting to the database at $DATABASE_URL..."
# 其他的腳本邏輯
```

**說明：**如果 `DATABASE_URL` 沒有設置，腳本將輸出 `"Error: DATABASE_URL is not set."` 並退出。

### 範例 2：檢查命令行參數
假設你的腳本需要至少一個命令行參數，否則應該提示用戶並退出。在這種情況下，你可以使用 `:?` 來檢查第一個參數是否提供。

```bash
#!/bin/bash

input_file=${1:? "Error: No input file provided. Usage: $0 <input_file>"}

echo "Processing file: $input_file"
# 其他的腳本邏輯
```

**說明：**如果運行腳本時未提供參數，將輸出 `"Error: No input file provided. Usage: ./script_name <input_file>"`，然後退出腳本。

### 範例 3：檢查目錄是否設置
假設腳本需要一個目錄路徑來存儲生成的文件，你可以使用 `:?` 來確保這個目錄變數已設置。

```bash
#!/bin/bash

output_dir=${OUTPUT_DIR:? "Error: OUTPUT_DIR is not set."}

echo "Saving files to directory: $output_dir"
# 其他的腳本邏輯
```

**說明：**如果 `OUTPUT_DIR` 沒有設置，腳本將輸出 `"Error: OUTPUT_DIR is not set."`，並退出。

### 範例 4：檢查用戶是否指定正確的參數
在需要多個參數的情況下，你可以使用 `:?` 檢查每個參數是否設置並有效。

```bash
#!/bin/bash

source_file=${1:? "Error: No source file provided."}
dest_file=${2:? "Error: No destination file provided."}

echo "Copying from $source_file to $dest_file..."
cp "$source_file" "$dest_file"
```

**說明：**如果腳本運行時沒有提供 `source_file` 或 `dest_file`，則會提示相應的錯誤信息並退出。

### 範例 5：檢查必要的配置文件
如果你的腳本依賴於某個配置文件，你可以用 `:?` 檢查這個文件的路徑是否已經設置。

```bash
#!/bin/bash

config_file=${CONFIG_FILE:? "Error: CONFIG_FILE is not set. Please specify the configuration file."}

echo "Loading configuration from $config_file..."
# 其他的腳本邏輯
```

**說明：**如果 `CONFIG_FILE` 變數沒有設置，腳本將輸出 `"Error: CONFIG_FILE is not set. Please specify the configuration file."`，並退出。

這些範例展示了 `:?` 運算符在不同場景中的應用，主要用於強制變數設置，並在未設置時提供明確的錯誤消息。