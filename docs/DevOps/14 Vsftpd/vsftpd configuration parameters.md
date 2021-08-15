* vsftpd.conf

```bash
anonymous_enable=NO
local_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
xferlog_file=/var/log/vsftpd.log
xferlog_std_format=YES
ascii_upload_enable=YES
ascii_download_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES
guest_enable=YES
guest_username=ftpuser
pam_service_name=vsftpd
user_config_dir=/etc/vsftpd/vsftp_user_conf
virtual_use_local_privs=YES
pasv_enable=YES
pasv_min_port=20000
pasv_max_port=30000
```
* 虚拟用户admin

```bash
local_root=/home/ftpuser/admin/
write_enable=YES
anon_umask=022
anon_world_readable_only=NO
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
```
* pam.d/vsftpd 文件添加(注意32为系统要将/lib64 改为/lib)

```bash
auth       sufficient   /lib64/security/pam_userdb.so   db=/etc/vsftpd/vsftpd_login
account    sufficient   /lib64/security/pam_userdb.so   db=/etc/vsftpd/vsftpd_login
```
* 生成数据库文件

```bash
db_load -T -t hash -f /etc/vsftpd/ftpuser.txt /etc/vsftpd/vsftpd.db
```