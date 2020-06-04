### 配置settings.REST_FRAMEWORK

```python
# 限速类
    'DEFAULT_THROTTLE_CLASSES': [
        # 用户没登录时的请求限速  通过ip地址判断
        'rest_framework.throttling.AnonRateThrottle',
        # 用户登录后的请求限速  通过token判断
        'rest_framework.throttling.UserRateThrottle'
    ],
    # 限速规则
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
```

**drf文档:https://www.django-rest-framework.org/api-guide/throttling/**

### 哪个接口需要进行限速,配置哪个接口的viewset

```python
from rest_framework.throttling import UserRateThrottle, AnonRateThrottle
	# 在viewset中添加
    throttle_classes = [UserRateThrottle, AnonRateThrottle]

```