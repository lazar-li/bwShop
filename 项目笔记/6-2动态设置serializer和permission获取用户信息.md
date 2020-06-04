**用户个人信息页需要展示用户的信息,所以需要对用户进行认证和权限验证**

```python
# users.views.UserViewSet
from rest_framework import permissions
from rest_framework import authentication
from rest_framework_jwt.authentication import JSONWebTokenAuthentication

permission_classes = (permissions.IsAuthenticated, )
# permissions虽然实现了用户权限验证,但是用户注册的时候是不能完成权限验证的,所以这样写不合理,
# 所以需要动态的对用户权限验证,及既能完成用户注册,又能满足对用户的权限验证,这里我们通过重写get_permissions来实现
    def get_permissions(self):
        # 判断用户的行为
        if self.action == "retrieve":
            return [permissions.IsAuthenticated()]
        elif self.action == "create":
            return []
        return []
# 还需要对用户进行认证
authentication_classes = (authentication.SessionAuthentication, JSONWebTokenAuthentication)

```

**用户详情的序列化需要新建一个serializers,但是UserViewSet还需要使用UserRegSerializer的序列化,在注册的时候又不能使用新建的serializers,所以需要动态的设置serializers**

```python
from .serializers import SmsSerializer, UserRegSerializer, UserDetailSerializer

# users.views.UserViewSet 重载获取serializers的函数,实现动态化 
    def get_serializer_class(self):
        if self.action == "retrieve":
            return UserDetailSerializer
        elif self.action == "create":
            return UserRegSerializer
        return UserRegSerializer
```