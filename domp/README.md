DOMP（Docker + Openresty + MySQL8.x + PHP7.3.4 + Redis）是一款全功能的**LNMP一键安装程序**。


## 1.目录结构

```
/
├── docker                                      挂载目录和文件，推荐这个目录放到/data目录下，目录结构/data/docker
│   ├── mysql                                   mysql配置目录及数据库目录
│   │   ├── mysql                               mysql数据库目录，注意权限要有写权限
│   │   ├── mysql.cnf                           mysql配置文件
│   ├── openresty                               Nginx默认配置目录
│   ├── php                                     php配置文件目录
│   │   ├── php-fpm.conf                        FPM配置文件（部分会覆盖php.ini配置）
│   │   ├── php.ini                             PHP默认配置文件
│   │   ├── php.d                               php的扩展文件，加扩展是没法加，但可以关闭和禁用一些扩展，或者对现有扩展调优
│   │   │   ├── php.d                           php的扩展文件，加扩展是没法加，但可以关闭和禁用一些扩展，或者对现有扩展调优
│   │   │   │   ├── docker-php-ext-xdebug.ini   一个覆盖掉docker镜像中xdebug插件配置的例子
│   │   └── php-fpm.d                           fpm的虚主机配置文件，里面有3个从docker容器中复制出来的配置文件，除非调优否则不用修改
│   ├── redis                                   redis配置文件
│   ├── var                                     存放日志和pid文件
│   │   ├── log                                 存放日志
│   │   │   ├── openresty                       存放openresty日志
│   │   │   ├── php                             存放php日志
│   │   ├── run                                 存放pid文件
├── Dockerfile                                  PHP镜像构建文件
├── docker-compose.yml                          docker-compose配置文件
├── .env.sample                                 docker环境变量配置文件范例，需要复制成.env文件
```

## 2.快速使用
1. 切换到阿里云镜像源
编辑/etc/docker/daemon.json 文件内容如下
{
  "registry-mirrors": ["https://sle32q6u.mirror.aliyuncs.com"]
}
$ systemctl daemon-reload
$ systemctl restart docker

1. 本地安装`git`、`docker`和`docker-compose`。
2. `clone`项目：
    ```
    $ git clone https://github.com/yeszao/dnmp.git
    ```
3. 如果不是`root`用户，还需将当前用户加入`docker`用户组：
    ```
    $ sudo gpasswd -a ${USER} docker
    ```
4. 拷贝环境配置文件`env.sample`为`.env`

5. 把docker目录复制到/data目录下，目录结构为 /data/docker

6. 启动：
    ```
    $ cd domp
    $ cp env.sample .env   # Windows系统请用copy命令，或者用编辑器打开后另存为.env
    $ docker-compose up    # 启动/安装容器，如果后台执行可以加参数 -d
    ```
7. 注意事项

Windows安装360安全卫士的同学，请先将其退出，不然安装过程中可能Docker创建账号过程可能被拦截，导致启动时文件共享失败；

在下载镜像时可能会出现I/O error，然后会中止构建，出现这个问题只要多重试几次就好了

一般出现exit with code 0 比较多的可能是权限不足，请检查php，openresty，redis和mysql的配置文件特别是日志和pid输出目录是否存在是否有权限


```bash
$ docker-compose build php54    # 重建单个服务
$ docker-compose build          # 重建全部服务

```

## 3.容器的内部网络通讯
你的电脑是host，docker容器是guest，guest与host会组建一个子网，172.255.0.x
```
host：172.255.0.1
openresty：172.252.0.2
myql：172.252.0.3
redis：172.252.0.4
fpm：172.252.0.5
在guest内部可以不用ip访问，通过fpm，redis，openresty，mysql这样的别名访问就可以访问别的guest（host除外）
在guest里访问host必须用172.255.0.1来访问
```

## 4.ssh隧道访问云端数据库
一般开发期间访问数据库需要配置ssh隧道把远程服务器的数据库端口映射到本地，注意映射端口时不要映射到127.0.0.1，而需要映射到0.0.0.0，否则guest无法访问host的映射端口！
```bash
/usr/bin/ssh www@<服务器IP> -p 22 -L0.0.0.0:33201:localhost:3306 -L0.0.0.0:2378:localhost:2379
这是将服务器的3306映射到本地的33201端口，服务器的2379映射到本地的2378端口
```
如果是生产环境就不用这么麻烦了，可以直接放开服务器内网端口，也可以配置iptables实现端口转发实现guest访问host端口

## 5.常用的docker操作

### 1.调用guest里的命令（容器必须开启）
docker exec <容器名，mysql/openresty/fpm/redis> 命令 参数

nginx reload的命令
```bash
$ docker exec openresty nginx -s reload
```

### 

## 6.容器的注意事项

### 容器内部的修改除非执行commit，否则是不保存的
### 容器的网络通讯（bridge模式），性能要比原生网络访问慢10%
### fpm关闭部分php扩展
    通过映射一个空文件到容器里来实现
```
        volumes:
            - /dev/null:/usr/local/etc/php/conf.d/docker-php-ext-opcache.ini:ro
```

### 添加快捷命令
在开发的时候，我们可能经常使用`docker exec -it`切换到容器中，把常用的做成命令别名是个省事的方法。

打开~/.bashrc，加上：
```bash
alias docker_nginx='docker exec openresty /usr/local/openresty/nginx/sbin/nginx'
alias docker_php='docker exec fpm /usr/local/bin/php'
配置完成后可以执行
docker_php -i 执行fpm容器中的php，像是在phpstorm中也可以这样用
其余的自己看着加就好了

```

## 7.在正式环境中安全使用
要在正式环境中使用，请：
1. 在php.ini中关闭XDebug扩展，并开启opcache扩展
2. 增强MySQL数据库访问的安全策略
3. 增强redis访问的安全策略

## 8.如果要装新的php扩展怎么办？
只能修改Dockerfile，然后在docker-composer.yml的fpm部分注释掉image，并添加
```
        build:
          context: .
```
然后重新编译

