# sed使用整理

## 基本用法

1. 语法
   * `sed [-n] 'command' [file...]`
   * `sed [-n] -e 'command' [file...]`
   * `sed [-n] -f script-file [file...]`
   * sed总是以行为输入进行处理
   * sed处理的是原文件的拷贝

2. 主要参数
   * `-e`: 执行命令
   * `-f`: 执行一个sed脚本文件中的指令
   * `-i`: 将会直接修改原文件
   * `-n`: 读取下一行到pattern space

3. 常用命令
   * `sed 'm,nd' file`: 删除文件的m到n行
   * `sed 'm,nc some words' file`: 将文件的m到n行内容替换为some words，注意不是逐一替换
   * `sed '$i some words' file`: 在文件的最后一行前插入some words
   * `sed 'm,na some words' file`: 在文件的第m到n行每行后插入some words
   * `sed '/root/p' /etc/passwd`: 打印出包含root的行
   * `sed '/^$/d' file`: 去除空行
   * `sed 's/old/new/' file`: 将文件中的old替换为new，每行的第一处
   * `sed 's/old/new/g' file`: 将文件中的old替换为new，替换全部
   * `sed 's/old/new/p' file`: 将文件中的old替换为new，打印出原来行
   * `sed nq file`: 显示文件的前n行
