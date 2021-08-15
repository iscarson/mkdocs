# goaccess
```bash
sed -n '/2019-12-18T07:30:00+08:00/,/2019-12-18T08:30:00+08:00/p' nginx.access.main.20191218.log >>nginx01.log


goaccess -f {nginx3.log,nginx1.log,nginx2.log,nginx4.log} -a -d -p /usr/local/etc/goaccess.conf -o nginx0730-0830.html &


goaccess -f nginx3.log -a -d -p /root/goaccess.conf -o nginx.html &
```