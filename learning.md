# Task 01
## 任务一
- ### `pwd=print working directory`  打印当前所在的绝对路径  
- ### `cd=change directory`  切换文件夹
- ### `ls`=列出当前目录内文件
- ### find . -type f -exec grep -l "PROJECT_ID" {} \;
    - `find`.:从当前目录(workspace)开始速归查找
    - `type f`:只找普通文件，排除文件夹
    - `-exec grep -l"PROJECT_ID"{}\;`对找到的每个文件执行grep, -l只输出匹配内容的文件名（查找包含指定字符串的文件）
- ### cat ./.project/metadata
    - `cat`:读取这个隐藏文件
- ### grep "PROJECT_ID" ./.project/metadata | cut -d '=' -f 2 > output/01_project_id.txt
    - `grep "PROJECT_ID" ./.project/metadata`:抓取包含PROJECT_ID的哪一行
    - `cut -d '=' -f 2`:以=作为分隔符，取第二段（-d分隔符，-f字段）
    - `>`:输出重定向，把结果写入后面的文件，覆盖原有内容
- ### cat output/01_project_id.txt
    查看文件是否写成功
----
## 任务二
- ### printf "../../.project/metadata" > output/01_relative_path.txt
    - `printf`:把xxx写入（不会自动换行）（最开始用的echo，会在末尾自动多加一个换行，结果判错）
    - 假设当前目录:workspace/src/utils/  
      utils→src: ..(第一个上跳)
      src→workspace: ..(第二个上跳)
- ### cat -A output/01_relative_path.txt
    - `cat -A`: 用来查看文件里看不见的控制字符
- ### cd ../
  ### ./check.sh 01
----
# Task 02
## 任务一
- ### chmod +x tools/recruit-info
    - `chomd`:修改文件权限
    - `+x`:增加执行权限
    - 补充学习：r读 w写 x执行
- ### ./tools/recruit-info
    - 测试运行
## 任务二
- ### cat > answers/02.md <<'EOF 文字内容 EOF
- ### cat answers/02.md
    - 检查内容
## 任务三
- ### export PATH="$PWD/tools:$PATH"
    - `$PWD`=当前目录（linux-shell-recruit）
    - `export`把变量到处给shell生效
    - 原理：把tools文件添加进当前shell的PATH环境变量
- ### recruit-info
    - 测试
# Task 03
- ### find workspace/project -type f | xargs grep -l 
    - `find workspace/project -type f`:速归查找，执照普通文件
    - `xargs`:把find找到的文件名传给后面grep
    - `grep -l`:只输出文件名，不输出匹配行
    - `-E "TODO|FIXME"`:正则，匹配TODO或者FIXME
- ### find workspace/project -type f | xargs grep -l -E "TODO|FIXME" | sort | uniq > output/03_code_search.txt
    - `find workspace/project -type f`:找出全部普通文件
    - `xargs grep -l -E "TODO|FIXME"`:筛选包含TODO/FIXME的文件，输出文件路径
    - `sort`:按字典序排序
    - `uniq`:去掉重复的路径，保证每个文件只出现一次
    - `>`:写入output/03_code_search.txt
    - `|`:管道，把前一条命令的标准输出当作后一条命令的标准输入
- ### cat output/03_code_search.txt
    - 查看生成的文件，核对
- ### ./check.sh 03
    - 执行校验脚本
# Task 04
## 任务一
- ### grep "ERROR" logs/server.log | wc -l
    - `grep "ERROR"`:筛选包含ERROR的日志
    - `wc -l`:统计行数
- ### printf "7" > output/04_error_count.txt
## 任务二
- ### `grep "ERROR" logs/server.log `
    - `grep "ERROR" logs/server.log `:拿到所有ERROR行
- ### `grep "ERROR" logs/server.log | cut -d' ' -f4 | cut -d'=' -f2`
    - `cut -d' ' -f4 `:以空格分隔
    - `cut -d'=' -f2`:以=分隔，取出用户名
- ### `grep "ERROR" logs/server.log | cut -d' ' -f4 | cut -d'=' -f2 | sort | uniq`
    - `sort`:字典排序
    - `uniq`:去重
- ### `grep "ERROR" logs/server.log | cut -d' ' -f4 | cut -d'=' -f2 | sort | uniq > output/04_error_users.txt`
- ### `cat output/04_error_users.txt`
> 简化版指令：grep "ERROR" logs/server.log | cut -d' ' -f4 | cut -d'=' -f2 | sort | uniq > output/04_error_users.txt
## 任务三
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2`
    - grep拿到所有ERROR行
    - cut提取code=xxx,再截取数字
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort`
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort | uniq -c`
    - sort排序，uniq -c统计每个错误码出现次数(左边=出现次数，右边=错误码)
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort | uniq -c | sort -nr`
    - sort -nr按数字降序（最大数在上面）
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort | uniq -c | sort -nr | head -1 | awk '{print $2}'`
    - head -1取第一行
    - awk '{print $2}'比cut更稳，避开空格坑
- ### `grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort | uniq -c | sort -nr | head -1 | awk '{print $2}' > output/04_top_code.txt`
> 简化版指令：grep "ERROR" logs/server.log | cut -d' ' -f5 | cut -d'=' -f2 | sort | uniq -c | sort -nr | head -1 | awk '{print $2}' > output/04_top_code.txt  

> ## 关于任务二和任务三中cut命令核心语法  
>> ### cut -d'分隔符' -f字段编  
>> - -d:delimiter，指定用什么字符切割文本，引号里写分割符号（以符号切成一小块一小块）
>> - -f:field,选择切割之后，想要第几段（字符）（挑出第N块） 

>> 例子：任务二中日至一行为：2026-09-22 12:00 ERROR user=alice code=404  
第一段管道：`cut -d' ' -f4`:以空格为分隔符，把整行切开  
1.2026-09-22  
2.12：00  
同理  
5：code=404  
`-f4`:取第四段，得到user=alice  
第二段管道：`cut -d'=' -f2

----
# Task 05
- ### cut -d' ' -f1 logs/access.log
    - 提取第一列地址 
- ### cut -d' ' -f1 logs/access.log | sort
    - 排序
- ### cut -d' ' -f1 logs/access.log | sort | uniq -c
    - 统计次数，输出格式：次数IP
- ### cut -d' ' -f1 logs/access.log | sort | uniq -c | sort -nr 
    - 按数字倒序
- ### cut -d' ' -f1 logs/access.log | sort | uniq -c | sort -nr |head -1
    - 只拿第一行
- ### cut -d' ' -f1 logs/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}'
    - 只保留IP，丢掉前面计数数字
> ### 最终合体版命令：cut -d' ' -f1 logs/access.log | sort | uniq -c | sort -nr | head -1 | awk '{print $2}' > output/05_top_ip.txt

> ### 小提示：
>> 1.sort必须放在uniq -c前面：uniq只合并相邻重复项  
>> 2.管道|：数据在内存里直接传给下一个命令，不产生临时文件  
>> 3.sort -nr:  
>>> - -n:当作数字比较（不是字典文本比较）
>>> - -r:reverse反转，降序，次数最多放在第一行
----
# Task 06
## 知识点先学：
1.stdin(标准输入，文件描述符0)：程序读取的输入，键盘
2.stdout(标准正常输出，文件描述符1)：正常信息  
3.stderr(标准错误输出，文件描述符2)：报错信息  
（stdout和stedrr是两条独立数据流，默认都会打印到终端）  
4.>:重定向标准输出stdout(等价 1>)  
5.2>:重定向标准错误stderr  
6.tee:一份输出同时打印到终端+写入文件
## 任务一
- ### ./tools/check-project > output/06_stdout.txt
    - 把stdout(正常信息)写入文件；stedrr错误信息一i就打印在屏幕上
## 任务二
- ### ./tools/check-project > output/06_stdout.txt 2> output/06_stderr.txt（一条命令同时分离stdout,stedrr）
    - `> output/06_stdout.txt 2`:正常输出→06_stdout.txt
    - `> output/06_stderr.txt`:错误输出→06_stedrr.txt
- ### cat output/06_stdout.txt
  ### cat output/06_stderr.txt
## 任务三（正常输出一边屏幕打印，一边存文件）（用tee命令）
- ### ./tools/check-project | tee output/06_tee.txt
    - `./tools/check-project`:运行程序，产生stdout和stedrr
    - `| tee output/06_tee.txt`:stdout交给tee（只传递stddout,不传递stedrr）（如果想要错误六也进入管道，需要2>&1,把二号重定向到1号，即把错误和正常输出全部一起存入同一个文件）(./tools/check-project > all.txt 2>&1)
    - `tee`（T型分流器，一进两出）功能；一份文件输出到终端（可见），一份写入文件（与>区别：>只写入文件，屏幕看不到）
# Task 07
## 增加权限：chmod +x scripts/analyze.sh
## 写脚本：nano scripts/analyze.sh
## 参数判断
> ### if [ $# -ne 1 ]; then
> ###    echo "Usage: ./scripts/analyze.sh FILE"
> ###    exit 1
> ### fi
> ### FILE="$1"
- `if [ $# -ne 1 ]; then`
    - `if`: 即如果，判断条件
    - `$#`: shell内置变量，代表运行脚本时，一共输入了多少个参数（./scripts/analyze.sh→没有传参数，为0；./scripts/analyze.sh a b→传了两个参数，为2）
    - `ne`:not equal,不等于
    - `[条件]`:shell里面用来做判断的命令，左右两边必须空格
    - `; then`:条件成立的话，就执行then后面的代码
    - 整句：如果传入脚本的参数数量不等于1，就执行大括号里面的代码
- `echo "Usage: ./scripts/analyze.sh FILE"`
    - `echo`:打印文字到终端
    - `"内容"`:双引号包裹字符串。引号必须一对
    - 作用：给用户提示这个脚本正确用法是./scripts/analyze.sh文件
- `exit 1`
    - `exit`:直接结束脚本运行
    - `exit 0`:正常成功结束
    - `exit` 非0数字：代表程序异常，出错退出
- `fi`:代表if判断结束（shell的if必须用fi收尾）
- `FILE="$1"`
    - `$1`:shell特殊变量，第一个传入的参数
    - 把$1存进变量FILE,后面代码直接用FILE就能拿到用户传入的文件名
    - 加双引号:防止文件名里面有空格
> ### 功能：$#获取传入参数个数，不等于1，echo打印使用提示， exit 1返回错误码；如果数量正确，把第一个参数存入FILE变量，供后续代码使用  

> ### 测试
> ./scripts/analyze.sh  
> echo $?
## 验证文件是否存在
> ### if [ ! -f "$FILE" ]; then
> ###    echo "Error: file $FILE does not exist"
> ###    exit 1
> ### fi
- `if [ ! -f "$FILE" ]; then`
    - `!`:感叹号，代表取反/非(NOT)
    - `-F`:判断现象，含义：这个路径是一个普通文件（不是文件夹）
    - `"$FILE"`:上一段存的变量，即用户输入的文件名
    - 整句：如果输入的文件名不是一个普通文件→执行里面代码
- `echo "Error: file $FILE does not exist"`
    - `$FILE`会自动替换为输入的文件名
> ### 功能：检查输入文件是否存在（不存在或者是文件夹：条件成立，打印报错，结束脚本）（文件存在且是普通文件：条件不成立，继续向下运行脚本）
## 
> ### 测试：
> ./scripts/analyze.sh abc.log
> echo $?
## 统计ERROR数量+找出出现最多的状态码
### 查看日志行：grep "ERROR" logs/server.log | head -n3
- `grep "ERROR" logs/server.log`
    - `grep`:检索工具
    - `"ERROR"`:要找到关键词
    - `logs/server.log`:要检索的文件路径
- `head -n3`
    - `head`:用来去文本开头的行
    - `-n`:指定行数（-n3只拿前三行）
- > ### 相较于cat整个日志的优势；防止日志过长，看不过来；只关心ERROR的行；只取前三行，快速看日志的字段结构，用来数awk的列号）
> ### total_error=$(grep "ERROR" "$FILE" | wc -l)
> ### top_code=$(grep "ERROR" "$FILE" | awk '{print $5}' | sed 's/code=//g'| sort | uniq -c | sort -nr | head -n1 | awk '{print $2}')
> ### echo "Total ERROR: $total_error"
> ### echo "Top Code: $top_code"
- `total_error=$(grep "ERROR" "$FILE" | wc -l)`(统计日志里包含ERROR的行)(注意空格)
    - `$`:命令替换。把括号里面命令执行的结果，拿出来存到变量total_error
    - `grep "ERROR" "$FILE"`:读取输入文件，筛选包含ERROR的所有行
    - `wc -l`:统计行数
- `top_code=$(grep "ERROR" "$FILE" | awk '{print $3}' | sort | uniq -c | sort -nr | head -n1 | awk '{print $2}')`
    - `grep "ERROR" "$FILE"`:拿到所有带ERROR日志行
    - `awk '{print $5}'`:awk用来切分一行文本，默认按空格分割;$3代表一行里第五列内容状态码（状态码：200请求成功，404页面找不到，500服务器内部错误，403权限禁止访问）
    - `sed`:流编辑器，用来替换文本
    - `s/A/B/g`:把A替换成B（`s/code=//g`:把字符串code=替换为空，删掉它）
    - `awk '{print $2}')`:去除这一行的第2列，也就是状态码（第一列是出现次数）
    - 整句：提取所有ERROR对应的状态码，统计每个状态码出现次数，拿到次数最多的状态码，存入top_code变量）
> ### 测试：
> ./scripts/analyze.sh logs/server.log
> echo $?
> ./check.sh 07
----
# Task 08
### 代码错误原因：缺少双引号（不带引号的变量，遇到空格就会切割字符串，导致带空格文件直接炸掉
- `destination="$1"`  
给$1加上双引号，目标路径如果带空格，也能完整识别。
- `mkdir -p "$destination"`  
$destination加上引号。-p参数本来是好的（作用：目录不存在就创建，存在不报错。）    
- `for file in "$@"`  
原来写 $@，不带引号，带空格文件名会被拆成多个参数。
"$@"（双引号包裹$@）：保留每一个传入参数原始边界，"My Report.txt"会被当成单个文件，不会在空格处切开。
- `cp "$file" "$destination"/`  
"$file"：文件变量套双引号，文件名里面的空格被保留，cp识别成一个完整文件。
"$destination"/：目标目录也加上引号，防止目标路径带空格。