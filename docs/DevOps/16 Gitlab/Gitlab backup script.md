# Gitlab备份脚本

```
#!/bin/bash

# gitlab 备份路径
LocalBackDir=/var/opt/gitlab/backups

# 远程备份服务器 gitlab备份文件存放路径
RemoteBackDir=/data/gitlab_backup

# 备份Log存放路径
BackupLogsDir=/data/logs/gitlab

# 远程备份服务器 登录账户
RemoteUser=root

# 远程备份服务器 IP地址 （根据实际修改）
RemoteIP=


# 远程备份服务器 ssh端口号 （根据实际修改）
RemotePort=8022

# 当前系统日期
DATE=`date +"%Y-%m-%d-%H:%M:%S"`

# Log存放路径
LogFile=$BackupLogsDir/$DATE.log

# 新建日志文件
touch $LogFile


# Gitlab备份
/usr/bin/gitlab-rake gitlab:backup:create >> $LogFile

# 判断是否备份成功
if [ $? -eq 0 ]
then
    echo " $DATE Gitlab Backup Success! "
# >> $LogFile
    echo "-------------------------------------"
# >> $LogFile
else
    echo "$DATE Gitlab Backup Failed"
#>> $LogFile
   # mail -s 'Gitlab Backup Failed' ops@gokuaidian.com < $LogFile
    exit
fi

# 查找 本地备份目录下 时间为5分钟之内的，并且后缀为.tar的gitlab备份文件
BACKUPFILE_SEND_TO_REMOTE=$(find /var/opt/gitlab/backups -type f -mmin -5  -name '*.tar*')

# 追加日志到日志文件
echo "Gitlab auto backup to remote server, start at  $DATE"
#>>  $LogFile
echo "-------------------------------------"
#>> $LogFile

# 输出日志，打印出每次scp的文件名
echo "The file to scp to remote server is: $BACKUPFILE_SEND_TO_REMOTE"
#>> $LogFile

# 备份到远程服务器
scp -P $RemotePort $BACKUPFILE_SEND_TO_REMOTE $RemoteUser@$RemoteIP:$RemoteBackDir

# 追加日志到日志文件
echo "-------------------------------------"
#>> $LogFile

# 删除7天以上备份
find /var/opt/gitlab/backups/ -mtime +7 -name "*.*"  -exec rm -rf {} \;

#ssh -T $RemoteUser@$RemoteIP <<EOF
#    find /data/backup/gitlab/beifen/ -mtime +7 -name "*.*"  -exec rm -rf {} \;
#EOF

# 备份成功发送邮件
# mail -s 'Gitlab Backup Success!' abc@abc.com < $LogFile
```