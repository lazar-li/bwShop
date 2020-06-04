**Django-REST-framework---简称---DRF**

```
# 安装依赖包
pip install django-guardian 
# 安装drf文档管理包
pip install coreapi
```

```python
# 将rest_framework注册到settings
INSTALLED_APPS = [
	.......
    'rest_framework',
    .......
]
```

### views.py

```python
from .serializers import GoodsSerializer
from rest_framework.views import APIView
from rest_framework.response import Response

from .models import Goods

# Create your views here.


class GoodsListView(APIView):
    """
    List all snippets, or create a new snippet.
    """
    def get(self, request, format=None):
        goods = Goods.objects.all()[:10]
        goods_serializer = GoodsSerializer(goods, many=True)
        return Response(goods_serializer.data)

```

### serializers.py

```python
from rest_framework import serializers


class GoodsSerializer(serializers.Serializer):
    name = serializers.CharField(required=True, max_length=100)
    click_num = serializers.IntegerField(default=0)

```

### urls.py

```python
from django.conf.urls import url, include
import xadmin
from MxShop.settings import MEDIA_ROOT
from django.views.static import serve
from rest_framework.documentation import include_docs_urls

from goods.views import GoodsListView

urlpatterns = [
    url(r'^xadmin/', xadmin.site.urls),
    url(r'^api-auth/', include('rest_framework.urls')),
    url(r'^media/(?P<path>.*)$', serve, {"document_root": MEDIA_ROOT}),

    # 商品列表页
    url(r'goods/$', GoodsListView.as_view(), name="goods-list"),

    url(r'docs/', include_docs_urls(title="慕学生鲜")),

]

```