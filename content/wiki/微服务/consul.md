#### 常用的服务发现
consul：常用于go-micro
mdns: go-micro 默认自带的服务发现
etcd: k8s内嵌的服务发现
zookeeper: java中比较常用

##### 简介
consul通过dns或者http接口使服务注册和服务发现变得很容易，一些外部服务，例如：saas提供的也可以一样注册

##### Consul：
服务发现：consul通过dns或者http接口使服务注册和服务发现变得很容易，一些外部服务，例如：saas提供的也可以一样注册
健康检查：健康检测使consul可以快速的告警在集群中的操作。和服务发现的集成，可以防止服务转发到故障服务上面。（监测机制）
键/值存储：一些配置等
多数据中心：无需复杂的配置，即可支持任意数量的区域（最好是三台或者三台以上的consul在运行）

##### 常用命令
consul agent
- -bind=0.0.0.0 指定consul 所在的机器IP地址。默认0.0.0.0
- -http-port  consul 自带的web访问的默认端口：8500
- -client=127.0.0.1 表明哪些机器可以访问consul。默认本机。0.0.0.0所有机器均可访问
- -config-dif=foo 所有主动注册服务的 描述信息
- -data-dir=path 存储所有注册过来的server机器的详细信息。
- -dev 开发者模式-node=hostname 服务发现的名字
- -rejoin consul 启动的时候，加入到consul集群
- -server 以服务方式开启consul,允许其他的consul连接到开启的consul上（形成集群）。如果不加-servr,表示以”客户端“的方式开启。不能被链接。
- -ui 可以使用web界面来查看服务发现的详情

consul members：查看集群中有多少个成员

consul info: 查看当前consul他的ip等信息

consul leave： 优雅的关闭 consul。

###### 测试
```shell
consul agent -server -bootstrap-expect 1 -data-dir /tmp/consul -node=n1 -bind=0.0.0.0 -ui -rejoin -config-dir=/etc/consul.d/ -client 0.0.0.0
```

#### 注册服务到consul步骤：
    1. 进入配置文件 /etc/consul.d/web.json
    2. 创建web.json文件
    3. 按json的语法，填写服务信息
    4. 重新启动consul consul agent -server -bootstrap-expect 1 -data-dir /tmp/consul -node=n1 -bind=0.0.0.0 -ui -rejoin -config-dir=/etc/consul.d/ -client 0.0.0.0
    5. 查看服务
        1. 浏览器查看
        2. curl -s 127.0.0.1:8500/v1/catalog/service/{servername}

#### consul心跳检测

配置文件编写web.json(需重启)
```
{
    "service":{
            "name": "sunkang",
            "tags": ["rails"],
            "port": 9000,
            "check": {
                    "id": "api",
                    "name": "sunkang check",
                    "http": "http://localhost:9000",
                    "interval": "10s",
                    "timeout": "1s"
            }
    }
}

```
使用浏览器查看服务的健康状态
除了http实现健康检查外，还可以使用“脚本”、“tcp”、“ttl”方式进行健康检查。 