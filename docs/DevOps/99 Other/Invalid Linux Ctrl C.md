# Linux Ctrl C无效
```bash
原因：rvm 版本bug
解决方法：
命令查看：
    正常：
            [root@server002 ~]# trap
            trap -- '' SIGTSTP
            trap -- '' SIGTTIN
            trap -- '' SIGTTOU
    异常：
            [root@server002 ~]# trap
            trap -- '' SIGTSTP
            trap -- '' SIGTTIN
            trap -- '' SIGTTOU
            trap -- '' SIGINT
            trap -- '' SIGQUIT
       现象：终端Ctrl + C完全失效，当执行trap 信号命令时多处两个SIGINT和SIGQUIT两项
升级rvm 版本：rvm get stable（
1.29.4 
版本以上都可以解决）
卸载rvm工具：gem uninstall rvm
```