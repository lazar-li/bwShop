### 解决用户取消收藏的权限问题

**用户在取消收藏时,有可能其他用户也收藏了那个商品,就可能把其他用户的收藏给删除掉,或者,用户在获取收藏列表时,获取的不是自己的而是所有的收藏列表**

### 1.首先验证用户是否登录

```python
# user_operation.views
from rest_framework.permissions import IsAuthenticated

# user_operation.views.UserFavViewSet
permission_classes = (IsAuthenticated, )
```

### 2.验证用户是否是收藏信息的用户,并且验证用户发送的请求是否是安全的

**在utils下新建permissions.py**

```python
from rest_framework import permissions


class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    Object-level permission to only allow owners of an object to edit it.
    Assumes the model instance has an `owner` attribute.
    """

    def has_object_permission(self, request, view, obj):
        # Read permissions are allowed to any request,
        # so we'll always allow GET, HEAD or OPTIONS requests.
        if request.method in permissions.SAFE_METHODS:
            return True

        # Instance must have an attribute named `owner`.
        return obj.user == request.user

```

**在views中导入**

```python
from utils.permissions import IsOwnerOrReadOnly

class UserFavViewSet(viewsets.GenericViewSet, mixins.ListModelMixin, mixins.DestroyModelMixin, mixins.CreateModelMixin):
    """
    用户收藏功能
    """
    queryset = UserFav.objects.all()
    serializer_class = UserFavSerializer
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
```

### 3.只获取用户下的收藏

```python
class UserFavViewSet(viewsets.GenericViewSet, mixins.ListModelMixin, mixins.DestroyModelMixin, mixins.CreateModelMixin):
    """
    用户收藏功能
    """
    # queryset = UserFav.objects.all()
    serializer_class = UserFavSerializer
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
	
    # 重载获取queryset方法
    def get_queryset(self):
        return UserFav.objects.filter(user=self.request.user)
```



### 修改jwt验证配置,将全局配置转为views内配置

**注释掉settings中的jwt配置**

```python
# user_operation.views
from rest_framework_jwt.authentication import JSONWebTokenAuthentication


class UserFavViewSet(viewsets.GenericViewSet, mixins.ListModelMixin, mixins.DestroyModelMixin, mixins.CreateModelMixin):
    """
    用户收藏功能
    """
    # queryset = UserFav.objects.all()
    serializer_class = UserFavSerializer
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    # jwt验证
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)

    def get_queryset(self):
        return UserFav.objects.filter(user=self.request.user)
```