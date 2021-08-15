### 1. Ngx lua waf 说明

防止sql注入，本地包含，部分溢出，fuzzing测试，xss,SSRF等web攻击
防止svn/备份之类文件泄漏
防止ApacheBench之类压力测试工具的攻击
屏蔽常见的扫描黑客工具，扫描器
屏蔽异常的网络请求
屏蔽图片附件类目录php执行权限
防止webshell上传

### 2. 下载 waf
使用git
> git clone https://github.com/loveshell/ngx_lua_waf.git

使用wget

> wget https://github.com/loveshell/ngx_lua_waf/archive/master.zip

### 3. 安装

```
下载解压后，将整 ngx_lua_waf 放到 nginx conf 目录中，并命名为 waf;
```

### 4. 配置

>nginx安装路径假设为:/usr/local/nginx/conf/  ；以下都将以此配置为例进行说明

#### 4.1. 在nginx.conf的http段添加

```
lua_package_path "/usr/local/nginx/conf/waf/?.lua";
lua_shared_dict limit 10m;
init_by_lua_file  /usr/local/nginx/conf/waf/init.lua;
access_by_lua_file /usr/local/nginx/conf/waf/waf.lua;
```

#### 4.2. 配置config.lua

```
--配置waf规则目录
RulePath = "/usr/local/nginx/conf/waf/wafconf/"

--修改失败提示
html=[[{"retcode":"21000","messages":"请求失败,请稍后再试","body":{}}]]

--开启cc攻击，需要在nginx.conf http 段配置lua_shared_dict
CCDeny="on"

-- 设置cc攻击频率，单位为秒 （如：同一个ip请求同一个地址，每秒最多请求50次）
CCrate = "50/1"
```

#### 4.3. 其他详细配置说明

```
    RulePath = "/usr/local/nginx/conf/waf/wafconf/"
    --规则存放目录

    attacklog = "off"
    --是否开启攻击信息记录，需要配置logdir

    logdir = "/usr/local/nginx/logs/hack/"
    --log存储目录，该目录需要用户自己新建，切需要nginx用户的可写权限

    UrlDeny="on"
    --是否拦截url访问

    Redirect="on"
    --是否拦截后重定向

    CookieMatch = "on"
    --是否拦截cookie攻击

    postMatch = "on"
    --是否拦截post攻击

    whiteModule = "on"
    --是否开启URL白名单

    black_fileExt={"php","jsp"}
    --填写不允许上传文件后缀类型

    ipWhitelist={"127.0.0.1"}
    --ip白名单，多个ip用逗号分隔

    ipBlocklist={"1.0.0.1"}
    --ip黑名单，多个ip用逗号分隔

    CCDeny="on"
    --是否开启拦截cc攻击(需要nginx.conf的http段增加lua_shared_dict limit 10m;)

    CCrate = "100/60"
    --设置cc攻击频率，单位为秒.
    --默认1分钟同一个IP只能请求同一个地址100次

    html=[[Please go away~~]]
    --警告内容,可在中括号内自定义
```

### 5 修改 waf/init.lua 文件

替换sys_html 函数：

```
function say_html()
    if Redirect then
        ngx.header.content_type = "text/html;charset=UTF-8"
        ngx.status = ngx.HTTP_FORBIDDEN
        ngx.say(html)
        ngx.exit(ngx.status)
    end
end
```

替换denycc 函数：

```
function denycc()
    if CCDeny then
        local uri=ngx.var.uri
        CCcount=tonumber(string.match(CCrate,'(.*)/'))
        CCseconds=tonumber(string.match(CCrate,'/(.*)'))
        local token = getClientIp()..uri
        local limit = ngx.shared.limit
        local req,_=limit:get(token)
        if req then
            if req > CCcount then
                ngx.header.content_type = "application/json;charset=UTF-8"
                local ret={returncode='22000',messages='请求拒绝,请稍后再试',body={}}
                --ngx.header['Content-Type']="text/html;charset=UTF-8"
                ngx.say(json.encode(ret))
                ngx.exit(200)
                return true
            else
                 limit:incr(token,1)
            end
        else
            limit:set(token,1,CCseconds)
        end
    end
    return false
end
```

### 6. 启用waf

```
然后重启nginx,或reload 即可:
/user/local/nginx/sbin/nginx -s reload
```

### 7. 测试

```
curl http://www.test.cn/test/index?id=../etc/passwd

返回：{"retcode":"21000","messages":"请求失败,请稍后再试","body":{}} 说明生效
```