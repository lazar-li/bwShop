## goods.filters.py

```python
import django_filters
from .models import Goods
from django.db.models import Q


class GoodsFilter(django_filters.rest_framework.FilterSet):
    """
    商品的过滤类
    """
    pricemin = django_filters.NumberFilter(field_name='shop_price', lookup_expr='gte')
    pricemax = django_filters.NumberFilter(field_name='shop_price', lookup_expr='lte')
    top_category = django_filters.NumberFilter(method='top_category_filter')

    def top_category_filter(self, queryset, name, value):
        return queryset.filter(Q(category_id=value) |
                               Q(category__parent_category_id=value) |
                               Q(category__parent_category__parent_category_id=value))

    class Meta:
        model = Goods
        fields = ['pricemin', 'pricemax']
```

### 修改goods.views.GoodsListViewSet

```python
ordering_fields = ['sold_num', 'shop_price']
```

### 修改前端 src.api.api.js

```javascript
//获取商品列表
export const getGoods = params => { return axios.get(`${local_host }/goods/`, { params: params }) }
```

