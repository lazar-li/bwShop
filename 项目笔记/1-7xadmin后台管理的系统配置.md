### 安装xadmin

```
pip install https://codeload.github.com/sshwsfc/xadmin/zip/django2
```

### 将源码中每个app下的adminx.py复制过来

### 将xadmin配置到settings中

```python
INSTALLED_APPS = [
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'apps.users.apps.UsersConfig',
    'goods',
    'trade',
    'user_operation',
    'DjangoUeditor',
    'crispy_forms',
    'xadmin',
]
```

### 安装xadmin依赖包

|      | django>=1.9.0               |
| ---- | --------------------------- |
|      | django-crispy-forms>=1.6.0  |
|      | django-import-export>=0.5.1 |
|      | django-reversion>=2.0.0     |
|      | django-formtools==1.0       |
|      | future==0.15.2              |
|      | httplib2==0.9.2             |
|      | six==1.10.0                 |

```
pip install django-crispy-forms django-import-export django-reversion django-formtools future httplib2 six

pip install xlwt xlsxwriter
```

### 配置MxShop.urls

```python
import xadmin
urlpatterns = [    
	path('xadmin/', xadmin.site.urls),
]
```

### 配置settings中的时区和中文支持

```python
LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'
```



### 创建supperuser

```python
python manage.py createsuperuser
```

