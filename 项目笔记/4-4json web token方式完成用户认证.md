## 配置jwt

```python
# 安装jwt
pip install djangorestframework-jwt
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    )
}
# urls.py
from rest_framework_jwt.views import obtain_jwt_token
#...

urlpatterns = [
    '',
    # ...

    url(r'^api-token-auth/', obtain_jwt_token),
]
```

### 使用jwt发送post请求,并携带用户名和密码

![image-20191226170219069](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191226170219069.png)

### jwt认证

**headers中添加**

Authorization: JWT <your_token>

