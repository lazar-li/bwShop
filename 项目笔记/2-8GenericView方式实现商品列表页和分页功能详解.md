## goods.views.py

```python
from .serializers import GoodsSerializer
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import mixins
from rest_framework import generics

from .models import Goods

# Create your views here.



class GoodsListView(mixins.ListModelMixin, generics.GenericAPIView):
    """
    商品列表页
    """
    # 变量名是固定的
    queryset = Goods.objects.all()[:10]
    serializer_class = GoodsSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)


# 代码减少功能不变
class GoodsListView(generics.ListAPIView):
    """
    商品列表页
    """
    # 变量名是固定的
    queryset = Goods.objects.all()[:10]
    serializer_class = GoodsSerializer


# ListAPIView源码
class ListAPIView(mixins.ListModelMixin,
                  GenericAPIView):
    """
    Concrete view for listing a queryset.
    """
    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)
```

## 分页功能实现

### 第一种方法

```python
# settings.py
REST_FRAMEWORK = {
    # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

### 第二种方法

```python
# views.py
from rest_framework.pagination import PageNumberPagination

class GoodsPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'
    page_query_param = 'p'
    max_page_size = 100
```