## views.py

**GoodsListView ==>GoodsListViewSet**

```python

class GoodsListViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    商品列表页
    """
    # 变量名是固定的
    queryset = Goods.objects.all()
    serializer_class = GoodsSerializer
    pagination_class = GoodsPagination
```

## urls.py

```python
from django.urls import path
from django.conf.urls import url, include
import xadmin
from MxShop.settings import MEDIA_ROOT
from django.views.static import serve
from rest_framework.documentation import include_docs_urls
from rest_framework.routers import DefaultRouter
from goods.views import GoodsListViewSet

router = DefaultRouter()

# 配置goods的url
router.register(r'goods', GoodsListViewSet)


urlpatterns = [
    url(r'^xadmin/', xadmin.site.urls),
    url(r'^api-auth/', include('rest_framework.urls')),
    url(r'^media/(?P<path>.*)$', serve, {"document_root": MEDIA_ROOT}),

    path('', include(router.urls)),
    url(r'docs/', include_docs_urls(title="慕学生鲜")),

]
```