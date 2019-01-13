# redis安装

## 安装

``` shell
$wget http://download.redis.io/releases/redis-x.x.x.tar.gz
$tar xzf redis-x.x.x.tar.gz -C /opt/lib/redis
$cd /opt/lib/redis/redis-x.x.x
$make
```

## docker安装

### 获取镜像

`docker pull redis:$tag`，tag一般为版本号

### 运行容器

`docker run [--name $name] [-p $port:6379] [-v $path:/data] -d redis:$tag [redis-server --appendonly yes] `

参数说明:

* name: redis实例名称
* port: 将容器的`6379`端口映射到主机的`$port`端口
* path: 开启持久化`redis-server --appendonly yes`后持久化数据会存储到实例`/data`目录下，可以通过`-v $path:/data`挂载本机目录替换
* 示例: `docker run -p 6379:6379 -v /opt/data/redis_6379:/data --name redis_6379 -d redis:3.2.8 redis-server --appendonly yes`

另外，也可通过指定挂载配置文件来启动redis：

`docker run -v $path/redis.conf:/usr/local/etc/redis/redis.conf --name $name redis redis-server /usr/local/etc/redis/redis.conf`
