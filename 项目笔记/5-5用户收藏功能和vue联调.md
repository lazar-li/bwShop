### 修改查找时用的id,默认是通过收藏的id查找,我们修改成商品id

```python
# user_operation.views.UserFavViewSet
lookup_field = "goods_id"
```

