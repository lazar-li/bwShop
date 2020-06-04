### goods.views.py

```python
class GoodsListViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    商品列表页, 分页, 搜索, 过滤, 排序
    """
    # 变量名是固定的
    queryset = Goods.objects.all()
    serializer_class = GoodsSerializer
    pagination_class = GoodsPagination
    filter_backends = [DjangoFilterBackend, filters.SearchFilter, filters.OrderingFilter]
    filter_class = GoodsFilter
    search_fields = ['name', 'goods_brief', 'goods_desc']
    ordering_fields = ['sold_num', 'shop_price']
```

