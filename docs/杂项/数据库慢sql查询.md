# 数据库慢sql排查
```mysql
select id,user,host,db,command,time,state,info from information_schema.PROCESSLIST order by time desc
```

