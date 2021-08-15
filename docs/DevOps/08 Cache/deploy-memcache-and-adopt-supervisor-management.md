## 1.修改主机名
```
vim /etc/hostname
vim /etc/hosts 
hostname <主机名>
```
##2.服务器磁盘挂载
```
vim /data/scripts/auto_fdisk.sh
```
##3.将磁盘挂载修改为uuid方式

```
blkid /dev/xvdb1
vim /etc/fstab
 UUID=41852b97-3630-42b1-b2ae-9d8f77922245 /data ext4 defaults 1 2
```
##4.初始化服务器
```
#!/bin/sh
yum clean all
systemctl stop firewalld.service
systemctl disable firewalld.service
sed -i 's/enforcing/disabled/g' /etc/selinux/config
yum -y install vim openssh* ntp wget screen bash-completion git
service ntpd stop
ntpdate time.nist.gov
sed -i 's/0.centos.pool.ntp.org/time.nist.gov/g' /etc/ntp.conf
chkconfig ntpd on
service ntpd restart
```
###5.安装程序
安装memcached与supervisord
###6.执行cache初始化脚本

```
#!/bin/sh -e

########## 1. 基础工作 start ##########

tmux_conf=/root/.tmux.conf

chk_service_super=`systemctl status supervisord.service | grep inactive`
if [[ -n $chk_service_super ]]
then
    echo "supervisord is inactive..."
else
    sudo service supervisord stop
fi

chk_service_hhvm=`systemctl status hhvm | grep inactive`
if [[ -n $chk_service_hhvm ]]
then
    echo "hhvm is inactive..."
else
    sudo service hhvm stop
fi

mkdir -p /data/logs
mkdir -p /data/backup
mkdir -p /data/components/
mkdir -p /data/scripts
mkdir -p /data/softs
mkdir -p /data/logs/access
mkdir -p /data/logs/general
mkdir -p /data/logs/logic
mkdir -p /data/logs/error/supervisor
chmod -R 777 /data/logs/*
chmod -R 777 /data/components/
# 常用类库
sudo yum -y install gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
# 常用软件
sudo yum -y install net-tools unzip vim lrzsz subversion tmux

# tmux配置
cat > $tmux_conf <<EOF
set-option -g default-terminal "screen-256color"

#设置前缀为Ctrl + x
set -g prefix C-x

#解除Ctrl+b 与前缀的对应关系
unbind C-b

#up
bind-key k select-pane -U
#down
bind-key j select-pane -D
#left
bind-key h select-pane -L
#right
bind-key l select-pane -R
#select last window
bind-key C-l select-window -l

#copy-mode 将快捷键设置为vi 模式
setw -g mode-keys vi

#bind C-k run "./bin/tmux-zoom.sh"
EOF
########## 基础工作 end ##########

########## 部署memcached start ##########

ip_addr=`ifconfig | grep 'inet' | grep -v '127.0.0.1' | cut -d: -f2 | awk '{ print $2}'`
sed -i "s/OPTIONS=\"-l 127.0.0.1\"/OPTIONS=\"-l ${ip_addr}\"/g" /etc/init.d/memcached
sed -i 's/CACHESIZE=977/CACHESIZE=2048/g' /etc/init.d/memcached
sed -i 's/MAXCONN=1024$/MAXCONN=10240/g' /etc/init.d/memcached

systemctl daemon-reload
service memcached restart

########## 部署memcached end ##########
```
### 7.配置supervisord
####  7.1 启动supervisord服务
 
```
service supervisord start
```
#### 7.2 修改supervisord配置文件

```
vim /etc/supervisord.conf

修改如下信息：
[include]
files = supervisord.d/*.ini

#[program:hhvm]
#command=/usr/bin/hhvm --mode server --user www --config /etc/hhvm/server.ini --config /etc/hhvm/php.ini --config /etc/hhvm/config.hdf
#numprocs=1 ; number of processes copies to start (def 1)
#directory=/tmp ; directory to cwd to before exec (def no cwd)
#autostart=true ; start at supervisord start (default: true)
#autorestart=unexpected ; whether/when to restart (default: unexpected)
#stopwaitsecs=10 ; max num secs to wait b4 SIGKILL (default 10)
```
#### 7.4 添加memcached信息

```
vim /etc/supervisord/mc_11211.ini
vim /etc/supervisord/mc_11212.ini
vim /etc/supervisord/mc_11213.ini
```
添加如下信息

```
[program:mc_11211]
command=/usr/local/memcached/bin/memcached -p 11211 -u memcached -m 2048 -c 10240 -l 10.0.0.51
user=root                                ;执行命令的用户
numprocs=1                            ; 启动几个进程 默认 1
#process_name=%(process_num)02d
;directory=                              ; 执行前要不要先cd到目录去
autostart=true                          ; 随着supervisord的启动而启动
autorestart=true                        ; 是否自动重启 默认true
startretries=5                          ; 启动失败时的最多重试次数 默认5
;;exitcodes=0                            ; 正常退出代码
;;stopsignal=KILL                         ; 用来杀死进程的信号
;;stopwaitsecs=10                        ; 发送SIGKILL前的等待时间
redirect_stderr=true                     ; 重定向stderr到stdout
stdout_logfile=/data/logs/error/supervisor/mc_11211.log
stderr_logfile=/data/logs/error/supervisor/mc_11211.log
```
### 8.最终配置
关闭防火墙服务

```
systemctl stop firewalld.service
systemctl disable firewalld.service
```

关闭memcached服务

```
service memcached stop
```
关闭memcached开机自启

```
chkconfig memcached off
```

重启supervisord，采用supervisord启动memcached

```
service supervisord restart
```


