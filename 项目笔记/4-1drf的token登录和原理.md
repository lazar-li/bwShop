**前后端分离用户认证**

**https://www.django-rest-framework.org/api-guide/authentication/**

## Token认证

### 使用rest_framework.authentication前的配置

```python
# settings.py
INSTALLED_APPS = [
    ......
    'rest_framework.authtoken',
    ......
]
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ]
}
# 执行命令
python manage.py makemigrations
python manage.py migrate
# 会生成一张表authtoken_usltoken表
# urls.py
from rest_framework.authtoken import views
urlpatterns = [
    ......
    url(r'^api-token-auth/', views.obtain_auth_token),
    ......
]
# 创建用户不会生成token需要自己创建
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=...)
print(token.key)  # 这样就可以获取key了

```

### 模拟用户登录

**使用postman发送post请求**

![image-20191226143857008](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191226143857008.png)

```json
{
    "token": "3bb4d59c8c1665d9e8db6bd564bbe5ae8fac56dd"
}
```

#### 为了使客户端进行身份验证，令牌密钥应包含在AuthorizationHTTP标头中。密钥应以字符串文字“ Token”作为前缀，并用空格分隔两个字符串。例如：

#### Authorization是header的key,token 是header的值

**Authorization**: Token 3bb4d59c8c1665d9e8db6bd564bbe5ae8fac56dd

![image-20191226152203397](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191226152203397.png)

​	![image-20191226152444674](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191226152444674.png)

### 原理和django的MIDDLEWARE(settings.py)类似

![image-20191226152854147](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191226152854147.png)

**Django从请求到返回都经历了什么**

https://blog.csdn.net/zelinhehe/article/details/80366105

