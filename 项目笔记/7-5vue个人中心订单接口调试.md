```javascript
//获取订单
export const getOrders = () => { return axios.get(`${local_host}/orders/`) }
//删除订单
export const delOrder = orderId => { return axios.delete(`${local_host}/orders/`+orderId+'/') }
//添加订单
export const createOrder = params => {return axios.post(`${local_host}/orders/`, params)}
//获取订单详情
export const getOrderDetail = orderId => {return axios.get(`${local_host}/orders/`+orderId+'/')}
```

### 实现订单页面详情页的序列化

```python
# trade.serializers
class OrderGoodsSerializer(serializers.ModelSerializer):
    goods = GoodsSerializer(many=False)

    class Meta:
        model = OrderGoods
        fields = "__all__"


class OrderDetailSerializer(serializers.ModelSerializer):
    goods = OrderGoodsSerializer(many=True)

    class Meta:
        model = OrderInfo
        fields = "__all__"
```

```python
# 修改viewset实现动态化serializer,继承mixins.RetrieveModelMixin
class OrderViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin, mixins.CreateModelMixin, mixins.DestroyModelMixin, viewsets.GenericViewSet):
    """
    订单管理
    list:
        获取个人订单
    delete:
        删除订单
    create:
        新增订单
    """
    serializer_class = OrderSerializer
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)

    # 只返回当前用户的queryset
    def get_queryset(self):
        return OrderInfo.objects.filter(user=self.request.user)

    def get_serializer_class(self):
        if self.action == "retrieve":
            return OrderDetailSerializer
        return OrderSerializer

    def perform_create(self, serializer):
        order = serializer.save()
        shop_carts = ShoppingCart.objects.filter(user=self.request.user)
        for shop_cart in shop_carts:
            order_goods = OrderGoods()
            order_goods.goods = shop_cart.goods
            order_goods.goods_num = shop_cart.nums
            order_goods.order = order
            order_goods.save()

            shop_cart.delete()
        return order
```