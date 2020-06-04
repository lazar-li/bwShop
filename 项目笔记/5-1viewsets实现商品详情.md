 ### 在goods.serializers下嵌套GoodsImageSerializer

```python
class GoodsImageSerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsImage
        fields = ("image", )
        
class GoodsSerializer(serializers.ModelSerializer):
    category = CategorySerializer()
    # 添加images字段
    images = GoodsImageSerializer(many=True)

    class Meta:
        model = Goods
        fields = "__all__"
```

### 在goods.views下的GoodsListViewSet中继承mixins.RetrieveModelMixin

### 实现:

![image-20191230092430638](C:\Users\fjllo\AppData\Roaming\Typora\typora-user-images\image-20191230092430638.png)

