### 1.安装drf第三方库[DRF-extensions](http://chibisov.github.io/drf-extensions/docs/#drf-extensions)

` pip3 install drf-extensions`

or

`pip3 install https://github.com/chibisov/drf-extensions/archive/master.zip`

### 2.导入缓存库

```python
from rest_framework_extensions.cache.mixins import CacheResponseMixin
# 哪里用,在哪里继承就行了
class GoodsListViewSet(CacheResponseMixin, mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):

```

### 配置settings

```python
# drf缓存过期时间
REST_FRAMEWORK_EXTENSIONS = {
    'DEFAULT_CACHE_RESPONSE_TIMEOUT': 60 * 15
}
```