```
#!/bin/bash

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