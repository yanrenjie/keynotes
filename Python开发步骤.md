# 使用Python写一个Blog系统的步骤

### 初始化项目
- 现在GitHub上创建一个远程仓库TestBlog，然后通过终端把项目clone到本地，在桌面TestBlog；
- 通过终端cd 到TestBlog目录下，django-admin startproject testblog，来初始化一个工程；
- cd testblog中，通过python3 manage.py runserver 0.0.0.0:8000，运行项目，先运行起来；

### 创建数据库blog_db;
- 终端中通过mysql -u root -p进入mysql交互环境；
- 创建数据库： create database blog_db charset utf8;
- 创建mysql用户： create user jackey identified by 'yanrenhao';
- 授权jackey 用户访问blog_db数据库：grant all on blog_db.* to 'jackey'@'%';
- 刷新权限：flush privileges;

### 配置数据库文件
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'blog_db',
        'HOST' : '127.0.0.1',
        'PORT' : 3306,
        'USER' : 'jackey',
        'PASSWORD' : 'yanrenhao'
    }
}
```

### __init__.py 中修改
```python
import pymysql

pymysql.install_as_MySQLdb()
```

### 安装django-redis
终端执行pip3 install django-redis

### 配置django-redis
```python
# Django的缓存配置
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis:/127.0.0.1:6379/0",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    },
    "session": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis:/127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}

# session 由数据库存储改为redis存储
SESSION_ENGINE = "django.contrib.sessions.backends.cache"
SESSION_CACHE_ALIAS = "session"
```

### 日志的简单实用
```python
# 1、导入系统的logging
import logging
# 2、创建日志器
logger = logging.getLogger('django')


from django.http import HttpResponse

def log(request):
    # 3、使用日志器记录信息
    logger.info('这个是我记录的日志信息呀')
    return HttpResponse("test")
```

```python
输出结果，urls 这个文件，第29行，输出了“这个是我记录的日志信息呀”这段文字，然后在blog.log中也有记录
INFO urls 29 这个是我记录的日志信息呀
```

