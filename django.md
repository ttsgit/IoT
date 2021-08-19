
# 参考
https://stackabuse.com/creating-a-rest-api-in-python-with-django#:~:text=Django%20is%20a%20powerful%20Python%20Web%20Framework%20used,RESTful%20API%20using%20Django%20without%20any%20external%20libraries.


https://docs.djangoproject.com/en/3.2/intro

# install
```
$ python3 -m venv env
$ sudo source env/bin/activate
$ pip install django
$ django-admin startproject iot_server
```

```
$ cd iot_server
$ python manage.py startapp api_app
```
once created, our project structure will look something along the lines of:
```
> env
> iot_server
  > api_app
    > migrations
    __init__.py
    admin.py
    apps.py
    models.py
    tests.py
    views.py
  > iot_server
    __init__.py
    asgi.py
    settings.py
    urls.py
    wsgi.py
  manage.py
```

The top-level iot_server is the Django root directory and shares the name with the project name. The root directory is the bridge between the framework and the project itself and contains several setup classes such as manage.py which is used to spin up applications.


# settings.py文件修改
默认用的是sqlite，这边换成postgresql的话，另外安装psycopg2
```
pip install psycopg2-binary

pip install mysqlclient # mysql
```
修改数据库连接参数
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'iot',
        'USER': 'postgres',
        'PASSWORD': 't0742812324',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
```
修改语言和时区
```
LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'
```
The api_app is the application we're spinning up, and there can be many here. Each has a few default scripts that we'll be modifying to accomodate for the CRUD functionality.

Let's list it by going to the settings.py file and modifying the list to include our own app:

```
INSTALLED_APPS = [
    'django.contrib.admin',he sqlmigrate command takes migration names and returns their SQL:
```

# models.py 文件新增可以新建数表
```
from django.db import models


class IotData(models.Model):
    name = models.CharField(max_length=200)
    value = models.FloatField()
```

Some of these applications make use of at least one database table, though, so we need to create the tables in the database before we can use them. To do that, run the following command:
```
$ python manage.py makemigrations  # Pack model changes into a file
$ python manage.py sqlmigrate api_app 0001 #the sqlmigrate command takes migration names and returns their SQL
$ python manage.py check #this checks for any problems in your project without making migrations or touching the database.
$ python manage.py migrate  # Apply those changes to the database
```
The migrate command looks at the INSTALLED_APPS setting and creates any necessary database tables according to the database settings in your mysite/settings.py file and the database migrations shipped with the app (we’ll cover those later). You’ll see a message for each migration it applies. If you’re interested, run the command-line client for your database and type \dt (PostgreSQL), SHOW TABLES; (MariaDB, MySQL), .schema (SQLite), or SELECT TABLE_NAME FROM USER_TABLES; (Oracle) to display the tables Django created.


# admin.py 管理页面
The Django Admin Site

When creating applications, Django automatically creates an admin-site, intended to be used by the developer to test things out, and give them access to forms generated for registered models. It's only meant to be used as a handy dashboard during development - not as the actual administration dashboard, which you'd create separately if you want to have one.

```
from django.contrib import admin
from .models import IotData

admin.site.register(IotData)
```

Let's create a superadmin account and confirm that all these changes were made successfully:

```
$ python manage.py createsuperuser
```

# REST API

## 简介
REST(英文：Representational State Transfer，简称REST，意思：表述性状态转换，描述了一个架构样式的网络系统，比如web应用)。

它是一种软件架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件，它主要用于客户端和服务端交互类的软件。基于这个风格设计的软件可以更简介，更有层次，更易于实现缓存等机制。

它本身并没有什么使用性，其核心价值在于如何设计出符合REST风格的网络接口。

GET、POST、PUT、DELETE。他们分别对应四种基本操作：GET用来获取资源，POST用来新建资源，PUT用来更新资源，DELETE用来删除资源。

路径设计：数据库设计完毕之后，基本上就可以确定有哪些资源要进行操作，相对应的路径也可以设计出来。

动词设计：也就是针对资源的具体操作类型，有HTTP动词表示，常用的HTTP动词如下：POST、DELETE、PUT、GET
RESTful示例

   1. /account/1 HTTP GET：得到id=1的account
   2. /account/1 HTTP POST:新建id=1的account
   3. /account/1 HTTP DELETE：删除id=1的account
   4. /account/1 HTTP PUT：更新id=1的account


iot数据采集项目 主要由vue前端项目通过axios get后端django的api数据呈现

##  djangorestframework
$ pip install djangorestframework


在setting.py中加入
```
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework', # add this line
    'api_app',
]
```

## views.py
```
from django.shortcuts import render
 
# Create your views here.
 
from django.http import HttpResponse
from django.shortcuts import get_object_or_404
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import IotData
from .serializers import dataSerializer
 

# def index(request):
#     return HttpResponse("Hello, world. You are at the api index.")
 
class index(APIView):

    def get(self,request):
        data = IotData.objects.all()
        serializer = dataSerializer(data, many= True)
        return Response(serializer.data) # Return JSON
 
    def post(self):
        pass
```

## app中urls.py 

```
from django.urls import path
from . import views

urlpatterns = [
    path('',views.index.as_view(), name='index'),
]
```

## 项目urls.py

```
from django.contrib import admin
from django.urls import path
from django.urls.conf import include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/',include('api_app.urls')),
]
```

# 跨域请求问题

$ pip install django-cors-headers

setting.py 配置
```
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework', # add this line
    'api_app', # add this line
    'corsheaders', # add this line
]

# 注意：要放在中间件（common.CommonMiddleware）的上面，涉及到请求响应的顺序问题

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    # 跨域请求中间件
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

#在最后添加如下代码

# 跨域允许的请求方式，可以使用默认值，默认的请求方式为:
# from corsheaders.defaults import default_methods
CORS_ALLOW_METHODS = (
    'GET',
    'POST',
    'PUT',
    'PATCH',
    'DELETE',
    'OPTIONS'
)

# 允许跨域的请求头，可以使用默认值，默认的请求头为:
# from corsheaders.defaults import default_headers
# CORS_ALLOW_HEADERS = default_headers

CORS_ALLOW_HEADERS = (
    'XMLHttpRequest',
    'X_FILENAME',
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
    'Pragma',
)

# 跨域请求时，是否运行携带cookie，默认为False
CORS_ALLOW_CREDENTIALS = True
# 允许所有主机执行跨站点请求，默认为False
# 如果没设置该参数，则必须设置白名单，运行部分白名单的主机才能执行跨站点请求
CORS_ORIGIN_ALLOW_ALL = True
```