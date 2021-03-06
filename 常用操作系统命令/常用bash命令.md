# LINUX

# 磁盘文件

### 安文件系统于磁盘
```
lsblk -f
parted /dev/<NAME>
mklabel gpt
mkpart primary 0% 100%
print
quit
mkfs.ext4 /dev/<NAME>
mkfs.ntfs /dev/<NAME>
```

### 查看磁盘IO实时情况
```
iostat -x 1 10
```

### 查看磁盘空间情况
```
df -h
du -h
du | sort -rn | head
（du后单位是KB）
```

### 同步本地文件夹到linux上
```
scp -r folder root@116.62.187.159:folder
```

### 查看文件元信息
```
file <x>
```

### 判断file.txt是否存在
```
if test -s file.txt; then echo 'full'; else echo 'empty'; fi
```

# 文本处理

### bash乱码
```
vi ~/.zshrc 或者 vi ~/.bashrc
添加
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
再输入
source ~/.zshrc 或者 source ~/.bashrc
```

### while枚举每行并替换
```
cat clientip.txt | while read i; sleep 0.1; do cat curl.txt | sed "s/<clientip>/$i/g" | sh; done
```

### 排序，用聚合方式实现（比直接sort高效）
```
cat smalllist.txt | awk -F ' ' '{a[$4]+=$5;b[$4]+=1}END{for(i in a){print b[i],a[i],i}}' | sort -nr
```

### 按照,分隔，再按照第1列排序
```
sort -t, -nk1 file
sort -nk1
```

### json文本处理
```
cat x | jq '.a | .b | "\(.c),\(.e)"'
cat x | jq '.a | .[] | .b'
```

### 将奇偶行合为一行
```
cat 1.txt | awk 'NR%2 == 1{printf("%s\t",$0);}; NR%2 == 0{printf("%s\n",$0);}'
```

### 整合两个文件，用'\t'隔离每行
```
paste -d "\t" file1.txt file2.txt
```

### 查看1.log的最后一行在2.log的哪几行
```
(tail -1 1.log | awk -F '`' '{print $2}') | while read i; do grep $i 2.log -n; done
```

### 将文件编码格式从utf-8转为gbk
```
iconv -f UTF-8 -t GBK f1.txt > f2.txt
```

### vim 空格和TAB转换
```
空格替换为TAB
:set ts=4
:set noexpandtab
:%retab!

TAB替换为空格
:set ts=4
:set expandtab
:%retab!
```

# tcp协议簇

### curl测试各阶段时间
```
curl -vo/dev/null -s -w %{remote_ip}::%{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" http://tswork.peterpy.cn/universe/maichong.mp4
```

### 查看本地ip
```
curl -4 icanhazip.com
```

### 查看本机内网ip
```
ipconfig getifaddr en0
```

### 查看tcp端口开启情况
```
sudo lsof -i tcp:80 -s tcp:listen
sudo lsof -i -t tcp:80 -s tcp:listen
```

### 查看tcp端口监听情况
```
netstat -antlpe | grep 8080
telnet 127.0.0.1 8080
```

### 解析 ip 192.168.0.1，转成二进制查看
```
ipcalc 192.168.0.1
```

### ab测试命令
```
ab -n 100 <uri>
```

### 表单上传
```
curl -v https://upload.qiniup.com/ -F'token=<token>' -F'file=<content>' -F'key=<key>'
```

### tcpflow抓包
```
tcpflow -c port 80
```

### tcpdump抓包
```
tcpdump -i en0 -w abc.pcap host 124.163.208.161 and port 80
```

### tcpdump查看包
```
tcpdump -qns 0 -A -r abc.pcap
```

### 切割pcap包
```
editcap -c 10000 tcpdump.pcap tcpdump/
```

### 下载url-list.txt每行URL，并保存成文件名
```
cat url-list.txt | while read i; do
	echo $i;
	file=$(echo $i | cut -d "?" -f 1 | rev | cut -d "/" -f 1 | rev);
	echo curl -o $file $i | sh;
done
```

### wget下载，取content-disposition，以多重文件夹形式保存
```
wget --content-disposition -i url_file
wget -m -c --content-disposition -i url_file
```

### 检测a.b.com在1.2.3.4的证书时间
```
echo | openssl s_client -servername a.b.com -connect 1.2.3.4:443 2>/dev/null | openssl x509 -noout -dates
```

# DOCKER

### 查询镜像及容器
```
docker images
docker ps
```

### 通过dockerfile打包镜像
```
docker build -t <REPOSITORY:TAG> .
```

### 运行镜像
```
docker run -p <port:port> <REPOSITORY:TAG>
```

### 登录到实例上
```
docker run -ti <REPOSITORY:TAG> bash
docker exec -ti <CONTAINER ID> bash
```

### 将容器打包再成镜像
```
docker commit -a "" -m "" <CONTAINER ID> <REPOSITORY:TAG>
```

## qdoractl

https://developer.qiniu.com/dora/tools/1222/qdoractl

### 将本地镜像推送到服务端
```
qdoractl push <REPOSITORY:TAG>
```

### 查看instance
```
qdoractl instance <ufop> --all
```

### 登录到实例上
```
qdoractl attach <instance ID> --region <region> --instance <ufop>
```

# 其他

### screen异步窗口命令
```
screen -RL jobname
"use ctr + A + D to quit"
screen -ls
screen -r jobname
```

### 查看linux cpu配置
```
lscpu
```

### 命令升级变色
```
curl -> http（brew install httpie）
cat -> ccat（brew install ccat）
top -> htop（brew install htop）
```

### 生成公钥私钥
```
ssh-keygen -t rsa
```

### cd到brew中/include/openssl的所在目录
```
cd $(brew --prefix openssl)/include/openssl
```

### 时间戳转型输出
```
echo '154139500711111' | cut -c1-10 | awk '{print strftime("%Y:%m:%d %T", $1)}'
```

### 16进制转10进制
```
echo $((16#FF))
echo "ibase=16; FF" | bc
perl -le 'print hex("FF");'
printf "%d\n" 0xFF
python -c 'print(int("FF", 16))'
```

### 查看实时带宽
```
nload -m
```

### 查看进程情况
```
ps aux
```

### 杀掉进程pid
```
kill -9 <pid>
```
