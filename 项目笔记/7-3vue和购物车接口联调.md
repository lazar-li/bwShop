**由于购物车页面要展示商品信息,所以我们只返回goods_id就不能满足了,这是我们需要新定义一个serializers并实现动态化**

### serializers

```python
from goods.serializers import GoodsSerializer


class ShopCarDetailSerializer(serializers.ModelSerializer):
    goods = GoodsSerializer(many=False)

    class Meta:
        model = ShoppingCart
        fields = "__all__"

```

### views.ShoppingCartViewSet

```python
    def get_serializer_class(self):
        if self.action == "list":
            return ShopCarDetailSerializer
        else:
            return ShopCarSerializer
```

### api.js

```javascript
//获取购物车商品
export const getShopCarts = params => { return axios.get(`${local_host}/shopcarts/`) }
// 添加商品到购物车
export const addShopCart = params => { return axios.post(`${local_host}/shopcarts/`, params) }
//更新购物车商品信息
export const updateShopCart = (goodsId, params) => { return axios.patch(`${local_host}/shopcarts/`+goodsId+'/', params) }
//删除某个商品的购物记录
export const deleteShopCart = goodsId => { return axios.delete(`${local_host}/shopcarts/`+goodsId+'/') }

```