
# harbor安装

## docker-compose install
```bash
yum -y install docker-compose
docker-compose --version
```
## 下载镜像
```bash
wget https://github.com/goharbor/harbor/releases/download/v1.10.3-rc2/harbor-offline-installer-v1.10.3-rc2.tgz
```
## 配置文件
```bash
vim harbor.yml
    hostname: hub.liangml.com
    https:
      # https port for harbor, default is 443
      port: 443
      # The path of cert and key files for nginx
      certificate: /data/cert/server.crt
      private_key: /data/cert/server.key
```
## 证书创建
```bash
[root@vpc harbor]# openssl genrsa -des3 -out server.key 2048
Generating RSA private key, 2048 bit long modulus
...................................+++
...+++
e is 65537 (0x10001)
Enter pass phrase for server.key: 输入密码
Verifying - Enter pass phrase for server.key:

===========================

[root@vpc harbor]# openssl req -new -key server.key -out server.csr
Enter pass phrase for server.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:Shanghai
Locality Name (eg, city) [Default City]:Shanghai
Organization Name (eg, company) [Default Company Ltd]:liangml
Organizational Unit Name (eg, section) []:liangml
Common Name (eg, your name or your server's hostname) []:hub.liangml.com
Email Address []:liangml0528@163.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:liangml
An optional company name []:liangml

=======
cp server.key server.key.org(备份)

=======
[root@vpc harbor]# openssl rsa -in server.key.org -out server.key (去掉密码)
Enter pass phrase for server.key.org:
writing RSA key

=======
[root@vpc harbor]# openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt (签名)
Signature ok
subject=/C=CN/ST=Shanghai/L=Shanghai/O=liangml/OU=liangml/CN=hub.liangml.com/emailAddress=liangml0528@163.com
Getting Private key

======
./install.sh 安装

======
echo "172.19.5.110 hub.liangml.com" >> /etc/hosts (识别域名)
```