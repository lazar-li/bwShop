**github搜索social django app:https://github.com/python-social-auth/social-app-django**

**文档:https://python-social-auth.readthedocs.io/en/latest/**

**django版本文档:https://python-social-auth.readthedocs.io/en/latest/configuration/django.html**

**django版本GitHub:https://github.com/python-social-auth/social-app-django**

### 安装

`pip install social-auth-app-django`

## 配置

### settings配置

```python
INSTALLED_APPS = (
    ...
    'social_django',
    ...
)

AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',
    'social_core.backends.weibo.WeiboOAuth2',
    'social_core.backends.qq.QQOAuth2',
    'social_core.backends.weixin.WeixinOAuth2',
    'django.contrib.auth.backends.ModelBackend',
)
TEMPLATES = [
    {
        ...
        'OPTIONS': {
            ...
            'context_processors': [
                ...
                'social_django.context_processors.backends',
                'social_django.context_processors.login_redirect',
                ...
            ]
        }
    }
]
# 第三方登录配置
SOCIAL_AUTH_WEIBO_KEY = '3067025662'
SOCIAL_AUTH_WEIBO_SECRET = '95f74303bf95323494e8efd5f145bfdc'

# 腾讯开放平台获取
SOCIAL_AUTH_QQ_KEY = 'foobar'
SOCIAL_AUTH_QQ_SECRET = 'bazqux'

# 微信开放平台获取
SOCIAL_AUTH_WEIXIN_KEY = 'foobar'
SOCIAL_AUTH_WEIXIN_SECRET = 'bazqux'

SOCIAL_AUTH_LOGIN_REDIRECT_URL = '/index/'
```

**执行migrate生成数据表,由于源码已经将migration文件生成好了,所以不用执行makemigrations**

### url配置

```python
# jwt的认证接口  由于jwt的url路径和第三方登录的url路径有冲突,所以要在^login/后加上结尾符合$
url(r'^login/$', obtain_jwt_token),
# 第三方登录url
url('', include('social_django.urls', namespace='social'))
```

### 将python包.site-packages.social_core复制到项目的extra_apps中

### 修改social_core.actions.do_complete的最后一行

```python
from rest_framework_jwt.serializers import jwt_encode_handler, jwt_payload_handler

response = backend.strategy.redirect(url)
payload = jwt_payload_handler(user)
response.set_cookie("name", user.name if user.name else user.username, max_age=24*3600)
response.set_cookie("token", jwt_encode_handler(payload), max_age=24*3600)
return response
```

**实现将token和用户名添加到cookie中**

