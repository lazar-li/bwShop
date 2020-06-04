**github搜索social django app:https://github.com/python-social-auth/social-app-django**

**文档:https://python-social-auth.readthedocs.io/en/latest/intro.html**

**django版本:https://github.com/python-social-auth/social-app-django**

### 安装social-auth-app-django

`pip install social-auth-app-django`

### 配置

https://python-social-auth.readthedocs.io/en/latest/configuration/django.html

#### settings中配置

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
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                ....
                'social_django.context_processors.backends',
                'social_django.context_processors.login_redirect',
            ],
        },
    },
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
```

#### migrate生成数据表,因为安装好的模块自带了migration文件,不需要执行makemigrations

#### url配置

```python
# jwt的认证接口  由于和第三方登录中的url路径冲突,需要在^login/后加上$结尾符
url(r'^login/$', obtain_jwt_token),
# 第三方登录url
url('', include('social_django.urls', namespace='social'))
```

**将源码中site-packages.social_core复制到extra_apps中,修改actions中的do_complete最后一行实现回调时将token添加到cookie中**

```python

    response = backend.strategy.redirect(url)
    payload = jwt_payload_handler(user)
    response.set_cookie("name", user.name if user.name else user.username, max_age=24*3600)
    response.set_cookie("token", jwt_encode_handler(payload))
    return response
```