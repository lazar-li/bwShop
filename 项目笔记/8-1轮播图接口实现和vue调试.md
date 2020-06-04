## goods.views

```python
class BannerViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    获取轮播图列表
    """
    queryset = Banner.objects.all().order_by("index")
    serializer_class = BannerSerializer
```

## goods.serializers

```python
class BannerSerializer(serializers.ModelSerializer):

    class Meta:
        model = Banner
        fields = "__all__"
```

## urls

```python
# 轮播图
router.register(r'banners', BannerViewSet, basename="banners")
```

## 前端api.js

```javascript
//获取轮播图
export const bannerGoods = params => { return axios.get(`${local_host}/banners/`) }
```

