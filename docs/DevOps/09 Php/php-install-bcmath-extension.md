```
#!/bin/bash

bcmath_ini=/etc/php.d/40-bcmath.ini

## 安装bcmath扩展
cd /data/softs
sudo tar zxvf php-5.6.30.tar.gz
cd /data/softs/php-5.6.30/ext/bcmath/
sudo phpize
sudo ./configure
make &&make install


## 增加扩展配置
cat > $bcmath_ini <<EOF
; Enable bcmath extension module
extension = bcmath.so
EOF


echo "bcmath安装完成......"
```