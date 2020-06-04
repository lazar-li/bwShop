### 修改接口url路径

```python
url(r'^jwt-auth/', obtain_jwt_token),
# 修改为
url(r'^login/', obtain_jwt_token),

```

## 自定义用户验证

```python
# users.views.py
from django.contrib.auth.backends import ModelBackend
from django.contrib.auth import get_user_model
from django.db.models import Q

User = get_user_model()


class CustomBackend(ModelBackend):
    """
    自定义用户验证
    """
    # pass

    def authenticate(self, request, username=None, password=None, **kwargs):
        try:
            user = User.objects.get(Q(username=username) | Q(mobile=username))
            if user.check_password(password):
                return user
        except Exception as e:
            return None

```

```python
# settings.py
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',
)
import datetime

JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=7),
    'JWT_AUTH_HEADER_PREFIX': 'JWT',
}
REST_FRAMEWORK = {
    # # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 'PAGE_SIZE': 10,
    # 'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend'],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        # 'rest_framework.authentication.TokenAuthentication',  # 全局token认证
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',  # jwt
    ]
}
```

**用户发送(post)请求时,settings会在url执行前调用,所以会先进入自定义验证,再进入jwt验证**

