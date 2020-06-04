**django-redis中文文档:https://django-redis-chs.readthedocs.io/zh_CN/latest/**

### 1.安装django-redis

`pip install django-redis`

### 2.配置settings

```python
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}
```

### 3.开启本地redis服务

`redis-server`

## centos7安装redis

直接yum 安装的redis 不是最新版本

```
yum install redis
```

如果要安装最新的redis，需要安装Remi的软件源，官网地址：http://rpms.famillecollet.com/

```
yum install -y http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
```

然后可以使用下面的命令安装最新版本的redis：

```
yum --enablerepo=remi install redis
```

安装完毕后，即可使用下面的命令启动redis服务

```
service redis start或者systemctl start redis
```

**后面的自己看网址去吧,懒得复制了:https://www.cnblogs.com/autohome7390/p/6433956.html**

